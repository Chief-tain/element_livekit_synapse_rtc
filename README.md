# friends-in-matrix

Find and replace in the repository

1 api_keys
```
LZDWiXVQb0PrA5ljlTbj
N0lwByxvfTmIMSopO1aO

strong_password_here_12345
```
2 ip
```
5.129.230.96 - Replace the IP with your own
```

3 domains
```
syn.private-messages.space - synapse matrix server
chat.private-messages.space - matrix chat server
mas.private-messages.space - auth server
jwt.private-messages.space - jwt server
madmim.private-messages.space - admin server
```

## Disclaimer: This setup is not ideal."

1) ### Uncomment the certbot section in docker-compose.yml 
2) ### Temporarily enable the port 80 block in nginx.conf and disable the 443 blocks. Certbot requires port 80; the certificate paths used in the 443 blocks do not exist yet.
3) ### Create certificates with certbot
```bash 
    docker-compose run --rm certbot certonly --webroot -w /var/www/certbot  -d private-messages.space
    -d chat.private-messages.space -d mas.private-messages.space -d madmim.private-messages.space 
    -d jwt.private-messages.space --email email@gmail.com --agree-tos --non-interactive --expand
```
4) ### Re-enable the 443 sections in nginx.conf and point them to the obtained certificates.
5) ### I prefer to comment certbot section in docker-compose.yml
6) ### docker-compose up -d
7) ### create database for mas
```bash
docker-compose exec -it postgres bash
createdb mas -U synapse
```
8) #### create first admin user
```bash
docker exec -it matrix-mas mas-cli manage register-user
```

### Iptables
#### LiveKit
```
sudo iptables -A INPUT -p tcp --dport 7880 -j ACCEPT
sudo iptables -A INPUT -p udp --dport 7880 -j ACCEPT
sudo iptables -A INPUT -p tcp --dport 7881 -j ACCEPT
sudo iptables -A INPUT -p udp --dport 7881 -j ACCEPT
sudo iptables -A INPUT -p udp --match multiport --dports 50000:50020 -j ACCEPT
```
####  Coturn
```
sudo iptables -A INPUT -p udp --dport 3478 -j ACCEPT
sudo iptables -A INPUT -p tcp --dport 3478 -j ACCEPT
sudo iptables -A INPUT -p tcp --dport 5349 -j ACCEPT
sudo iptables -A INPUT -p udp --match multiport --dports 49152:49201 -j ACCEPT
```
####  Nginx/Matrix
```sudo iptables -A INPUT -p tcp --dport 80 -j ACCEPT
sudo iptables -A INPUT -p tcp --dport 443 -j ACCEPT
sudo iptables -A INPUT -p tcp --dport 8448 -j ACCEPT

sudo service netfilter-persistent save
```

### Perms for data-media (all)
```
sudo apt update
sudo apt install acl -y
sudo setfacl -R -m g::rwx ./synapse-data
sudo setfacl -R -d -m g::rwx ./synapse-data
getfacl ./synapse-data
```

#### generate config with secrets for mas and replace secrets section
```bash
docker run ghcr.io/element-hq/matrix-authentication-service config generate > config.yaml
```
### Create admin user
```bash
docker exec -it matrix-mas mas-cli manage register-user
```
other users U can create in madmim.private-messages.space


### delete containers and database
```
docker-compose rm -sf
docker volume ls
docker volume rm <your_folder_name>_postgres-data
```