# friends-in-matrix

Find and replace in the repository

1 api_keys
```
111ea560574baaabbbccc9cf2f4d3111
11122233359c0d83f836055e267255674e39f9237dd22fcc5ed444f333222111

strong_password_here_12345
```
2 ip
```
44.333.222.111 - Replace the IP with your own
```

3 domains
```
syn.mydomain.com - synapse matrix server
chat.mydomain.com - matrix chat server
mas.mydomain.com - auth server
jwt.mydomain.com - jwt server
madmim.mydomain.com - admin server
```

## Disclaimer: This setup is not ideal."

1) ### Uncomment the certbot section in docker-compose.yml 
2) ### Temporarily enable the port 80 block in nginx.conf and disable the 443 blocks. Certbot requires port 80; the certificate paths used in the 443 blocks do not exist yet.
3) ### Create certificates with certbot
```bash 
    docker-compose run --rm certbot certonly --webroot -w /var/www/certbot  -d mydomain.com
    -d chat.mydomain.com -d mas.mydomain.com -d madmim.mydomain.com 
    -d jwt.mydomain.com --email email@gmail.com --agree-tos --non-interactive --expand
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
other users U can create in madmim.mydomain.com


### delete containers and database
```
docker-compose rm -sf
docker volume ls
docker volume rm <your_folder_name>_postgres-data
```