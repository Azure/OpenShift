#Custom CA

How to generate wildcard cert for *.apps.<cluster>.<yourdomain.xyx>

https://medium.com/@saurabh6790/generate-wildcard-ssl-certificate-using-lets-encrypt-certbot-273e432794d7

This will generate PEM files, convert the cert to a crt file:

openssl x509 -outform der -in cert.pem -out cert.crt

Follow the instructions here:

https://docs.openshift.com/container-platform/4.3/authentication/certificates/replacing-default-ingress-certificate.html

Now do the same for the API Server:

https://docs.openshift.com/container-platform/4.3/authentication/certificates/api-server.html