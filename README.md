# genssl - Модифицированный образ acme.sh c Московской TimeZone и редактором nano.

***
Получить можно командой ``docker pull furriouswarrior/genssl``

или собрать самому `` docker build -t genssl . `` из корня папки.
***


### 1. Быстрый запуск  acme.sh as в режиме демона, с автоматическим проблемнием сертификатов через крон.
```
   docker run --rm  -itd  \
  -v /etc/acme:/acme.sh  \ 
  --net=host \
  --name=genssl \
  furriouswarrior/genssl daemon
 ``` 

При данном режиме запуска файлы контейнера будут в папке /etc/acme хоста!
  
### 2. Основные acme.sh команды.
Вывести все команды
`` docker  exec  genssl --help 
``

Получение сертификата стандартным способом, будет задействован 80 порт, данный вариант не особо интересен когда можно использовать api регистраторов или dns сервисов по типу cloudflare.
`` acme.sh --issue --standalone -d example.com -d www.example.com -d cp.example.com
``


### 3. Зайти  в контейнер можно командой
`` docker  exec -it genssl /bin/ash
``

Если хотим изменить ключ cloudflare
``nano /root/.acme.sh/dnsapi/dns_cf.sh
``

Меняем на свой api ключ и почту
``
CF_Key="dfdfdfdfdfdfdfdfdf"
CF_Email="email@gmail.com" 
`` 


### 4. Получение WildCard сертификатов
RSA 2048 key

``docker  exec  genssl   --issue -d example.com  -d '*.example.com'  --accountemail "email@gmail.com" --dns dns_cf
``

ECC 256 key
``docker  exec  genssl   --issue -d example.com  -d '*.example.com'  --accountemail "email@gmail.com" --dns dns_cf --keylength ec-256
``
``docker  exec  genssl  --issue -d example.com  -d '*.example.com'  --accountemail "email@gmail.com" --dns dns_cf --keylength ec-256
``

--dns dns_cf использовать api cloudflare

Так же мы можем при 1 выдаче явно указать переменные cloudflare. <br>
`` docker  exec  genssl  -e CF_Key="dfdfdfdfdfdfdfdfdf" -e CF_Email="email@gmail.com" --issue -d example.com  -d '*.example.com'  --accountemail "email@gmail.com" --dns dns_cf --keylength ec-256
``


### 5. В NGINX указываем пути до секртификатов. Далее nginx -t и nginx -s reload
```
        # RSA certificates
        ssl_certificate /etc/acme/example.com/fullchain.cer;
        ssl_certificate_key /etc/acme/example.com/example.com.key;
       #ssl_trusted_certificate ;

        # ECDSA certificates
        ssl_certificate /etc/acme/example.com_ecc/fullchain.cer;
        ssl_certificate_key /etc/acme/example.com_ecc/example.com.key;
       #ssl_trusted_certificate ;
 ```
