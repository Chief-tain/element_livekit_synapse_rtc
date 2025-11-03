# friends-in-matrix

Grep and replace on repository

1 api_keys
```
111ea560574baaabbbccc9cf2f4d3111
11122233359c0d83f836055e267255674e39f9237dd22fcc5ed444f333222111

strong_password_here_12345
```
2 ip
```
44.333.222.111 - change ip in yours. 
```

3 domains
```
mydomain.com - matrix server(domain)
chat.mydomain.com - matrix chat server(domain)
livekit.mydomain.com - livekit server(domain)
jwt.mydomain.com - jwt server(domain)
```

## Disclaimer: It's not ideal. It works, but it's have problems, i think in default.conf: Excessive parts of nginx.conf and docker-compose.yml..

1) ### uncomment certbot section in docker-compose.yml 
2) ### uncomment section with port 80 and comment others in nginx.conf
    Because of certbot we need to use port 80. In 443 sections we use paths to certs, that at that moment doesn't exist.
3) ### create by certbot certs. 
    ```docker-compose run --rm certbot certonly --webroot -w /var/www/certbot  -d mydomain.com -d chat.mydomain.com -d call.mydomain.com -d livekit.mydomain.com -d jwt.mydomain.com --email email@gmail.com --agree-tos --non-interactive --expand```
4) ### uncomment sections with 443 in nginx.conf
5) ### I prefer to comment certbot in docker-compose.yml 
6) ### docker-compose up -d


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
