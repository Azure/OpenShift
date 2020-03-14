# Configure htpasswd (non-AAD) access to ARO 4.3
# IN DEVELOPMENT

Below are the steps to configure a local htpasswd based authentication provider for ARO 4.3.

Usernames are passwords are stored in a secrets file and are accessed by a configured authentication provider.

# create auth provider

oc login --token=27ZljNlnEc8M8ORlssqCjyXSbEeAQfoZ3cIrBLGuG_0 --server=https://api.b7z01vg8.eastus.aroapp.io:6443
oc get oauth cluster -o yaml > aro-oauth.yaml
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

# update auth provider

oc replace -f ./aro-oauth.yaml 

# create initial user database

htpasswd -c -B aro-users.db admin (add admin user)
htpasswd -B aro-users.db user1 (add a non-privileged user)
htpasswd -B aro-users.db user2 (add another non-privileged user)
cat aro-users.db

# create initial user db secret file

oc create secret generic htp-secret --from-file ./aro-users.db -n openshift-config
oc adm policy add-cluster-role-to-user cluster-admin admin
Warning: User 'admin' not found can be ignored
oc login -u admin

# verify logged in as admin
oc get users
oc get identity
oc whoami

# delete the kubeadmin default user
oc delete secrets kubeadmin -n kube-system

# add new user when user db secret file

oc extract secret/htp-secret -n openshift-config --to - > aro-users.db
htpasswd -B aro-users-db extrauser
oc create secret generic htp-secret --from-file htpasswd=./aro-users.db --dry-run -o yaml | oc replace -n openshift-config -f -

# set / change user's full name

oc get identity aro-users:admin -o yaml


You will need to modify the DNS Operator in ARO 4.3 in order for your application pods running inside the ARO 4.3 cluster to resolve names hosted on a private DNS server outside the cluster.  These steps are documented for OpenShift 4.3 [here](https://docs.openshift.com/container-platform/4.3/networking/dns-operator.html).

For example, if you want to forward all DNS requests for *.example.com to be resolved by a DNS server 192.168.100.10, you can edit the operator configuration by running:
 
```bash
oc edit dns.operator/default
```
 
This will launch an editor and you can replace `spec: {}` with:
 
 ```yaml
spec:
  servers:
  - forwardPlugin:
      upstreams:
      - 192.168.100.10
    name: example-dns
    zones:
    - example.com
```

Save the file and exit your editor.
