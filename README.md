# TLS configuration

При виконанні даної лабораторної роботи я використовувала додаток, який був розроблений уході виконання 5-6 лабораторних робіт:
1. Сервер: https://github.com/ouroboroser/crypto_lab_56
2. Клієнт: https://github.com/ouroboroser/crypto_lab5-6-client

Обидві частині застосунка я деплоїла з використанням GCP. Для деплою серверу я використосувала звичайну віртуальну машину. Для клієнтської частині я використовувала Cloud Run оскільки мала лише один сертифікат, але за аналогією до деплою серверної частини можна зроботи теж саме для клієнта.

Для налаштування TLS для сервера я використовувала nginx з наступною конфігурацією:

```
events {
  worker_connections  1024;
}

http {
  server {
    listen 443 ssl;

    ssl_certificate /home/evgeniyagorshchyk/certificates/studqueue.ninja.crt;
    ssl_certificate_key /home/evgeniyagorshchyk/certificates/studqueue.ninja.key;

    server_name studqueue.ninja;

    ssl_protocols TLSv1.2 TLSv1.3;
    ssl_prefer_server_ciphers on;

    access_log /var/log/nginx/nginx.vhost.access.log;
    error_log /var/log/nginx/nginx.vhost.error.log;

    gzip on;

    location / {
      proxy_pass http://localhost:3001;
    }
  }
}
```
Для налаштування TLS для клієнтської частини я використовувала сервіс Cloud Run, для цього потрібно:
1. Зробити докер імедж
2. Налаштувати nginx

Далі використовуючи cli gcloud запушити відповідний імедж до Google Container Registry:
```
gcloud builds submit --tag gcr.io/<project id>/<image name> .
```
В меню Cloud Run обрати потрібний імедж