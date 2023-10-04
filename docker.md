## Running keycloak as a Docker container

### Build a container image using the `Dockerfile` provided. 

```
docker build -t keycloak:22.0.3 .
```

```
docker images
```

### Generate certificate.pem and key.pem
```
openssl req -newkey rsa:2048 -nodes -keyout key.pem -subj "/C=US/ST=VA/L=Ashburn/O=VMware/OU=Tanzu/CN=bastion0.navlab.io" -addext 'subjectAltName = DNS:bastion0.navlab.io, DNS:*.navlab.io, IP:192.168.10.138' -x509 -days 365 -out certificate.pem
```

### Execute the Docker container
```
docker run -d --name mykeycloak \
 -p 8443:8443 \
 -e KEYCLOAK_ADMIN=admin \
 -e KEYCLOAK_ADMIN_PASSWORD=change_me \
 -e KC_HOSTNAME=$(hostname --fqdn) \
 -e KC_HTTPS_CERTIFICATE_FILE=/opt/keycloak/conf/certificate.pem \
 -e KC_HTTPS_CERTIFICATE_KEY_FILE=/opt/keycloak/conf/key.pem \
 -v ${PWD}/certificate.pem:/opt/keycloak/conf/certificate.pem \
 -v ${PWD}/key.pem:/opt/keycloak/conf/key.pem \
keycloak:22.0.3 start --optimized
```

Access the application at - https://hostname:8443 

or 

use the `docker-compose.yaml` to run as  a detached option. 

```
docker-compose up -d
```
