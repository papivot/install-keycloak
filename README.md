# install-keycloak

```bash
# Setup up pre-req
sudo apt install openjdk-19-jdk
java --version
sudo groupadd keycloak
sudo useradd -r -g keycloak -d /opt/keycloak -s /sbin/nologin keycloak
```

```bash
# Install product
sudo wget https://github.com/keycloak/keycloak/releases/download/22.0.1/keycloak-22.0.1.zip -O /opt/keycloak-22.0.1.zip
sudo unzip /opt/keycloak-22.0.1.zip -d /opt
sudo mv /opt/keycloak-22.0.1 /opt/keycloak
sudo chmod o+x /opt/keycloak/bin
sudo chown -R keycloak /opt/keycloak
```

```bash
# Configure keycloak
sudo -u keycloak openssl req -newkey rsa:2048 -nodes -keyout /opt/keycloak/conf/key.pem -subj "/C=US/ST=VA/L=Ashburn/O=VMware/OU=Tanzu/CN=ubuntu-nv-03.env1.lab.test" -addext 'subjectAltName = DNS:ubuntu-nv-03.env1.lab.test, DNS:*.env1.lab.test, IP:10.197.107.63, IP:192.168.100.1' -x509 -days 365 -out /opt/keycloak/conf/certificate.pem
sudo -u keycloak vi /opt/keycloak/conf/keycloak.conf
sudo -u keycloak /opt/keycloak/bin/kc.sh build
sudo -u keycloak /opt/keycloak/bin/kc.sh show-config
```

```bash
# Setup up systemd svc
cat << EOF > /tmp/keycloak.service
[Unit]
Description=Keycloak Application Server
After=syslog.target network.target

[Service]
Type=idle
User=keycloak
Group=keycloak
RemainAfterExit=yes
LimitNOFILE=102642
ExecStart=/opt/keycloak/bin/kc.sh start --optimized
StandardOutput=null

[Install]
WantedBy=multi-user.target
EOF

sudo mv /tmp/keycloak.service  /etc/systemd/system/keycloak.service 
sudo chown root:root /etc/systemd/system/keycloak.service
sudo systemctl daemon-reload
sudo systemctl enable keycloak
sudo systemctl start keycloak
sudo systemctl status keycloak
```
