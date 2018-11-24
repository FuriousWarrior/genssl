# genssl


docker pull furriouswarrior/genssl


1. Running acme.sh as a docker daemon, so that it can handle the renewal cronjob automatically.
docker run --rm  -itd  \
-v /etc/acme:/acme.sh  \
  --net=host \
  --name=genssl \
  furriouswarrior/genssl daemon
  
  2. Then you can just use docker exec to execute any acme.sh commands.
docker  exec  genssl --help
docker  exec  genssl  --issue -d example.com  -d '*.example.com'  --accountemail "email@gmail.com" --dns dns_cf --keylength ec-384

ECC SSL
docker  exec  genssl  -e CF_Key="dfdfdfdfdfdfdfdfdf" -e CF_Email="email@gmail.com" --issue -d example.com  -d '*.example.com'  --accountemail "email@gmail.com" --dns dns_cf --keylength ec-384

docker  exec -it genssl /bin/ash
nano /root/.acme.sh/dnsapi/dns_cf.sh

CF_Key="dfdfdfdfdfdfdfdfdf"
CF_Email="email@gmail.com" 

RSA 2048 key
docker  exec  genssl   --issue -d example.com  -d '*.example.com'  --accountemail "email@gmail.com" --dns dns_cf
ECC 384 key
docker  exec  genssl   --issue -d example.com  -d '*.example.com'  --accountemail "email@gmail.com" --dns dns_cf --keylength ec-384

FOR NGINX
        # RSA certificates
        ssl_certificate /etc/acme/example.com/fullchain.cer;
        ssl_certificate_key /etc/acme/example.com/example.com.key;
#       ssl_trusted_certificate ;

        # ECDSA certificates
        ssl_certificate /etc/acme/example.com_ecc/fullchain.cer;
        ssl_certificate_key /etc/acme/example.com_ecc/example.com.key;
#        ssl_trusted_certificate ;
