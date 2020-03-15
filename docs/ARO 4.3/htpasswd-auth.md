# Configure htpasswd (non-AAD) access to ARO 4.3
# IN DEVELOPMENT

Below are the steps to configure a local htpasswd based authentication provider for ARO 4.3. This is useful in proofs-of-concepts to enable multi-user authentication / RBAC / limits without utilizing Azure Active Directory.

Usernames are passwords are stored in a secrets file and are accessed by a configured authentication provider within OpenShift. The steps below outline the process for creating an encrypted secrets file, configuration of the OpenShift authentication provider and subsequent removal of the default kubeadmin user.

### Login to the OpenShift CLI
```bash
oc login --token=<copied-token-hash> --server=https://api.b7z01vg8.eastus.aroapp.io:6443
```

### Create the encrypted user database and add multiple users
```bash
htpasswd -c -B aro-users.db admin
htpasswd -B aro-users.db user1
htpasswd -B aro-users.db user2
cat aro-users.db
```

### Create initial OpenShift secret file and administrative account
```bash
oc create secret generic htp-secret --from-file ./aro-users.db -n openshift-config
oc adm policy add-cluster-role-to-user cluster-admin admin
```

### Obtain current OpenShift authentication provider
```bash
oc get oauth cluster -o yaml > aro-oauth.yaml
```

### Prepare htpasswd authentication provider
```bash
sed -i '$d' aro-oauth.yaml
cat <<EOF >> aro-oauth.yaml
spec:
  identityProviders:
  - htpasswd:
      fileData:
        name: htp-secret
    mappingMethod: claim
    name: aro-users
    type: HTPasswd
EOF
```

### Update OpenShift with htpasswd authentication provider
```bash
oc replace -f ./aro-oauth.yaml 
oc create secret generic htp-secret --from-file htpasswd=./aro-users.db --dry-run -o yaml | oc replace -n openshift-config -f -
```

### Login as Administrator & Verify account
```bash
oc login -u admin
oc get users
oc get identity
oc whoami
```

### Delete the default kubeadmin virtual user
```bash
oc delete secrets kubeadmin -n kube-system
```

### Adding a new/subsquent htpasswd users when an existing secrets file exists
```bash
oc extract secret/htp-secret -n openshift-config --to - > aro-users.db
htpasswd -B aro-users.db extrauser
oc create secret generic htp-secret --from-file htpasswd=./aro-users.db --dry-run -o yaml | oc replace -n openshift-config -f -
```

### Configure user's full name
```bash
oc get user admin -o yaml > adminfullname.yaml
echo "fullName: 'Administrative User'" >> adminfullname.yaml
oc replace -f adminfullname.yaml
```
