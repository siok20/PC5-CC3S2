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