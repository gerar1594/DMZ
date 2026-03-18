# DMZ

# Distribución

Tenemos tres redes

* Red interna con una maquina con ip 192.168.1.10
* Red DMZ con un servidor web con ip 192.168.2.10
* Red Externa con una máquina. con ip 192.168.3.10

# Objetivo

Tenemos que crear varias ACLs para cumplir varios objetivos.
Los objetivos son:

* Desde la red externa tenga acceso al servidor web pero no pueda acceder a más puertos como ping.
* Desde la DMZ no tenga acceso a la red interna pero la red interna sí que tenga acceso al servidor web pero a más protocolos.

![][image1]

## Desarrollo de las ACLs

![][image2]

## Creamos la primera ACL sin nombre.
```cisco
Router\_FW\> enable
Router\_FW\# configure terminal
```
### Añadimos reglas a la ACL

*Router\_FW(config)\# access-list 101 permit tcp any host 192.168.3.1 eq 80*

* Permitimos todo el acceso que se produzca a la ip 192.168.3.1 que sea por el puerto 80

### Añadimos la interfaz de la red externa en la regla ACL

*Router\_FW(config)\# interface GigabitEthernet0/2*
*Router\_FW(config-if)\# ip access-group 101 in*
*Router\_FW(config-if)\# exit*

## Creamos la segunda regla ACL pero con nombre.

*Router\_FW\# configure terminal*
*Router\_FW(config)\# ip access-list extended DMZ\_TO\_LAN*

### Añadimos reglas a la ACL

*Router\_FW(config-ext-nacl)\# 5 permit tcp 192.168.2.0 0.0.0.255 eq 80 192.168.1.0 0.0.0.255 established*
*Router\_FW(config-ext-nacl)\# deny ip 192.168.2.0 0.0.0.255 192.168.1.0 0.0.0.255*
*Router\_FW(config-ext-nacl)\# permit ip any any*
*Router\_FW(config-ext-nacl)\# exit*

* Permitimos paquetes que pertenezcan a una conexión establecida por la PC interna.
* Bloqueamos todos los paquetes que se envíen desde la red 192.168.2.0 a la red 192.168.1.0
* Permite el resto de peticiones

### Añadimos la interfaz del router de la red DMZ a la regla ACL

*Router\_FW(config)\# interface GigabitEthernet0/1*
*Router\_FW(config-if)\# ip access-group DMZ\_TO\_LAN in*
*Router\_FW(config-if)\# exit*
