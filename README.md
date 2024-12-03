# PC5 - CC3S2

Estructura inicial del proyecto 

```
.
├── ansible
├── docs
├── handlers
├── README.md
├── site.yml
└── templates
```

## Ejercicio1 - Configuraciòn bàsica del sistema
Inicializar un `Vangrantfile` con la configraciòn bàsica de una VM con Ubuntu 20.04
`vagrant init ubuntu/focal64`

Levantar la VM con 
`vagrant up`

Tarea configurar zona horaria local
```
- name: Configurar la zona horaria y locales
      become: true
      comunity.general.timezone:
        name: America/Lima
```

Resultado en la VM: 
```bash
vagrant@ubuntu-focal:~$ timedatectl
               Local time: Mon 2024-12-02 14:01:12 -05
           Universal time: Mon 2024-12-02 19:01:12 UTC
                 RTC time: Mon 2024-12-02 19:01:12    
                Time zone: America/Lima (-05, -0500)  
System clock synchronized: no                         
              NTP service: inactive                   
          RTC in local TZ: no 
```

Ver los usuarios y grupos en la VM

`less /etc/group`

```
admin:x:117:devuser
netdev:x:118:ubuntu
lxd:x:119:ubuntu
vboxsf:x:120:
vagrant:x:1000:
systemd-coredump:x:999:
ubuntu:x:1001:
devuser:x:1002:
(END)
```

## Ejercicio2
Instalar Nginx

```
- name: Instalar Nginx
  apt:
    name: nginx
    update_cache: yes
    state: present
```

Comprobar dentro de la VM

```bash
vagrant@ubuntu-focal:~$ nginx -v
nginx version: nginx/1.18.0 (Ubuntu)

vagrant@ubuntu-focal:~$ sudo systemctl status nginx
● nginx.service - A high performance web server and a reverse proxy server
     Loaded: loaded (/lib/systemd/system/nginx.service; enabled; vendor preset:>
     Active: active (running) since Tue 2024-12-03 10:50:25 -05; 9min ago
       Docs: man:nginx(8)
    Process: 4241 ExecStartPre=/usr/sbin/nginx -t -q -g daemon on; master_proce>
    Process: 4242 ExecStart=/usr/sbin/nginx -g daemon on; master_process on; (c>
   Main PID: 4243 (nginx)
      Tasks: 3 (limit: 2324)
     Memory: 3.4M
     CGroup: /system.slice/nginx.service
             ├─4243 nginx: master process /usr/sbin/nginx -g daemon on; master_>
             ├─4244 nginx: worker process
             └─4245 nginx: worker process

Dec 03 10:50:25 ubuntu-focal systemd[1]: Starting A high performance web server>
Dec 03 10:50:25 ubuntu-focal systemd[1]: Started A high performance web server >

vagrant@ubuntu-focal:~$ sudo systemctl is-enabled nginx
enabled

vagrant@ubuntu-focal:~$ sudo netstat -tuln | grep '80\|443'
tcp        0      0 0.0.0.0:443             0.0.0.0:*               LISTEN     
tcp        0      0 0.0.0.0:80              0.0.0.0:*               LISTEN 
```

Verificar las claves privas y certificao SSL

```bash
vagrant@ubuntu-focal:~$ ls -l /etc/nginx/ssl/
total 12
-rw-r--r-- 1 root root 1192 Dec  3 10:50 nginx-key.crt
-rw-r--r-- 1 root root 1005 Dec  3 10:50 nginx-key.csr
-rw------- 1 root root 1675 Dec  3 10:50 nginx-key.key
```

Comprobar UFW

```bash
vagrant@ubuntu-focal:~$ sudo ufw status
Status: active

To                         Action      From
--                         ------      ----
22/tcp                     ALLOW       Anywhere                  
Nginx Full                 ALLOW       Anywhere                  
22/tcp (v6)                ALLOW       Anywhere (v6)             
Nginx Full (v6)            ALLOW       Anywhere (v6)             

vagrant@ubuntu-focal:~$ sudo ufw status verbose
Status: active
Logging: on (low)
Default: deny (incoming), allow (outgoing), disabled (routed)
New profiles: skip

To                         Action      From
--                         ------      ----
22/tcp                     ALLOW IN    Anywhere                  
80,443/tcp (Nginx Full)    ALLOW IN    Anywhere                  
22/tcp (v6)                ALLOW IN    Anywhere (v6)             
80,443/tcp (Nginx Full (v6)) ALLOW IN    Anywhere (v6)
```

Resultado del dominio 

```bash
vagrant@ubuntu-focal:~$ curl https://localhost
curl: (60) SSL certificate problem: self signed certificate
More details here: https://curl.haxx.se/docs/sslcerts.html

curl failed to verify the legitimacy of the server and therefore could not
establish a secure connection to it. To learn more about this situation and
how to fix it, please visit the web page mentioned above.
vagrant@ubuntu-focal:~$ curl https://localhost --insecure
<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>
<style>
    body {
        width: 35em;
        margin: 0 auto;
        font-family: Tahoma, Verdana, Arial, sans-serif;
    }
</style>
</head>
<body>
<h1>Welcome to nginx!</h1>
<p>If you see this page, the nginx web server is successfully installed and
working. Further configuration is required.</p>

<p>For online documentation and support please refer to
<a href="http://nginx.org/">nginx.org</a>.<br/>
Commercial support is available at
<a href="http://nginx.com/">nginx.com</a>.</p>

<p><em>Thank you for using nginx.</em></p>
</body>
</html>
```

## Ejercicio 3

greeting.service is running

```bash
● greeting.service - La aplicación de saludo altamente complicada
     Loaded: loaded (/etc/systemd/system/greeting.service; enabled; vendor preset: enabled)
     Active: active (running) since Tue 2024-12-03 16:26:05 -05; 50s ago
   Main PID: 5070 (gunicorn3)
      Tasks: 2 (limit: 2324)
     Memory: 31.0M
     CGroup: /system.slice/greeting.service
             ├─5070 /usr/bin/python3 /usr/bin/gunicorn3 --bind 0.0.0.0:5000 --access-logfile - --error-logfile - wsgi:app
             └─5089 /usr/bin/python3 /usr/bin/gunicorn3 --bind 0.0.0.0:5000 --access-logfile - --error-logfile - wsgi:app
```

```bash
vagrant@ubuntu-focal:~$ curl http://localhost:5000
<h1 style='color:green'>Greetings!</h1>
```