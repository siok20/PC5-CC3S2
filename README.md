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