# CONFIGURACIÓN DE LOS DISPOSITIVOS DE RED EN TODAS LAS SEDES

Trabajo Final de Redes y Comunicaciones de Datos

## ISP

### ROUTER (RTISP)

```kotlin
! CONFIGURACION SEGURIDAD BASICA
enable
configure terminal
hostname RTISP
enable secret ISP#20
line console 0
password ISP>20
login
exit
line vty 0 15
password ISP>20
login
exit
banner motd $ *** SOLO PERSONAL AUTORIZADO *** $

! ASIGNAR IP ROUTER - MLSWISP1
interface GigabitEthernet0/0/1
 ip address 200.0.0.1 255.255.255.0
!

! ASIGNAR IP DEL ROUTER ISP A LA SEDE LIMA
interface Serial0/1/0
 ip address 100.50.50.1 255.255.255.252
 no shutdown
!

ip route 0.0.0.0 0.0.0.0 100.50.50.2

username RTLIM1 secret Lim#20
interface s0/1/0
encapsulation ppp
ppp authentication chap
end


! GRABAR CONFIGURACION
write
write memory
copy running-config startup-config
```

### Multilayer Switch (MLSWISP1)

```kotlin
! CONFIGURACION SEGURIDAD BASICA
enable
configure terminal
hostname MLSWISP1
enable secret ISP#20
line console 0
password ISP>20
login
exit
line vty 0 15
password ISP>20
login
exit
banner motd $ *** SOLO PERSONAL AUTORIZADO *** $

interface Vlan1
 ip address 200.0.0.2 255.255.255.0
 no shutdown
!

interface range g1/0/11-12
 switchport access vlan 1
!

! GRABAR CONFIGURACION
write
write memory
copy running-config startup-config
```

## SEDE LIMA

### ROUTER (RTLIM1)

```kotlin
! CONFIGURACION SEGURIDAD BASICA
enable
configure terminal
hostname RTLIM1
enable secret Lim#20
line console 0
password Lim>20
login
exit
line vty 0 15
password Lim>20
login
exit
banner motd $ *** SOLO PERSONAL AUTORIZADO *** $

! ASIGNAR IP ROUTER - MLSLIM1
interface g0/1
ip address 172.21.17.137 255.255.255.252
ip ospf 100 area 0

! ASIGNAR IP ROUTER - MLSLIM2
interface g0/2
ip address 172.21.17.141 255.255.255.252
ip ospf 100 area 0


! ASIGNAR IP A ROUTERS DE OTRAS SEDES E ISP
interface Serial0/1/0
 ip address 100.50.50.2 255.255.255.252
 clock rate 2000000
!
interface Serial0/1/1
 ip address 172.22.254.5 255.255.255.252
 clock rate 2000000
!
interface Serial0/2/0
 ip address 172.22.254.17 255.255.255.252
 clock rate 2000000
!
interface Serial0/2/1
 ip address 172.22.254.13 255.255.255.252
 clock rate 2000000
!
interface Serial0/3/1
 ip address 172.22.254.9 255.255.255.252
 clock rate 2000000
end

! CONFIGURAR OSPF
configure terminal
router ospf 100
router-id 1.1.1.1
network 172.21.17.136 0.0.0.3 area 0
network 172.21.17.140 0.0.0.3 area 0
network 172.22.254.4 0.0.0.3 area 0
network 172.22.254.8 0.0.0.3 area 0
network 172.22.254.12 0.0.0.3 area 0
network 172.22.254.16 0.0.0.3 area 0
default-information originate
exit

ip route 0.0.0.0 0.0.0.0 100.50.50.1
end

! CONFIGURAR PPP-PAP
configure terminal
interface s0/1/1
encapsulation ppp
exit
username RTPIU1 secret Piu#20
interface s0/1/1
ppp authentication pap
ppp pap sent-username RTLIM1 password Lim#20
!
interface s0/3/1
encapsulation ppp
exit
username RTARE1 secret Are#20
interface s0/3/1
ppp authentication pap
ppp pap sent-username RTLIM1 password Lim#20
!
interface s0/2/1
encapsulation ppp
exit
username RTCUS1 secret Cus#20
interface s0/2/1
ppp authentication pap
ppp pap sent-username RTLIM1 password Lim#20
!
interface s0/2/0
encapsulation ppp
exit
username RTCAJ1 secret Caj#20
interface s0/2/0
ppp authentication pap
ppp pap sent-username RTLIM1 password Lim#20
!
exit
username RTISP secret ISP#20
interface s0/1/0
encapsulation ppp
ppp authentication chap
end

! GRABAR CONFIGURACION
write
write memory
copy running-config startup-config
```

### Multilayer Switch 1 (MLSLIM1)

```kotlin
! CONFIGURACION SEGURIDAD BASICA
enable
configure terminal
hostname MLSLIM1
enable secret Lim#20
line console 0
password Lim>20
login
exit
line vty 0 15
password Lim>20
login
exit
banner motd $ *** SOLO PERSONAL AUTORIZADO *** $

! Habilitar el enrutamiento capa 3
ip routing

! CREACION Y NOMBRAMIENTO DE VLANS
VLAN 110
name Administracion
VLAN 120
name Logistica
VLAN 130
name Finanzas
VLAN 140
name Marketing
VLAN 150
name Ventas
VLAN 160
name Servidores
VLAN 170
name WifiEjecutivo
VLAN 180
name WifiCliente
VLAN 99
name Management&Native
exit

! ASIGNACION DE ENLACE TRUNCAL CON SWITCHES DE ACCESO
interface range g1/0/11-17
switchport trunk native vlan 99
switchport mode trunk
switchport nonegotiate
ip ospf 100 area 0

! ASIGNAR IP MLS - ROUTER
interface g1/0/1
no switchport
ip address 172.21.17.138 255.255.255.252
ip ospf 100 area 0

! ASIGNAR IP MLS1 - MLS2
interface g1/0/2
no switchport
ip address 172.21.17.145 255.255.255.252
ip ospf 100 area 0

! DESHABILITAR VLAN1
interface Vlan1
no ip address
shutdown

! ASIGNAR IP DE LA MLS EN LAS VLANS
interface Vlan110
ip address 172.21.16.1 255.255.255.128
!
interface Vlan120
ip address 172.21.16.129 255.255.255.192
!
interface Vlan130
ip address 172.21.17.33  255.255.255.224
!
interface Vlan140
ip address 172.21.16.193 255.255.255.192
!
interface Vlan150
ip address 172.21.17.1 255.255.255.224
!
interface Vlan160
ip address 172.21.17.129 255.255.255.248
!
interface Vlan170
ip address 172.21.17.97 255.255.255.224
!
interface Vlan180
ip address 172.21.17.65 255.255.255.224
!
interface Vlan99
ip address 172.21.99.1 255.255.255.240
!
exit

! CREAR DHCP PARA CADA VLAN
ip dhcp pool POOL-VLAN110
network 172.21.16.0 255.255.255.128
default-router 172.21.16.1
dns-server 200.0.0.22
!
ip dhcp pool POOL-VLAN120
network 172.21.16.128 255.255.255.192
default-router 172.21.16.129
dns-server 200.0.0.22
!
ip dhcp pool POOL-VLAN130
network 172.21.17.32 255.255.255.224
default-router 172.21.17.33
dns-server 200.0.0.22
!
ip dhcp pool POOL-VLAN140
network 172.21.16.192 255.255.255.192
default-router 172.21.16.193
dns-server 200.0.0.22
!
ip dhcp pool POOL-VLAN150
network 172.21.17.0 255.255.255.224
default-router 172.21.17.1
dns-server 200.0.0.22
!
ip dhcp pool POOL-VLAN160
network 172.21.17.128 255.255.255.248
default-router 172.21.17.129
dns-server 200.0.0.22
!
ip dhcp pool POOL-VLAN170
network 172.21.17.96 255.255.255.224
default-router 172.21.17.97
dns-server 200.0.0.22
!
ip dhcp pool POOL-VLAN180
network 172.21.17.64 255.255.255.224
default-router 172.21.17.65
dns-server 200.0.0.22
end

! CONFIGURAR OSPF
configure terminal
router ospf 100
router-id 1.1.1.2
network 172.21.16.0 0.0.0.127 area 0
network 172.21.16.128 0.0.0.63 area 0
network 172.21.16.192 0.0.0.63 area 0
network 172.21.17.0 0.0.0.31 area 0
network 172.21.17.32 0.0.0.31 area 0
network 172.21.17.64 0.0.0.31 area 0
network 172.21.17.96 0.0.0.31 area 0
network 172.21.17.128 0.0.0.7 area 0
network 172.21.99.0 0.0.0.15 area 0
network 172.21.17.136 0.0.0.3 area 0
network 172.21.17.144 0.0.0.3 area 0
end

! CONFIGURAR ACL
configure terminal
ip access-list extended FILTRO-FTPWEBLIM
permit tcp 172.21.17.0 0.0.0.31 172.21.17.133 0.0.0.0 eq 20
permit tcp 172.21.17.0 0.0.0.31 172.21.17.133 0.0.0.0 eq 21
permit tcp 172.21.17.32 0.0.0.31 172.21.17.133 0.0.0.0 eq 20
permit tcp 172.21.17.32 0.0.0.31 172.21.17.133 0.0.0.0 eq 21
permit tcp 172.21.16.192 0.0.0.63 172.21.17.133 0.0.0.0 eq 20
permit tcp 172.21.16.192 0.0.0.63 172.21.17.133 0.0.0.0 eq 21
deny tcp 172.21.16.0 0.0.0.127 172.21.17.133 0.0.0.0 eq 20
deny tcp 172.21.16.0 0.0.0.127 172.21.17.133 0.0.0.0 eq 21
deny tcp 172.21.16.128 0.0.0.63 172.21.17.133 0.0.0.0 eq 20
deny tcp 172.21.16.128 0.0.0.63 172.21.17.133 0.0.0.0 eq 21
deny tcp 172.21.17.64 0.0.0.31 172.21.17.133 0.0.0.0 eq 20
deny tcp 172.21.17.64 0.0.0.31 172.21.17.133 0.0.0.0 eq 21
deny tcp 172.21.17.96 0.0.0.31 172.21.17.133 0.0.0.0 eq 20
deny tcp 172.21.17.96 0.0.0.31 172.21.17.133 0.0.0.0 eq 21
deny tcp 172.21.16.0 0.0.0.127 172.21.17.131 0.0.0.0 eq 80
deny tcp 172.21.16.0 0.0.0.127 172.21.17.131 0.0.0.0 eq 443
deny tcp 172.21.17.96 0.0.0.31 172.21.17.131 0.0.0.0 eq 80
deny tcp 172.21.17.96 0.0.0.31 172.21.17.131 0.0.0.0 eq 443
deny tcp 172.21.17.64 0.0.0.31 172.21.17.131 0.0.0.0 eq 80
deny tcp 172.21.17.64 0.0.0.31 172.21.17.131 0.0.0.0 eq 443
deny tcp 172.21.17.32 0.0.0.31 172.21.17.131 0.0.0.0 eq 80
deny tcp 172.21.17.32 0.0.0.31 172.21.17.131 0.0.0.0 eq 443
permit ip any any
exit
int vlan 110
ip access-group FILTRO-FTPWEBLIM in
int vlan 120
ip access-group FILTRO-FTPWEBLIM in
int vlan 130
ip access-group FILTRO-FTPWEBLIM in
int vlan 140
ip access-group FILTRO-FTPWEBLIM in
int vlan 150
ip access-group FILTRO-FTPWEBLIM in
int vlan 170
ip access-group FILTRO-FTPWEBLIM in
int vlan 180
ip access-group FILTRO-FTPWEBLIM in
end

! GRABAR CONFIGURACION
write
write memory
copy running-config startup-config
```

### Multilayer Switch 2 (MLSLIM2)

```kotlin
! CONFIGURACION SEGURIDAD BASICA
enable
configure terminal
hostname MLSLIM2
enable secret Lim#20
line console 0
password Lim>20
login
exit
line vty 0 15
password Lim>20
login
exit
banner motd $ *** SOLO PERSONAL AUTORIZADO *** $

! Habilitar el enrutamiento capa 3
ip routing

! CREACION Y NOMBRAMIENTO DE VLANS
VLAN 110
name Administracion
VLAN 120
name Logistica
VLAN 130
name Finanzas
VLAN 140
name Marketing
VLAN 150
name Ventas
VLAN 160
name Servidores
VLAN 170
name WifiEjecutivo
VLAN 180
name WifiCliente
VLAN 99
name Management&Native
exit

! ASIGNACION DE ENLACE TRUNCAL CON SWITCHES DE ACCESO
interface range g1/0/11-17
switchport trunk native vlan 99
switchport mode trunk
switchport nonegotiate

! ASIGNAR IP MLS - ROUTER
interface g1/0/1
no switchport
ip address 172.21.17.142 255.255.255.252
ip ospf 100 area 0

! ASIGNAR IP MLS2 - MLS1
interface g1/0/2
no switchport
ip address 172.21.17.146 255.255.255.252
ip ospf 100 area 0

! DESHABILITAR VLAN1
interface Vlan1
no ip address
shutdown

! ASIGNAR IP DE LA MLS EN LAS VLANS
interface Vlan110
ip address 172.21.16.2 255.255.255.128
!
interface Vlan120
ip address 172.21.16.130 255.255.255.192
!
interface Vlan130
ip address 172.21.17.34  255.255.255.224
!
interface Vlan140
ip address 172.21.16.194 255.255.255.192
!
interface Vlan150
ip address 172.21.17.2 255.255.255.224
!
interface Vlan160
ip address 172.21.17.130 255.255.255.248
!
interface Vlan170
ip address 172.21.17.98 255.255.255.224
!
interface Vlan180
ip address 172.21.17.66 255.255.255.224
!
interface Vlan99
ip address 172.21.99.2 255.255.255.240
!
exit

! CREAR DHCP PARA CADA VLAN
ip dhcp pool POOL-VLAN110
network 172.21.16.0 255.255.255.128
default-router 172.21.16.2
dns-server 200.0.0.22
!
ip dhcp pool POOL-VLAN120
network 172.21.16.128 255.255.255.192
default-router 172.21.16.130
dns-server 200.0.0.22
!
ip dhcp pool POOL-VLAN130
network 172.21.17.32 255.255.255.224
default-router 172.21.17.34
dns-server 200.0.0.22
!
ip dhcp pool POOL-VLAN140
network 172.21.16.192 255.255.255.192
default-router 172.21.16.194
dns-server 200.0.0.22
!
ip dhcp pool POOL-VLAN150
network 172.21.17.0 255.255.255.224
default-router 172.21.17.2
dns-server 200.0.0.22
!
ip dhcp pool POOL-VLAN160
network 172.21.17.128 255.255.255.248
default-router 172.21.17.130
dns-server 200.0.0.22
!
ip dhcp pool POOL-VLAN170
network 172.21.17.96 255.255.255.224
default-router 172.21.17.98
dns-server 200.0.0.22
!
ip dhcp pool POOL-VLAN180
network 172.21.17.64 255.255.255.224
default-router 172.21.17.66
dns-server 200.0.0.22
end

! CONFIGURAR OSPF
configure terminal
router ospf 100
router-id 1.1.1.3
network 172.21.16.0 0.0.0.127 area 0
network 172.21.16.128 0.0.0.63 area 0
network 172.21.16.192 0.0.0.63 area 0
network 172.21.17.0 0.0.0.31 area 0
network 172.21.17.32 0.0.0.31 area 0
network 172.21.17.64 0.0.0.31 area 0
network 172.21.17.96 0.0.0.31 area 0
network 172.21.17.128 0.0.0.7 area 0
network 172.21.99.0 0.0.0.15 area 0
network 172.21.17.140 0.0.0.3 area 0
network 172.21.17.144 0.0.0.3 area 0
end

! CONFIGURAR ACL
configure terminal
ip access-list extended FILTRO-FTPWEBLIM
permit tcp 172.21.17.0 0.0.0.31 172.21.17.133 0.0.0.0 eq 20
permit tcp 172.21.17.0 0.0.0.31 172.21.17.133 0.0.0.0 eq 21
permit tcp 172.21.17.32 0.0.0.31 172.21.17.133 0.0.0.0 eq 20
permit tcp 172.21.17.32 0.0.0.31 172.21.17.133 0.0.0.0 eq 21
permit tcp 172.21.16.192 0.0.0.63 172.21.17.133 0.0.0.0 eq 20
permit tcp 172.21.16.192 0.0.0.63 172.21.17.133 0.0.0.0 eq 21
deny tcp 172.21.16.0 0.0.0.127 172.21.17.133 0.0.0.0 eq 20
deny tcp 172.21.16.0 0.0.0.127 172.21.17.133 0.0.0.0 eq 21
deny tcp 172.21.16.128 0.0.0.63 172.21.17.133 0.0.0.0 eq 20
deny tcp 172.21.16.128 0.0.0.63 172.21.17.133 0.0.0.0 eq 21
deny tcp 172.21.17.64 0.0.0.31 172.21.17.133 0.0.0.0 eq 20
deny tcp 172.21.17.64 0.0.0.31 172.21.17.133 0.0.0.0 eq 21
deny tcp 172.21.17.96 0.0.0.31 172.21.17.133 0.0.0.0 eq 20
deny tcp 172.21.17.96 0.0.0.31 172.21.17.133 0.0.0.0 eq 21
deny tcp 172.21.16.0 0.0.0.127 172.21.17.131 0.0.0.0 eq 80
deny tcp 172.21.16.0 0.0.0.127 172.21.17.131 0.0.0.0 eq 443
deny tcp 172.21.17.96 0.0.0.31 172.21.17.131 0.0.0.0 eq 80
deny tcp 172.21.17.96 0.0.0.31 172.21.17.131 0.0.0.0 eq 443
deny tcp 172.21.17.64 0.0.0.31 172.21.17.131 0.0.0.0 eq 80
deny tcp 172.21.17.64 0.0.0.31 172.21.17.131 0.0.0.0 eq 443
deny tcp 172.21.17.32 0.0.0.31 172.21.17.131 0.0.0.0 eq 80
deny tcp 172.21.17.32 0.0.0.31 172.21.17.131 0.0.0.0 eq 443
permit ip any any
exit
int vlan 110
ip access-group FILTRO-FTPWEBLIM in
int vlan 120
ip access-group FILTRO-FTPWEBLIM in
int vlan 130
ip access-group FILTRO-FTPWEBLIM in
int vlan 140
ip access-group FILTRO-FTPWEBLIM in
int vlan 150
ip access-group FILTRO-FTPWEBLIM in
int vlan 170
ip access-group FILTRO-FTPWEBLIM in
int vlan 180
ip access-group FILTRO-FTPWEBLIM in
end

! GRABAR CONFIGURACION
write
write memory
copy running-config startup-config
```

### SWTICH DE ACCESO 1: ADMINISTRACION (SWLIM1)

```kotlin
! CONFIGURACION SEGURIDAD BASICA
enable
configure terminal
hostname SWLIM1
enable secret Lim#20
line console 0
password Lim>20
login
exit
line vty 0 15
password Lim>20
login
exit
banner motd $ *** SOLO PERSONAL AUTORIZADO *** $

! CREACION Y NOMBRAMIENTO DE VLANS
VLAN 110
name Administracion
VLAN 120
name Logistica
VLAN 130
name Finanzas
VLAN 140
name Marketing
VLAN 150
name Ventas
VLAN 160
name Servidores
VLAN 170
name WifiEjecutivo
VLAN 180
name WifiCliente
VLAN 99
name Management&Native
exit

! ASIGNACION DE VLAN A PCS
interface range f0/1-12
switchport mode access
switchport access vlan 110
!
interface range f0/13-24
switchport mode access
switchport access vlan 110
exit

! ASIGNACION DE ENLACE TRUNCAL CON LOS MLS
interface range g0/1-2
switchport mode trunk
switchport trunk native vlan 99

! DESHABILITAR VLAN 1
interface Vlan1
no ip address
shutdown

! ASIGNAR IP EN VLAN 99
interface Vlan99
ip address 172.21.99.3 255.255.255.240
exit
ip default-gateway 172.21.99.1
end

! GRABAR CONFIGURACION
write
write memory
copy running-config startup-config
```

### SWTICH DE ACCESO 2: LOGISTICA (SWLIM2)

```kotlin
! CONFIGURACION SEGURIDAD BASICA
enable
configure terminal
hostname SWLIM2
enable secret Lim#20
line console 0
password Lim>20
login
exit
line vty 0 15
password Lim>20
login
exit
banner motd $ *** SOLO PERSONAL AUTORIZADO *** $

! CREACION Y NOMBRAMIENTO DE VLANS
VLAN 110
name Administracion
VLAN 120
name Logistica
VLAN 130
name Finanzas
VLAN 140
name Marketing
VLAN 150
name Ventas
VLAN 160
name Servidores
VLAN 170
name WifiEjecutivo
VLAN 180
name WifiCliente
VLAN 99
name Management&Native
exit

! ASIGNACION DE VLAN A PCS
interface range f0/1-12
switchport mode access
switchport access vlan 120
!
interface range f0/13-24
switchport mode access
switchport access vlan 120
exit

! ASIGNACION DE ENLACE TRUNCAL CON LOS MLS
interface range g0/1-2
switchport mode trunk
switchport trunk native vlan 99

! DESHABILITAR VLAN 1
interface Vlan1
no ip address
shutdown

! ASIGNAR IP EN VLAN 99
interface Vlan99
ip address 172.21.99.4 255.255.255.240
exit
ip default-gateway 172.21.99.1
end

! GRABAR CONFIGURACION
write
write memory
copy running-config startup-config
```

### SWITCH DE ACCESO 3: FINANZAS (SWLIM3)

```kotlin
! CONFIGURACION SEGURIDAD BASICA
enable
configure terminal
hostname SWLIM3
enable secret Lim#20
line console 0
password Lim>20
login
exit
line vty 0 15
password Lim>20
login
exit
banner motd $ *** SOLO PERSONAL AUTORIZADO *** $

! CREACION Y NOMBRAMIENTO DE VLANS
VLAN 110
name Administracion
VLAN 120
name Logistica
VLAN 130
name Finanzas
VLAN 140
name Marketing
VLAN 150
name Ventas
VLAN 160
name Servidores
VLAN 170
name WifiEjecutivo
VLAN 180
name WifiCliente
VLAN 99
name Management&Native
exit

! ASIGNACION DE VLAN A PCS
interface range f0/1-12
switchport mode access
switchport access vlan 130
!
interface range f0/13-24
switchport mode access
switchport access vlan 130
exit

! ASIGNACION DE ENLACE TRUNCAL CON LOS MLS
interface range g0/1-2
switchport mode trunk
switchport trunk native vlan 99

! DESHABILITAR VLAN 1
interface Vlan1
no ip address
shutdown

! ASIGNAR IP EN VLAN 99
interface Vlan99
ip address 172.21.99.5 255.255.255.240
exit
ip default-gateway 172.21.99.1
end

! GRABAR CONFIGURACION
write
write memory
copy running-config startup-config
```

### SWITCH DE ACCESO 4: MARKETING (SWLIM4)

```kotlin
! CONFIGURACION SEGURIDAD BASICA
enable
configure terminal
hostname SWLIM4
enable secret Lim#20
line console 0
password Lim>20
login
exit
line vty 0 15
password Lim>20
login
exit
banner motd $ *** SOLO PERSONAL AUTORIZADO *** $

! CREACION Y NOMBRAMIENTO DE VLANS
VLAN 110
name Administracion
VLAN 120
name Logistica
VLAN 130
name Finanzas
VLAN 140
name Marketing
VLAN 150
name Ventas
VLAN 160
name Servidores
VLAN 170
name WifiEjecutivo
VLAN 180
name WifiCliente
VLAN 99
name Management&Native
exit

! ASIGNACION DE VLAN A PCS
interface range f0/1-12
switchport mode access
switchport access VLAN 140
!
interface range f0/13-24
switchport mode access
switchport access VLAN 140
exit

! ASIGNACION DE ENLACE TRUNCAL CON LOS MLS
interface range g0/1-2
switchport mode trunk
switchport trunk native vlan 99

! DESHABILITAR VLAN 1
interface Vlan1
no ip address
shutdown

! ASIGNAR IP EN VLAN 99
interface Vlan99
ip address 172.21.99.6 255.255.255.240
exit
ip default-gateway 172.21.99.1
end

! GRABAR CONFIGURACION
write
write memory
copy running-config startup-config
```

### SWITCH DE ACCESO 5: VENTAS (SWLIM5)

```kotlin
! CONFIGURACION SEGURIDAD BASICA
enable
configure terminal
hostname SWLIM5
enable secret Lim#20
line console 0
password Lim>20
login
exit
line vty 0 15
password Lim>20
login
exit
banner motd $ *** SOLO PERSONAL AUTORIZADO *** $

! CREACION Y NOMBRAMIENTO DE VLANS
VLAN 110
name Administracion
VLAN 120
name Logistica
VLAN 130
name Finanzas
VLAN 140
name Marketing
VLAN 150
name Ventas
VLAN 160
name Servidores
VLAN 170
name WifiEjecutivo
VLAN 180
name WifiCliente
VLAN 99
name Management&Native
exit

! ASIGNACION DE VLAN A PCS
interface range f0/1-12
switchport mode access
switchport access VLAN 150
!
interface range f0/13-24
switchport mode access
switchport access VLAN 150
exit

! ASIGNACION DE ENLACE TRUNCAL CON LOS MLS
interface range g0/1-2
switchport mode trunk
switchport trunk native vlan 99

! DESHABILITAR VLAN 1
interface Vlan1
no ip address
shutdown

! ASIGNAR IP EN VLAN 99
interface Vlan99
ip address 172.21.99.7 255.255.255.240
exit
ip default-gateway 172.21.99.1
end

! GRABAR CONFIGURACION
write
write memory
copy running-config startup-config
```

### SWITCH DE ACCESO 6: SERVIDORES (SWLIM6)

```kotlin
! CONFIGURACION SEGURIDAD BASICA
enable
configure terminal
hostname SWLIM6
enable secret Lim#20
line console 0
password Lim>20
login
exit
line vty 0 15
password Lim>20
login
exit
banner motd $ *** SOLO PERSONAL AUTORIZADO *** $

! CREACION Y NOMBRAMIENTO DE VLANS
VLAN 110
name Administracion
VLAN 120
name Logistica
VLAN 130
name Finanzas
VLAN 140
name Marketing
VLAN 150
name Ventas
VLAN 160
name Servidores
VLAN 170
name WifiEjecutivo
VLAN 180
name WifiCliente
VLAN 99
name Management&Native
exit

! ASIGNACION DE VLAN A PCS
interface range f0/1-12
switchport mode access
switchport access VLAN 160
!
interface range f0/13-24
switchport mode access
switchport access VLAN 160
exit

! ASIGNACION DE ENLACE TRUNCAL CON LOS MLS
interface range g0/1-2
switchport mode trunk
switchport trunk native vlan 99

! DESHABILITAR VLAN 1
interface Vlan1
no ip address
shutdown

! ASIGNAR IP EN VLAN 99
interface Vlan99
ip address 172.21.99.8 255.255.255.240
exit
ip default-gateway 172.21.99.1
end

! GRABAR CONFIGURACION
write
write memory
copy running-config startup-config
```

### SWITCH DE ACCESO 7: WIFI EJECUTIVO Y CLIENTE (SWLIM7)

```kotlin
! CONFIGURACION SEGURIDAD BASICA
enable
configure terminal
hostname SWLIM7
enable secret Lim#20
line console 0
password Lim>20
login
exit
line vty 0 15
password Lim>20
login
exit
banner motd $ *** SOLO PERSONAL AUTORIZADO *** $

! CREACION Y NOMBRAMIENTO DE VLANS
VLAN 110
name Administracion
VLAN 120
name Logistica
VLAN 130
name Finanzas
VLAN 140
name Marketing
VLAN 150
name Ventas
VLAN 160
name Servidores
VLAN 170
name WifiEjecutivo
VLAN 180
name WifiCliente
VLAN 99
name Management&Native
exit

! ASIGNACION DE VLAN A PCS
interface f0/1
switchport mode access
switchport access VLAN 180
!
interface f0/2
switchport mode access
switchport access VLAN 170
exit

! ASIGNACION DE ENLACE TRUNCAL CON LOS MLS
interface range g0/1-2
switchport mode trunk
switchport trunk native vlan 99

! DESHABILITAR VLAN 1
interface Vlan1
no ip address
shutdown

! ASIGNAR IP EN VLAN 99
interface Vlan99
ip address 172.21.99.9 255.255.255.240
exit
ip default-gateway 172.21.99.1
end

! GRABAR CONFIGURACION
write
write memory
copy running-config startup-config
```

## SEDE PIURA

### ROUTER (RTPIU1)

```kotlin
! CONFIGURACION SEGURIDAD BASICA
enable
configure terminal
hostname RTPIU1
enable secret Piu#20
line console 0
password Piu>20
login
exit
line vty 0 15
password Piu>20
login
exit
banner motd $ *** SOLO PERSONAL AUTORIZADO *** $

! ASIGNAR IP ROUTER - MLSPIU1
interface g0/0/1
ip address 172.21.33.89 255.255.255.252
ip ospf 100 area 0

! ASIGNAR IP A ROUTERS DE OTRAS SEDES E ISP
interface Serial0/1/1
 ip address 172.22.254.6 255.255.255.252
 clock rate 2000000
 ip ospf 100 area 0
end

! CONFIGURAR OSPF
configure terminal
router ospf 100
router-id 2.2.2.2
network 172.21.33.88 0.0.0.3 area 0
network 172.22.254.4 0.0.0.3 area 0
end

! CONFIGURAR PPP-PAP
configure terminal
interface s0/1/1
encapsulation ppp
exit
username RTLIM1 secret Lim#20
interface s0/1/1
ppp authentication pap
ppp pap sent-username RTPIU1 password Piu#20
end

! GRABAR CONFIGURACION
write
write memory
copy running-config startup-config
```

### MULTILAYER SWITCH (MLSPIU1)

```kotlin
! CONFIGURACION SEGURIDAD BASICA
enable
configure terminal
hostname MLSPIU1
enable secret Piu#20
line console 0
password Piu>20
login
exit
line vty 0 15
password Piu>20
login
exit
banner motd $ *** SOLO PERSONAL AUTORIZADO *** $

! Habilitar el enrutamiento capa 3
ip routing

! CREACION Y NOMBRAMIENTO DE VLANS
VLAN 210
name Administracion
VLAN 220
name Logistica
VLAN 230
name Finanzas
VLAN 240
name Marketing
VLAN 250
name Ventas
VLAN 260
name Servidores
VLAN 270
name WifiEjecutivo
VLAN 280
name WifiCliente
VLAN 99
name Management&Native
exit

! ASIGNACION DE ENLACE TRUNCAL CON SWITCHES DE ACCESO
interface range g1/0/11-17
switchport trunk native vlan 99
switchport mode trunk
switchport nonegotiate

! ASIGNAR IP MLS - ROUTER
interface g1/0/1
no switchport
ip address 172.21.33.90 255.255.255.252

! DESHABILITAR VLAN1
interface Vlan1
no ip address
shutdown

! ASIGNAR IP DE LA MLS EN LAS VLANS
interface VLAN210
ip address 172.21.32.1 255.255.255.128
!
interface VLAN220
ip address 172.21.32.129 255.255.255.192
!
interface VLAN230
ip address 172.21.33.1  255.255.255.224
!
interface VLAN240
ip address 172.21.32.193 255.255.255.224
!
interface VLAN250
ip address 172.21.32.225 255.255.255.224
!
interface VLAN260
ip address 172.21.33.81 255.255.255.248
!
interface VLAN270
ip address 172.21.33.65 255.255.255.240
!
interface VLAN280
ip address 172.21.33.33 255.255.255.224
!
interface Vlan99
ip address 172.21.99.17 255.255.255.240
!
exit

! CREAR DHCP PARA CADA VLAN
ip dhcp pool POOL-VLAN210
network 172.21.32.0 255.255.255.128
default-router 172.21.32.1
dns-server 200.0.0.22
!
ip dhcp pool POOL-VLAN220
network 172.21.32.128 255.255.255.192
default-router 172.21.32.129
dns-server 200.0.0.22
!
ip dhcp pool POOL-VLAN230
network 172.21.33.0  255.255.255.224
default-router 172.21.33.1
dns-server 200.0.0.22
!
ip dhcp pool POOL-VLAN240
network 172.21.32.192 255.255.255.224
default-router 172.21.32.193
dns-server 200.0.0.22
!
ip dhcp pool POOL-VLAN250
network 172.21.32.224 255.255.255.224
default-router 172.21.32.225
dns-server 200.0.0.22
!
ip dhcp pool POOL-VLAN260
network 172.21.33.80 255.255.255.248
default-router 172.21.33.81
dns-server 200.0.0.22
!
ip dhcp pool POOL-VLAN270
network 172.21.33.64 255.255.255.240
default-router 172.21.33.65
dns-server 200.0.0.22
!
ip dhcp pool POOL-VLAN280
network 172.21.33.32 255.255.255.224
default-router 172.21.33.33
dns-server 200.0.0.22
end

! CONFIGURAR OSPF
configure terminal
router ospf 100
router-id 2.2.2.3
network 172.21.32.0 0.0.0.127 area 0
network 172.21.32.128 0.0.0.63 area 0
network 172.21.32.192 0.0.0.31 area 0
network 172.21.32.224 0.0.0.31 area 0
network 172.21.33.0 0.0.0.31 area 0
network 172.21.33.32 0.0.0.31 area 0
network 172.21.33.64 0.0.0.15 area 0
network 172.21.33.80 0.0.0.7 area 0
network 172.21.99.16 0.0.0.15 area 0
network 172.21.33.88 0.0.0.3 area 0
end

! CONFIGURAR ACL
configure terminal
IP ACCESS-LIST Extended FILTRO-FTPWEBPIU
PERMIT TCP 172.21.32.0 0.0.0.127 172.21.33.84 0.0.0.0 eq 20
PERMIT TCP 172.21.32.0 0.0.0.127 172.21.33.84 0.0.0.0 eq 21
PERMIT TCP 172.21.33.0 0.0.0.31 172.21.33.84 0.0.0.0 eq 20
PERMIT TCP 172.21.33.0 0.0.0.31 172.21.33.84 0.0.0.0 eq 21
PERMIT TCP 172.21.32.224 0.0.0.31 172.21.33.84 0.0.0.0 eq 20
PERMIT TCP 172.21.32.224 0.0.0.31 172.21.33.84 0.0.0.0 eq 21
DENY TCP 172.21.32.128 0.0.0.63 172.21.33.84 0.0.0.0 eq 20
DENY TCP 172.21.32.128 0.0.0.63 172.21.33.84 0.0.0.0 eq 21
DENY TCP 172.21.32.192 0.0.0.31 172.21.33.84 0.0.0.0 eq 20
DENY TCP 172.21.32.192 0.0.0.31 172.21.33.84 0.0.0.0 eq 21
DENY TCP 172.21.33.64 0.0.0.15 172.21.33.84 0.0.0.0 eq 20
DENY TCP 172.21.33.64 0.0.0.15 172.21.33.84 0.0.0.0 eq 21
DENY TCP 172.21.33.32 0.0.0.31 172.21.33.84 0.0.0.0 eq 20
DENY TCP 172.21.33.32 0.0.0.31 172.21.33.84 0.0.0.0 eq 21
DENY TCP 172.21.32.128 0.0.0.63 172.21.33.83 0.0.0.0 eq 80
DENY TCP 172.21.32.128 0.0.0.63 172.21.33.83 0.0.0.0 eq 443
DENY TCP 172.21.32.192 0.0.0.31 172.21.33.83 0.0.0.0 eq 80
DENY TCP 172.21.32.192 0.0.0.31 172.21.33.83 0.0.0.0 eq 443
DENY TCP 172.21.33.64 0.0.0.15 172.21.33.83 0.0.0.0 eq 80
DENY TCP 172.21.33.64 0.0.0.15 172.21.33.83 0.0.0.0 eq 443
permit ip any any
exit
int vlan 210
ip access-group FILTRO-FTPWEBPIU in
int vlan 220
ip access-group FILTRO-FTPWEBPIU in
int vlan 230
ip access-group FILTRO-FTPWEBPIU in
int vlan 240
ip access-group FILTRO-FTPWEBPIU in
int vlan 250
ip access-group FILTRO-FTPWEBPIU in
int vlan 270
ip access-group FILTRO-FTPWEBPIU in
int vlan 280
ip access-group FILTRO-FTPWEBPIU in
end

! GRABAR CONFIGURACION
write
write memory
copy running-config startup-config
```

### SWTICH DE ACCESO 1: ADMINISTRACION (SWPIU1)

```kotlin
! CONFIGURACION SEGURIDAD BASICA
enable
configure terminal
hostname SWPIU1
enable secret Piu#20
line console 0
password Piu>20
login
exit
line vty 0 15
password Piu>20
login
exit
banner motd $ *** SOLO PERSONAL AUTORIZADO *** $

! CREACION Y NOMBRAMIENTO DE VLANS
VLAN 210
name Administracion
VLAN 220
name Logistica
VLAN 230
name Finanzas
VLAN 240
name Marketing
VLAN 250
name Ventas
VLAN 260
name Servidores
VLAN 270
name WifiEjecutivo
VLAN 280
name WifiCliente
VLAN 99
name Management&Native
exit

! ASIGNACION DE VLAN A PCS
interface range f0/1-12
switchport mode access
switchport access VLAN 210
!
interface range f0/13-24
switchport mode access
switchport access VLAN 210
exit

! ASIGNACION DE ENLACE TRUNCAL CON LOS MLS
interface range g0/1
switchport mode trunk
switchport trunk native vlan 99

! DESHABILITAR VLAN 1
interface Vlan1
no ip address
shutdown

! ASIGNAR IP EN VLAN 99
interface Vlan99
ip address 172.21.99.18 255.255.255.240
exit
ip default-gateway 172.21.99.17
end

! GRABAR CONFIGURACION
write
write memory
copy running-config startup-config
```

### SWTICH DE ACCESO 2: LOGISTICA (SWPIU2)

```kotlin
! CONFIGURACION SEGURIDAD BASICA
enable
configure terminal
hostname SWPIU2
enable secret Piu#20
line console 0
password Piu>20
login
exit
line vty 0 15
password Piu>20
login
exit
banner motd $ *** SOLO PERSONAL AUTORIZADO *** $

! CREACION Y NOMBRAMIENTO DE VLANS
VLAN 210
name Administracion
VLAN 220
name Logistica
VLAN 230
name Finanzas
VLAN 240
name Marketing
VLAN 250
name Ventas
VLAN 260
name Servidores
VLAN 270
name WifiEjecutivo
VLAN 280
name WifiCliente
VLAN 99
name Management&Native
exit

! ASIGNACION DE VLAN A PCS
interface range f0/1-12
switchport mode access
switchport access VLAN 220
!
interface range f0/13-24
switchport mode access
switchport access VLAN 220
exit

! ASIGNACION DE ENLACE TRUNCAL CON LOS MLS
interface range g0/1
switchport mode trunk
switchport trunk native vlan 99

! DESHABILITAR VLAN 1
interface Vlan1
no ip address
shutdown

! ASIGNAR IP EN VLAN 99
interface Vlan99
ip address 172.21.99.19 255.255.255.240
exit
ip default-gateway 172.21.99.17
end

! GRABAR CONFIGURACION
write
write memory
copy running-config startup-config
```

### SWTICH DE ACCESO 3: FINANZAS (SWPIU3)

```kotlin
! CONFIGURACION SEGURIDAD BASICA
enable
configure terminal
hostname SWPIU3
enable secret Piu#20
line console 0
password Piu>20
login
exit
line vty 0 15
password Piu>20
login
exit
banner motd $ *** SOLO PERSONAL AUTORIZADO *** $

! CREACION Y NOMBRAMIENTO DE VLANS
VLAN 210
name Administracion
VLAN 220
name Logistica
VLAN 230
name Finanzas
VLAN 240
name Marketing
VLAN 250
name Ventas
VLAN 260
name Servidores
VLAN 270
name WifiEjecutivo
VLAN 280
name WifiCliente
VLAN 99
name Management&Native
exit

! ASIGNACION DE VLAN A PCS
interface range f0/1-12
switchport mode access
switchport access VLAN 230
!
interface range f0/13-24
switchport mode access
switchport access VLAN 230
exit

! ASIGNACION DE ENLACE TRUNCAL CON LOS MLS
interface range g0/1
switchport mode trunk
switchport trunk native vlan 99

! DESHABILITAR VLAN 1
interface Vlan1
no ip address
shutdown

! ASIGNAR IP EN VLAN 99
interface Vlan99
ip address 172.21.99.20 255.255.255.240
exit
ip default-gateway 172.21.99.17
end

! GRABAR CONFIGURACION
write
write memory
copy running-config startup-config
```

### SWTICH DE ACCESO 4: MARKETING (SWPIU4)

```kotlin
! CONFIGURACION SEGURIDAD BASICA
enable
configure terminal
hostname SWPIU4
enable secret Piu#20
line console 0
password Piu>20
login
exit
line vty 0 15
password Piu>20
login
exit
banner motd $ *** SOLO PERSONAL AUTORIZADO *** $

! CREACION Y NOMBRAMIENTO DE VLANS
VLAN 210
name Administracion
VLAN 220
name Logistica
VLAN 230
name Finanzas
VLAN 240
name Marketing
VLAN 250
name Ventas
VLAN 260
name Servidores
VLAN 270
name WifiEjecutivo
VLAN 280
name WifiCliente
VLAN 99
name Management&Native
exit

! ASIGNACION DE VLAN A PCS
interface range f0/1-12
switchport mode access
switchport access VLAN 240
!
interface range f0/13-24
switchport mode access
switchport access VLAN 240
exit

! ASIGNACION DE ENLACE TRUNCAL CON LOS MLS
interface range g0/1
switchport mode trunk
switchport trunk native vlan 99

! DESHABILITAR VLAN 1
interface Vlan1
no ip address
shutdown

! ASIGNAR IP EN VLAN 99
interface Vlan99
ip address 172.21.99.21 255.255.255.240
exit
ip default-gateway 172.21.99.17
end

! GRABAR CONFIGURACION
write
write memory
copy running-config startup-config
```

### SWTICH DE ACCESO 5: VENTAS (SWPIU5)

```kotlin
! CONFIGURACION SEGURIDAD BASICA
enable
configure terminal
hostname SWPIU5
enable secret Piu#20
line console 0
password Piu>20
login
exit
line vty 0 15
password Piu>20
login
exit
banner motd $ *** SOLO PERSONAL AUTORIZADO *** $

! CREACION Y NOMBRAMIENTO DE VLANS
VLAN 210
name Administracion
VLAN 220
name Logistica
VLAN 230
name Finanzas
VLAN 240
name Marketing
VLAN 250
name Ventas
VLAN 260
name Servidores
VLAN 270
name WifiEjecutivo
VLAN 280
name WifiCliente
VLAN 99
name Management&Native
exit

! ASIGNACION DE VLAN A PCS
interface range f0/1-12
switchport mode access
switchport access VLAN 250
!
interface range f0/13-24
switchport mode access
switchport access VLAN 250
exit

! ASIGNACION DE ENLACE TRUNCAL CON LOS MLS
interface range g0/1
switchport mode trunk
switchport trunk native vlan 99

! DESHABILITAR VLAN 1
interface Vlan1
no ip address
shutdown

! ASIGNAR IP EN VLAN 99
interface Vlan99
ip address 172.21.99.22 255.255.255.240
exit
ip default-gateway 172.21.99.17
end

! GRABAR CONFIGURACION
write
write memory
copy running-config startup-config
```

### SWTICH DE ACCESO 6: SERVIDORES (SWPIU6)

```kotlin
! CONFIGURACION SEGURIDAD BASICA
enable
configure terminal
hostname SWPIU6
enable secret Piu#20
line console 0
password Piu>20
login
exit
line vty 0 15
password Piu>20
login
exit
banner motd $ *** SOLO PERSONAL AUTORIZADO *** $

! CREACION Y NOMBRAMIENTO DE VLANS
VLAN 210
name Administracion
VLAN 220
name Logistica
VLAN 230
name Finanzas
VLAN 240
name Marketing
VLAN 250
name Ventas
VLAN 260
name Servidores
VLAN 270
name WifiEjecutivo
VLAN 280
name WifiCliente
VLAN 99
name Management&Native
exit

! ASIGNACION DE VLAN A PCS
interface range f0/1-12
switchport mode access
switchport access VLAN 260
!
interface range f0/13-24
switchport mode access
switchport access VLAN 260
exit

! ASIGNACION DE ENLACE TRUNCAL CON LOS MLS
interface range g0/1
switchport mode trunk
switchport trunk native vlan 99

! DESHABILITAR VLAN 1
interface Vlan1
no ip address
shutdown

! ASIGNAR IP EN VLAN 99
interface Vlan99
ip address 172.21.99.23 255.255.255.240
exit
ip default-gateway 172.21.99.17
end

! GRABAR CONFIGURACION
write
write memory
copy running-config startup-config
```

### SWTICH DE ACCESO 7: WIFI EJECUTIVO Y CLIENTE (SWPIU7)

```kotlin
! CONFIGURACION SEGURIDAD BASICA
enable
configure terminal
hostname SWPIU7
enable secret Piu#20
line console 0
password Piu>20
login
exit
line vty 0 15
password Piu>20
login
exit
banner motd $ *** SOLO PERSONAL AUTORIZADO *** $

! CREACION Y NOMBRAMIENTO DE VLANS
VLAN 210
name Administracion
VLAN 220
name Logistica
VLAN 230
name Finanzas
VLAN 240
name Marketing
VLAN 250
name Ventas
VLAN 260
name Servidores
VLAN 270
name WifiEjecutivo
VLAN 280
name WifiCliente
VLAN 99
name Management&Native
exit

! ASIGNACION DE VLAN A PCS
interface f0/1
switchport mode access
switchport access VLAN 270
!
interface f0/2
switchport mode access
switchport access VLAN 280
exit

! ASIGNACION DE ENLACE TRUNCAL CON LOS MLS
interface range g0/1
switchport mode trunk
switchport trunk native vlan 99

! DESHABILITAR VLAN 1
interface Vlan1
no ip address
shutdown

! ASIGNAR IP EN VLAN 99
interface Vlan99
ip address 172.21.99.24 255.255.255.240
exit
ip default-gateway 172.21.99.17
end

! GRABAR CONFIGURACION
write
write memory
copy running-config startup-config
```

## SEDE AREQUIPA

### ROUTER (RTARE1)

```kotlin
! CONFIGURACION SEGURIDAD BASICA
enable
configure terminal
hostname RTARE1
enable secret Are#20
line console 0
password Are>20
login
exit
line vty 0 15
password Are>20
login
exit
banner motd $ *** SOLO PERSONAL AUTORIZADO *** $

! ASIGNAR IP ROUTER - MLSARE1
interface g0/0/1
ip address 172.21.48.209 255.255.255.252

! ASIGNAR IP A ROUTERS DE OTRAS SEDES E ISP
interface Serial0/2/1
 ip address 172.22.254.10 255.255.255.252
 clock rate 2000000
end

! CONFIGURAR OSPF
configure terminal
router ospf 100
router-id 3.3.3.3
network 172.21.48.208 0.0.0.3 area 0
network 172.22.254.8 0.0.0.3 area 0
end

! CONFIGURAR PPP-PAP
configure terminal
interface s0/2/1
encapsulation ppp
exit
username RTLIM1 secret Lim#20
interface s0/2/1
ppp authentication pap
ppp pap sent-username RTARE1 password Are#20
end

! GRABAR CONFIGURACION
write
write memory
copy running-config startup-config
```

### MULTILAYER SWITCH (MLSARE1)

```kotlin
! CONFIGURACION SEGURIDAD BASICA
enable
configure terminal
hostname MLSARE1
enable secret Are#20
line console 0
password Are>20
login
exit
line vty 0 15
password Are>20
login
exit
banner motd $ *** SOLO PERSONAL AUTORIZADO *** $

! Habilitar el enrutamiento capa 3
ip routing

! CREACION Y NOMBRAMIENTO DE VLANS
VLAN 310
name Administracion
VLAN 320
name Logistica
VLAN 330
name Finanzas
VLAN 340
name Marketing
VLAN 350
name Ventas
VLAN 360
name Servidores
VLAN 370
name WifiEjecutivo
VLAN 380
name WifiCliente
VLAN 99
name Management&Native
exit

! ASIGNACION DE ENLACE TRUNCAL CON SWITCHES DE ACCESO
interface range g1/0/11-17
switchport trunk native vlan 99
switchport mode trunk
switchport nonegotiate

! ASIGNAR IP MLS - ROUTER
interface g1/0/1
no switchport
ip address 172.21.48.210 255.255.255.252

! DESHABILITAR VLAN1
interface Vlan1
no ip address
shutdown

! ASIGNAR IP DE LA MLS EN LAS VLANS
interface VLAN310
ip address 172.21.48.1 255.255.255.192
!
interface VLAN320
ip address 172.21.48.65 255.255.255.224
!
interface VLAN330
ip address 172.21.48.161 255.255.255.240
!
interface VLAN340
ip address 172.21.48.97 255.255.255.224
!
interface VLAN350
ip address 172.21.48.129 255.255.255.224
!
interface VLAN360
ip address 172.21.48.201 255.255.255.248
!
interface VLAN370
ip address 172.21.48.193 255.255.255.248
!
interface VLAN380
ip address 172.21.48.177 255.255.255.240
!
interface Vlan99
ip address 172.21.99.33 255.255.255.240
!
exit

! CREAR DHCP PARA CADA VLAN
ip dhcp pool POOL-VLAN310
network 172.21.48.0 255.255.255.192
default-router 172.21.48.1
dns-server 200.0.0.22
!
ip dhcp pool POOL-VLAN320
network 172.21.48.64 255.255.255.224
default-router 172.21.48.65
dns-server 200.0.0.22
!
ip dhcp pool POOL-VLAN330
network 172.21.48.160 255.255.255.240
default-router 172.21.48.161
dns-server 200.0.0.22
!
ip dhcp pool POOL-VLAN340
network 172.21.48.96 255.255.255.224
default-router 172.21.48.97
dns-server 200.0.0.22
!
ip dhcp pool POOL-VLAN350
network 172.21.48.128 255.255.255.224
default-router 172.21.48.129
dns-server 200.0.0.22
!
ip dhcp pool POOL-VLAN360
network 172.21.48.200 255.255.255.248
default-router 172.21.48.201
dns-server 200.0.0.22
!
ip dhcp pool POOL-VLAN370
network 172.21.48.192 255.255.255.248
default-router 172.21.48.193
dns-server 200.0.0.22
!
ip dhcp pool POOL-VLAN380
network 172.21.48.176 255.255.255.240
default-router 172.21.48.177
dns-server 200.0.0.22
end

! CONFIGURAR OSPF
configure terminal
router ospf 100
router-id 3.3.3.4
network 172.21.48.0 0.0.0.63 area 0
network 172.21.48.64 0.0.0.31 area 0
network 172.21.48.96 0.0.0.31 area 0
network 172.21.48.128 0.0.0.31 area 0
network 172.21.48.160 0.0.0.15 area 0
network 172.21.48.176 0.0.0.15 area 0
network 172.21.48.192 0.0.0.07 area 0
network 172.21.48.200 0.0.0.07 area 0
network 172.21.99.32 0.0.0.15 area 0
network 172.21.48.208 0.0.0.3 area 0
end

! CONFIGURAR ACL 
configure terminal 
IP ACCESS-LIST Extended FILTRO-FTPWEBARE
PERMIT TCP 172.21.48.0 0.0.0.63 172.21.48.202 0.0.0.0 eq 20
PERMIT TCP 172.21.48.0 0.0.0.63 172.21.48.202 0.0.0.0 eq 21
PERMIT TCP 172.21.48.64 0.0.0.31 172.21.48.202 0.0.0.0 eq 20
PERMIT TCP 172.21.48.64 0.0.0.31 172.21.48.202 0.0.0.0 eq 21
PERMIT TCP 172.21.48.160 0.0.0.15 172.21.48.202 0.0.0.0 eq 20 
PERMIT TCP 172.21.48.160 0.0.0.15 172.21.48.202 0.0.0.0 eq 21
DENY TCP 172.21.48.96 0.0.0.31 172.21.48.202 0.0.0.0 eq 20 
DENY TCP 172.21.48.96 0.0.0.31 172.21.48.202 0.0.0.0 eq 21 
DENY TCP 172.21.48.128 0.0.0.31 172.21.48.202 0.0.0.0 eq 20 
DENY TCP 172.21.48.128 0.0.0.31 172.21.48.202 0.0.0.0 eq 21
DENY TCP 172.21.48.176 0.0.0.15 172.21.48.202 0.0.0.0 eq 20 
DENY TCP 172.21.48.176 0.0.0.15 172.21.48.202 0.0.0.0 eq 21
DENY TCP 172.21.48.192 0.0.0.7 172.21.48.202 0.0.0.0 eq 20 
DENY TCP 172.21.48.192 0.0.0.7 172.21.48.202 0.0.0.0 eq 21
DENY TCP 172.21.48.0 0.0.0.63 172.21.48.204 0.0.0.0 eq 80 
DENY TCP 172.21.48.0 0.0.0.63 172.21.48.204 0.0.0.0 eq 443
DENY TCP 172.21.48.64 0.0.0.31  172.21.48.204 0.0.0.0 eq 80 
DENY TCP 172.21.48.64 0.0.0.31  172.21.48.204 0.0.0.0 eq 443
DENY TCP 172.21.48.160 0.0.0.15 172.21.48.204 0.0.0.0 eq 80 
DENY TCP 172.21.48.160 0.0.0.15 172.21.48.204 0.0.0.0 eq 443
PERMIT IP ANY ANY
exit
int vlan 310
ip access-group FILTRO-FTPWEBARE in
int vlan 320
ip access-group FILTRO-FTPWEBARE in
int vlan 330
ip access-group FILTRO-FTPWEBARE in
int vlan 340
ip access-group FILTRO-FTPWEBARE in
int vlan 350
ip access-group FILTRO-FTPWEBARE in
int vlan 370
ip access-group FILTRO-FTPWEBARE in
int vlan 380
ip access-group FILTRO-FTPWEBARE in
end 

! GRABAR CONFIGURACION
write
write memory
copy running-config startup-config
```

### SWTICH DE ACCESO 1: ADMINISTRACION (SWARE1)

```kotlin
! CONFIGURACION SEGURIDAD BASICA
enable
configure terminal
hostname SWARE1
enable secret Are#20
line console 0
password Are>20
login
exit
line vty 0 15
password Are>20
login
exit
banner motd $ *** SOLO PERSONAL AUTORIZADO *** $

! CREACION Y NOMBRAMIENTO DE VLANS
VLAN 310
name Administracion
VLAN 320
name Logistica
VLAN 330
name Finanzas
VLAN 340
name Marketing
VLAN 350
name Ventas
VLAN 360
name Servidores
VLAN 370
name WifiEjecutivo
VLAN 380
name WifiCliente
VLAN 99
name Management&Native
exit

! ASIGNACION DE VLAN A PCS
interface range f0/1-12
switchport mode access
switchport access VLAN 310
!
interface range f0/13-24
switchport mode access
switchport access VLAN 310
exit

! ASIGNACION DE ENLACE TRUNCAL CON LOS MLS
interface range g0/1
switchport mode trunk
switchport trunk native vlan 99

! DESHABILITAR VLAN 1
interface Vlan1
no ip address
shutdown

! ASIGNAR IP EN VLAN 99
interface Vlan99
ip address 172.21.99.34 255.255.255.240
exit
ip default-gateway 172.21.99.33
end

! GRABAR CONFIGURACION
write
write memory
copy running-config startup-config
```

### SWTICH DE ACCESO 2: LOGISTICA (SWARE2)

```kotlin
! CONFIGURACION SEGURIDAD BASICA
enable
configure terminal
hostname SWARE2
enable secret Are#20
line console 0
password Are>20
login
exit
line vty 0 15
password Are>20
login
exit
banner motd $ *** SOLO PERSONAL AUTORIZADO *** $

! CREACION Y NOMBRAMIENTO DE VLANS
VLAN 310
name Administracion
VLAN 320
name Logistica
VLAN 330
name Finanzas
VLAN 340
name Marketing
VLAN 350
name Ventas
VLAN 360
name Servidores
VLAN 370
name WifiEjecutivo
VLAN 380
name WifiCliente
VLAN 99
name Management&Native
exit

! ASIGNACION DE VLAN A PCS
interface range f0/1-12
switchport mode access
switchport access VLAN 320
!
interface range f0/13-24
switchport mode access
switchport access VLAN 320
exit

! ASIGNACION DE ENLACE TRUNCAL CON LOS MLS
interface range g0/1
switchport mode trunk
switchport trunk native vlan 99

! DESHABILITAR VLAN 1
interface Vlan1
no ip address
shutdown

! ASIGNAR IP EN VLAN 99
interface Vlan99
ip address 172.21.99.35 255.255.255.240
exit
ip default-gateway 172.21.99.33
end

! GRABAR CONFIGURACION
write
write memory
copy running-config startup-config
```

### SWTICH DE ACCESO 3: FINANZAS (SWARE3)

```kotlin
! CONFIGURACION SEGURIDAD BASICA
enable
configure terminal
hostname SWARE3
enable secret Are#20
line console 0
password Are>20
login
exit
line vty 0 15
password Are>20
login
exit
banner motd $ *** SOLO PERSONAL AUTORIZADO *** $

! CREACION Y NOMBRAMIENTO DE VLANS
VLAN 310
name Administracion
VLAN 320
name Logistica
VLAN 330
name Finanzas
VLAN 340
name Marketing
VLAN 350
name Ventas
VLAN 360
name Servidores
VLAN 370
name WifiEjecutivo
VLAN 380
name WifiCliente
VLAN 99
name Management&Native
exit

! ASIGNACION DE VLAN A PCS
interface range f0/1-12
switchport mode access
switchport access VLAN 330
!
interface range f0/13-24
switchport mode access
switchport access VLAN 330
exit

! ASIGNACION DE ENLACE TRUNCAL CON LOS MLS
interface range g0/1
switchport mode trunk
switchport trunk native vlan 99

! DESHABILITAR VLAN 1
interface Vlan1
no ip address
shutdown

! ASIGNAR IP EN VLAN 99
interface Vlan99
ip address 172.21.99.36 255.255.255.240
exit
ip default-gateway 172.21.99.33
end

! GRABAR CONFIGURACION
write
write memory
copy running-config startup-config
```

### SWTICH DE ACCESO 4: MARKETING (SWARE4)

```kotlin
! CONFIGURACION SEGURIDAD BASICA
enable
configure terminal
hostname SWARE4
enable secret Are#20
line console 0
password Are>20
login
exit
line vty 0 15
password Are>20
login
exit
banner motd $ *** SOLO PERSONAL AUTORIZADO *** $

! CREACION Y NOMBRAMIENTO DE VLANS
VLAN 310
name Administracion
VLAN 320
name Logistica
VLAN 330
name Finanzas
VLAN 340
name Marketing
VLAN 350
name Ventas
VLAN 360
name Servidores
VLAN 370
name WifiEjecutivo
VLAN 380
name WifiCliente
VLAN 99
name Management&Native
exit

! ASIGNACION DE VLAN A PCS
interface range f0/1-12
switchport mode access
switchport access VLAN 340
!
interface range f0/13-24
switchport mode access
switchport access VLAN 340
exit

! ASIGNACION DE ENLACE TRUNCAL CON LOS MLS
interface range g0/1
switchport mode trunk
switchport trunk native vlan 99

! DESHABILITAR VLAN 1
interface Vlan1
no ip address
shutdown

! ASIGNAR IP EN VLAN 99
interface Vlan99
ip address 172.21.99.37 255.255.255.240
exit
ip default-gateway 172.21.99.33
end

! GRABAR CONFIGURACION
write
write memory
copy running-config startup-config
```

### SWTICH DE ACCESO 5: VENTAS (SWARE5)

```kotlin
! CONFIGURACION SEGURIDAD BASICA
enable
configure terminal
hostname SWARE5
enable secret Are#20
line console 0
password Are>20
login
exit
line vty 0 15
password Are>20
login
exit
banner motd $ *** SOLO PERSONAL AUTORIZADO *** $

! CREACION Y NOMBRAMIENTO DE VLANS
VLAN 310
name Administracion
VLAN 320
name Logistica
VLAN 330
name Finanzas
VLAN 340
name Marketing
VLAN 350
name Ventas
VLAN 360
name Servidores
VLAN 370
name WifiEjecutivo
VLAN 380
name WifiCliente
VLAN 99
name Management&Native
exit

! ASIGNACION DE VLAN A PCS
interface range f0/1-12
switchport mode access
switchport access VLAN 350
!
interface range f0/13-24
switchport mode access
switchport access VLAN 350
exit

! ASIGNACION DE ENLACE TRUNCAL CON LOS MLS
interface range g0/1
switchport mode trunk
switchport trunk native vlan 99

! DESHABILITAR VLAN 1
interface Vlan1
no ip address
shutdown

! ASIGNAR IP EN VLAN 99
interface Vlan99
ip address 172.21.99.38 255.255.255.240
exit
ip default-gateway 172.21.99.33
end

! GRABAR CONFIGURACION
write
write memory
copy running-config startup-config
```

### SWTICH DE ACCESO 6: WIFI EJECUTIVO Y CLIENTE (SWARE6)

```kotlin
! CONFIGURACION SEGURIDAD BASICA
enable
configure terminal
hostname SWARE6
enable secret Are#20
line console 0
password Are>20
login
exit
line vty 0 15
password Are>20
login
exit
banner motd $ *** SOLO PERSONAL AUTORIZADO *** $

! CREACION Y NOMBRAMIENTO DE VLANS
VLAN 310
name Administracion
VLAN 320
name Logistica
VLAN 330
name Finanzas
VLAN 340
name Marketing
VLAN 350
name Ventas
VLAN 360
name Servidores
VLAN 370
name WifiEjecutivo
VLAN 380
name WifiCliente
VLAN 99
name Management&Native
exit

! ASIGNACION DE VLAN A PCS
interface f0/1
switchport mode access
switchport access VLAN 380
!
interface f0/2
switchport mode access
switchport access VLAN 370
exit

! ASIGNACION DE ENLACE TRUNCAL CON LOS MLS
interface range g0/1
switchport mode trunk
switchport trunk native vlan 99

! DESHABILITAR VLAN 1
interface Vlan1
no ip address
shutdown

! ASIGNAR IP EN VLAN 99
interface Vlan99
ip address 172.21.99.39 255.255.255.240
exit
ip default-gateway 172.21.99.33
end

! GRABAR CONFIGURACION
write
write memory
copy running-config startup-config
```

### SWTICH DE ACCESO 7: SERVIDORES (SWPIU7)

```kotlin
! CONFIGURACION SEGURIDAD BASICA
enable
configure terminal
hostname SWARE7
enable secret Are#20
line console 0
password Are>20
login
exit
line vty 0 15
password Are>20
login
exit
banner motd $ *** SOLO PERSONAL AUTORIZADO *** $

! CREACION Y NOMBRAMIENTO DE VLANS
VLAN 310
name Administracion
VLAN 320
name Logistica
VLAN 330
name Finanzas
VLAN 340
name Marketing
VLAN 350
name Ventas
VLAN 360
name Servidores
VLAN 370
name WifiEjecutivo
VLAN 380
name WifiCliente
VLAN 99
name Management&Native
exit

! ASIGNACION DE VLAN A PCS
interface range f0/1-12
switchport mode access
switchport access VLAN 360
!
interface range f0/13-24
switchport mode access
switchport access VLAN 360
exit

! ASIGNACION DE ENLACE TRUNCAL CON LOS MLS
interface range g0/1
switchport mode trunk
switchport trunk native vlan 99

! DESHABILITAR VLAN 1
interface Vlan1
no ip address
shutdown

! ASIGNAR IP EN VLAN 99
interface Vlan99
ip address 172.21.99.40 255.255.255.240
exit
ip default-gateway 172.21.99.33
end

! GRABAR CONFIGURACION
write
write memory
copy running-config startup-config
```

## SEDE CUSCO

### ROUTER (RTCUS1)

```kotlin
! CONFIGURACION SEGURIDAD BASICA
enable
configure terminal
hostname RTCUS1
enable secret Cus#20
line console 0
password Cus>20
login
exit
line vty 0 15
password Cus>20
login
exit
banner motd $ *** SOLO PERSONAL AUTORIZADO *** $

! ASIGNAR IP ROUTER - MLSCUS1
interface g0/0/1
ip address 172.21.64.201 255.255.255.252

! ASIGNAR IP A ROUTERS DE OTRAS SEDES E ISP
interface Serial0/1/0
 ip address 172.22.254.14 255.255.255.252
 clock rate 2000000
end

! CONFIGURAR OSPF
configure terminal
router ospf 100
router-id 4.4.4.4
network 172.21.64.200 0.0.0.3 area 0
network 172.22.254.12 0.0.0.3 area 0
end

! CONFIGURAR PPP-PAP
configure terminal
interface s0/1/0
encapsulation ppp
exit
username RTLIM1 secret Lim#20
interface s0/1/0
ppp authentication pap
ppp pap sent-username RTCUS1 password Cus#20
end

! GRABAR CONFIGURACION
write
write memory
copy running-config startup-config
```

### MULTILAYER SWITCH (MLSCUS1)

```kotlin
! CONFIGURACION SEGURIDAD BASICA
enable
configure terminal
hostname MLSCUS1
enable secret Cus#20
line console 0
password Cus>20
login
exit
line vty 0 15
password Cus>20
login
exit
banner motd $ *** SOLO PERSONAL AUTORIZADO *** $

! Habilitar el enrutamiento capa 3
ip routing

! CREACION Y NOMBRAMIENTO DE VLANS
VLAN 410
name Administracion
VLAN 420
name Logistica
VLAN 430
name Finanzas
VLAN 440
name Marketing
VLAN 450
name Ventas
VLAN 460
name Servidores
VLAN 470
name WifiEjecutivo
VLAN 480
name WifiCliente
VLAN 99
name Management&Native
exit

! ASIGNACION DE ENLACE TRUNCAL CON SWITCHES DE ACCESO
interface range g1/0/11-17
switchport trunk native vlan 99
switchport mode trunk
switchport nonegotiate

! ASIGNAR IP MLS - ROUTER
interface g1/0/1
no switchport
ip address 172.21.64.202 255.255.255.252

! DESHABILITAR VLAN1
interface Vlan1
no ip address
shutdown

! ASIGNAR IP DE LA MLS EN LAS VLANS
interface VLAN410
ip address 172.21.64.1 255.255.255.192
!
interface VLAN420
ip address 172.21.64.65 255.255.255.224
!
interface VLAN430
ip address 172.21.64.145 255.255.255.240
!
interface VLAN440
ip address 172.21.64.97 255.255.255.224
!
interface VLAN450
ip address 172.21.64.129 255.255.255.240
!
interface VLAN460
ip address 172.21.64.193 255.255.255.248
!
interface VLAN470
ip address 172.21.64.177 255.255.255.240
!
interface VLAN480
ip address 172.21.64.161 255.255.255.240
!
interface Vlan99
ip address 172.21.99.49 255.255.255.240
!
exit

! CREAR DHCP PARA CADA VLAN
ip dhcp pool POOL-VLAN410
network 172.21.64.0 255.255.255.192
default-router 172.21.64.1
dns-server 200.0.0.22
!
ip dhcp pool POOL-VLAN420
network 172.21.64.64 255.255.255.224
default-router 172.21.64.65
dns-server 200.0.0.22
!
ip dhcp pool POOL-VLAN430
network 172.21.64.144 255.255.255.240
default-router 172.21.64.145
dns-server 200.0.0.22
!
ip dhcp pool POOL-VLAN440
network 172.21.64.96 255.255.255.224
default-router 172.21.64.97
dns-server 200.0.0.22
!
ip dhcp pool POOL-VLAN450
network 172.21.64.128 255.255.255.240
default-router 172.21.64.129
dns-server 200.0.0.22
!
ip dhcp pool POOL-VLAN460
network 172.21.64.192 255.255.255.248
default-router 172.21.64.193
dns-server 200.0.0.22
!
ip dhcp pool POOL-VLAN470
network 172.21.64.176 255.255.255.240
default-router 172.21.64.177
dns-server 200.0.0.22
!
ip dhcp pool POOL-VLAN480
network 172.21.64.160 255.255.255.240
default-router 172.21.64.161
dns-server 200.0.0.22
end

! CONFIGURAR OSPF
configure terminal
router ospf 100
router-id 4.4.4.5
network 172.21.64.0 0.0.0.63 area 0
network 172.21.64.64 0.0.0.31 area 0
network 172.21.64.96 0.0.0.31 area 0
network 172.21.64.128 0.0.0.15 area 0
network 172.21.64.144 0.0.0.15 area 0
network 172.21.64.160 0.0.0.15 area 0
network 172.21.64.176 0.0.0.15 area 0
network 172.21.64.192 0.0.0.7 area 0
network 172.21.99.48 0.0.0.15 area 0
network 172.21.64.200 0.0.0.3 area 0
end

! CONFIGURAR ACL
configure terminal
ip access-list extended FILTRO-FTPWEBCUS
permit tcp 172.21.64.0 0.0.0.63 172.21.64.196 0.0.0.0 eq 20
permit tcp 172.21.64.0 0.0.0.63 172.21.64.196 0.0.0.0 eq 21
permit tcp 172.21.64.64 0.0.0.31 172.21.64.196 0.0.0.0 eq 20
permit tcp 172.21.64.64 0.0.0.31 172.21.64.196 0.0.0.0 eq 21
permit tcp 172.21.64.144 0.0.0.31 172.21.64.196 0.0.0.0 eq 20
permit tcp 172.21.64.144 0.0.0.31 172.21.64.196 0.0.0.0 eq 21
deny tcp 172.21.64.96 0.0.0.31 172.21.64.196 0.0.0.0 eq 20
deny tcp 172.21.64.96 0.0.0.31 172.21.64.196 0.0.0.0 eq 21
deny tcp 172.21.64.128 0.0.0.15 172.21.64.196 0.0.0.0 eq 20
deny tcp 172.21.64.128 0.0.0.15 172.21.64.196 0.0.0.0 eq 21
deny tcp 172.21.80.160 0.0.0.15 172.21.64.196 0.0.0.0 eq 20
deny tcp 172.21.80.160 0.0.0.15 172.21.64.196 0.0.0.0 eq 21
deny tcp 172.21.64.176 0.0.0.15 172.21.64.196 0.0.0.0 eq 20
deny tcp 172.21.64.176 0.0.0.15 172.21.64.196 0.0.0.0 eq 21
deny tcp 172.21.64.64 0.0.0.31 172.21.64.194 0.0.0.0 eq 80
deny tcp 172.21.64.64 0.0.0.31 172.21.64.194 0.0.0.0 eq 443
deny tcp 172.21.64.144 0.0.0.31 172.21.64.194 0.0.0.0 eq 80
deny tcp 172.21.64.144 0.0.0.31 172.21.64.194 0.0.0.0 eq 443
deny tcp 172.21.64.128 0.0.0.15 172.21.64.194 0.0.0.0 eq 80
deny tcp 172.21.64.128 0.0.0.15 172.21.64.194 0.0.0.0 eq 443
permit ip any any
exit
int vlan 410
ip access-group FILTRO-FTPWEBCUS in
int vlan 420
ip access-group FILTRO-FTPWEBCUS in
int vlan 430
ip access-group FILTRO-FTPWEBCUS in
int vlan 440
ip access-group FILTRO-FTPWEBCUS in
int vlan 450
ip access-group FILTRO-FTPWEBCUS in
int vlan 470
ip access-group FILTRO-FTPWEBCUS in
int vlan 480
ip access-group FILTRO-FTPWEBCUS in
end

! GRABAR CONFIGURACION
write
write memory
copy running-config startup-config
```

### SWTICH DE ACCESO 1: ADMINISTRACION (SWCUS1)

```kotlin
! CONFIGURACION SEGURIDAD BASICA
enable
configure terminal
hostname SWCUS1
enable secret Cus#20
line console 0
password Cus>20
login
exit
line vty 0 15
password Cus>20
login
exit
banner motd $ *** SOLO PERSONAL AUTORIZADO *** $

! CREACION Y NOMBRAMIENTO DE VLANS
VLAN 410
name Administracion
VLAN 420
name Logistica
VLAN 430
name Finanzas
VLAN 440
name Marketing
VLAN 450
name Ventas
VLAN 460
name Servidores
VLAN 470
name WifiEjecutivo
VLAN 480
name WifiCliente
VLAN 99
name Management&Native
exit

! ASIGNACION DE VLAN A PCS
interface range f0/1-12
switchport mode access
switchport access VLAN 410
!
interface range f0/13-24
switchport mode access
switchport access VLAN 410
exit

! ASIGNACION DE ENLACE TRUNCAL CON LOS MLS
interface range g0/1
switchport mode trunk
switchport trunk native vlan 99

! DESHABILITAR VLAN 1
interface Vlan1
no ip address
shutdown

! ASIGNAR IP EN VLAN 99
interface Vlan99
ip address 172.21.99.50 255.255.255.240
exit
ip default-gateway 172.21.99.49
end

! GRABAR CONFIGURACION
write
write memory
copy running-config startup-config
```

### SWTICH DE ACCESO 2: LOGISTICA (SWCUS2)

```kotlin
! CONFIGURACION SEGURIDAD BASICA
enable
configure terminal
hostname SWCUS2
enable secret Cus#20
line console 0
password Cus>20
login
exit
line vty 0 15
password Cus>20
login
exit
banner motd $ *** SOLO PERSONAL AUTORIZADO *** $

! CREACION Y NOMBRAMIENTO DE VLANS
VLAN 410
name Administracion
VLAN 420
name Logistica
VLAN 430
name Finanzas
VLAN 440
name Marketing
VLAN 450
name Ventas
VLAN 460
name Servidores
VLAN 470
name WifiEjecutivo
VLAN 480
name WifiCliente
VLAN 99
name Management&Native
exit

! ASIGNACION DE VLAN A PCS
interface range f0/1-12
switchport mode access
switchport access VLAN 420
!
interface range f0/13-24
switchport mode access
switchport access VLAN 420
exit

! ASIGNACION DE ENLACE TRUNCAL CON LOS MLS
interface range g0/1
switchport mode trunk
switchport trunk native vlan 99

! DESHABILITAR VLAN 1
interface Vlan1
no ip address
shutdown

! ASIGNAR IP EN VLAN 99
interface Vlan99
ip address 172.21.99.51 255.255.255.240
exit
ip default-gateway 172.21.99.49
end

! GRABAR CONFIGURACION
write
write memory
copy running-config startup-config
```

### SWTICH DE ACCESO 3: FINANZAS (SWCUS3)

```kotlin
! CONFIGURACION SEGURIDAD BASICA
enable
configure terminal
hostname SWCUS3
enable secret Cus#20
line console 0
password Cus>20
login
exit
line vty 0 15
password Cus>20
login
exit
banner motd $ *** SOLO PERSONAL AUTORIZADO *** $

! CREACION Y NOMBRAMIENTO DE VLANS
VLAN 410
name Administracion
VLAN 420
name Logistica
VLAN 430
name Finanzas
VLAN 440
name Marketing
VLAN 450
name Ventas
VLAN 460
name Servidores
VLAN 470
name WifiEjecutivo
VLAN 480
name WifiCliente
VLAN 99
name Management&Native
exit

! ASIGNACION DE VLAN A PCS
interface range f0/1-12
switchport mode access
switchport access VLAN 430
!
interface range f0/13-24
switchport mode access
switchport access VLAN 430
exit

! ASIGNACION DE ENLACE TRUNCAL CON LOS MLS
interface range g0/1
switchport mode trunk
switchport trunk native vlan 99

! DESHABILITAR VLAN 1
interface Vlan1
no ip address
shutdown

! ASIGNAR IP EN VLAN 99
interface Vlan99
ip address 172.21.99.52 255.255.255.240
exit
ip default-gateway 172.21.99.49
end

! GRABAR CONFIGURACION
write
write memory
copy running-config startup-config
```

### SWTICH DE ACCESO 4: MARKETING (SWCUS4)

```kotlin
! CONFIGURACION SEGURIDAD BASICA
enable
configure terminal
hostname SWCUS4
enable secret Cus#20
line console 0
password Cus>20
login
exit
line vty 0 15
password Cus>20
login
exit
banner motd $ *** SOLO PERSONAL AUTORIZADO *** $

! CREACION Y NOMBRAMIENTO DE VLANS
VLAN 410
name Administracion
VLAN 420
name Logistica
VLAN 430
name Finanzas
VLAN 440
name Marketing
VLAN 450
name Ventas
VLAN 460
name Servidores
VLAN 470
name WifiEjecutivo
VLAN 480
name WifiCliente
VLAN 99
name Management&Native
exit

! ASIGNACION DE VLAN A PCS
interface range f0/1-12
switchport mode access
switchport access VLAN 440
!
interface range f0/13-24
switchport mode access
switchport access VLAN 440
exit

! ASIGNACION DE ENLACE TRUNCAL CON LOS MLS
interface range g0/1
switchport mode trunk
switchport trunk native vlan 99

! DESHABILITAR VLAN 1
interface Vlan1
no ip address
shutdown

! ASIGNAR IP EN VLAN 99
interface Vlan99
ip address 172.21.99.53 255.255.255.240
exit
ip default-gateway 172.21.99.49
end

! GRABAR CONFIGURACION
write
write memory
copy running-config startup-config
```

### SWTICH DE ACCESO 5: VENTAS (SWCUS5)

```kotlin
! CONFIGURACION SEGURIDAD BASICA
enable
configure terminal
hostname SWCUS5
enable secret Cus#20
line console 0
password Cus>20
login
exit
line vty 0 15
password Cus>20
login
exit
banner motd $ *** SOLO PERSONAL AUTORIZADO *** $

! CREACION Y NOMBRAMIENTO DE VLANS
VLAN 410
name Administracion
VLAN 420
name Logistica
VLAN 430
name Finanzas
VLAN 440
name Marketing
VLAN 450
name Ventas
VLAN 460
name Servidores
VLAN 470
name WifiEjecutivo
VLAN 480
name WifiCliente
VLAN 99
name Management&Native
exit

! ASIGNACION DE VLAN A PCS
interface range f0/1-12
switchport mode access
switchport access VLAN 450
!
interface range f0/13-24
switchport mode access
switchport access VLAN 450
exit

! ASIGNACION DE ENLACE TRUNCAL CON LOS MLS
interface range g0/1
switchport mode trunk
switchport trunk native vlan 99

! DESHABILITAR VLAN 1
interface Vlan1
no ip address
shutdown

! ASIGNAR IP EN VLAN 99
interface Vlan99
ip address 172.21.99.54 255.255.255.240
exit
ip default-gateway 172.21.99.49
end

! GRABAR CONFIGURACION
write
write memory
copy running-config startup-config
```

### SWTICH DE ACCESO 6: WIFI EJECUTIVO Y CLIENTE (SWCUS6)

```kotlin
! CONFIGURACION SEGURIDAD BASICA
enable
configure terminal
hostname SWCUS6
enable secret Cus#20
line console 0
password Cus>20
login
exit
line vty 0 15
password Cus>20
login
exit
banner motd $ *** SOLO PERSONAL AUTORIZADO *** $

! CREACION Y NOMBRAMIENTO DE VLANS
VLAN 410
name Administracion
VLAN 420
name Logistica
VLAN 430
name Finanzas
VLAN 440
name Marketing
VLAN 450
name Ventas
VLAN 460
name Servidores
VLAN 470
name WifiEjecutivo
VLAN 480
name WifiCliente
VLAN 99
name Management&Native
exit

! ASIGNACION DE VLAN A PCS
interface f0/1
switchport mode access
switchport access VLAN 480
!
interface f0/2
switchport mode access
switchport access VLAN 470
exit

! ASIGNACION DE ENLACE TRUNCAL CON LOS MLS
interface range g0/1
switchport mode trunk
switchport trunk native vlan 99

! DESHABILITAR VLAN 1
interface Vlan1
no ip address
shutdown

! ASIGNAR IP EN VLAN 99
interface Vlan99
ip address 172.21.99.55 255.255.255.240
exit
ip default-gateway 172.21.99.49
end

! GRABAR CONFIGURACION
write
write memory
copy running-config startup-config
```

### SWTICH DE ACCESO 7: SERVIDORES (SWCUS7)

```kotlin
! CONFIGURACION SEGURIDAD BASICA
enable
configure terminal
hostname SWCUS7
enable secret Cus#20
line console 0
password Cus>20
login
exit
line vty 0 15
password Cus>20
login
exit
banner motd $ *** SOLO PERSONAL AUTORIZADO *** $

! CREACION Y NOMBRAMIENTO DE VLANS
VLAN 410
name Administracion
VLAN 420
name Logistica
VLAN 430
name Finanzas
VLAN 440
name Marketing
VLAN 450
name Ventas
VLAN 460
name Servidores
VLAN 470
name WifiEjecutivo
VLAN 480
name WifiCliente
VLAN 99
name Management&Native
exit

! ASIGNACION DE VLAN A PCS
interface range f0/1-12
switchport mode access
switchport access VLAN 460
!
interface range f0/13-24
switchport mode access
switchport access VLAN 460
exit

! ASIGNACION DE ENLACE TRUNCAL CON LOS MLS
interface range g0/1
switchport mode trunk
switchport trunk native vlan 99

! DESHABILITAR VLAN 1
interface Vlan1
no ip address
shutdown

! ASIGNAR IP EN VLAN 99
interface Vlan99
ip address 172.21.99.56 255.255.255.240
exit
ip default-gateway 172.21.99.49
end

! GRABAR CONFIGURACION
write
write memory
copy running-config startup-config
```

## SEDE CAJAMARCA

### ROUTER (RTCAJ1)

```kotlin
! CONFIGURACION SEGURIDAD BASICA
enable
configure terminal
hostname RTCAJ1
enable secret Caj#20
line console 0
password Caj>20
login
exit
line vty 0 15
password Caj>20
login
exit
banner motd $ *** SOLO PERSONAL AUTORIZADO *** $

! ASIGNAR IP ROUTER - MLSCAJ1
interface g0/0/1
ip address 172.21.80.193 255.255.255.252

! ASIGNAR IP A ROUTERS DE OTRAS SEDES E ISP
interface Serial0/2/0
 ip address 172.22.254.18 255.255.255.252
 clock rate 2000000
end

! CONFIGURAR OSPF
configure terminal
router ospf 100
router-id 5.5.5.5
network 172.21.80.192 0.0.0.3 area 0
network 172.22.254.16 0.0.0.3 area 0
end

! CONFIGURAR PPP-PAP
configure terminal
interface s0/2/0
encapsulation ppp
exit
username RTLIM1 secret Lim#20
interface s0/2/0
ppp authentication pap
ppp pap sent-username RTCAJ1 password Caj#20
end

! GRABAR CONFIGURACION
write
write memory
copy running-config startup-config
```

### MULTILAYER SWITCH (MLSCAJ1)

```kotlin
! CONFIGURACION SEGURIDAD BASICA
enable
configure terminal
hostname MLSCAJ1
enable secret Caj#20
line console 0
password Caj>20
login
exit
line vty 0 15
password Caj>20
login
exit
banner motd $ *** SOLO PERSONAL AUTORIZADO *** $

! Habilitar el enrutamiento capa 3
ip routing

! CREACION Y NOMBRAMIENTO DE VLANS
VLAN 510
name Administracion
VLAN 520
name Logistica
VLAN 530
name Finanzas
VLAN 540
name Marketing
VLAN 550
name Ventas
VLAN 560
name Servidores
VLAN 570
name WifiEjecutivo
VLAN 580
name WifiCliente
VLAN 99
name Management&Native
exit

! ASIGNACION DE ENLACE TRUNCAL CON SWITCHES DE ACCESO
interface range g1/0/11-17
switchport trunk native vlan 99
switchport mode trunk
switchport nonegotiate

! ASIGNAR IP MLS - ROUTER
interface g1/0/1
no switchport
ip address 172.21.80.194 255.255.255.252

! DESHABILITAR VLAN1
interface Vlan1
no ip address
shutdown

! ASIGNAR IP DE LA MLS EN LAS VLANS
interface VLAN510
ip address 172.21.80.1 255.255.255.192
!
interface VLAN520
ip address 172.21.80.65 255.255.255.224
!
interface VLAN530
ip address 172.21.80.145 255.255.255.240
!
interface VLAN540
ip address 172.21.80.97 255.255.255.224
!
interface VLAN550
ip address 172.21.80.129 255.255.255.240
!
interface VLAN560
ip address 172.21.80.185 255.255.255.248
!
interface VLAN570
ip address 172.21.80.177 255.255.255.248
!
interface VLAN580
ip address 172.21.80.161 255.255.255.240
!
interface Vlan99
ip address 172.21.99.65 255.255.255.240
!
exit

! CREAR DHCP PARA CADA VLAN
ip dhcp pool POOL-VLAN510
network 172.21.80.0 255.255.255.192
default-router 172.21.80.1
dns-server 200.0.0.22
!
ip dhcp pool POOL-VLAN520
network 172.21.80.64 255.255.255.224
default-router 172.21.80.65
dns-server 200.0.0.22
!
ip dhcp pool POOL-VLAN530
network 172.21.80.144 255.255.255.240
default-router 172.21.80.145
dns-server 200.0.0.22
!
ip dhcp pool POOL-VLAN540
network 172.21.80.96 255.255.255.224
default-router 172.21.80.97
dns-server 200.0.0.22
!
ip dhcp pool POOL-VLAN550
network 172.21.80.128 255.255.255.240
default-router 172.21.80.129
dns-server 200.0.0.22
!
ip dhcp pool POOL-VLAN560
network 172.21.80.184 255.255.255.248
default-router 172.21.80.185
dns-server 200.0.0.22
!
ip dhcp pool POOL-VLAN570
network 172.21.80.176 255.255.255.248
default-router 172.21.64.177
dns-server 200.0.0.22
!
ip dhcp pool POOL-VLAN580
network 172.21.80.160 255.255.255.240
default-router 172.21.64.161
dns-server 200.0.0.22
end

! CONFIGURAR OSPF
configure terminal
router ospf 100
router-id 5.5.5.6
network 172.21.80.0 0.0.0.63 area 0
network 172.21.80.64 0.0.0.31 area 0
network 172.21.80.96 0.0.0.31 area 0
network 172.21.80.128 0.0.0.15 area 0
network 172.21.80.144 0.0.0.15 area 0
network 172.21.80.160 0.0.0.15 area 0
network 172.21.80.176 0.0.0.7 area 0
network 172.21.80.184 0.0.0.7 area 0
network 172.21.99.64 0.0.0.15 area 0
network 172.21.80.192 0.0.0.3 area 0
end

! CONFIGURAR ACL
configure terminal
ip access-list extended FILTRO-FTPWEBCAJ
permit tcp 172.21.80.128 0.0.0.15 172.21.80.187 0.0.0.0 eq 20
permit tcp 172.21.80.128 0.0.0.15 172.21.80.187 0.0.0.0 eq 21
permit tcp 172.21.80.144 0.0.0.15 172.21.80.187 0.0.0.0 eq 20
permit tcp 172.21.80.144 0.0.0.15 172.21.80.187 0.0.0.0 eq 21
permit tcp 172.21.80.96 0.0.0.31 172.21.80.187 0.0.0.0 eq 20
permit tcp 172.21.80.96 0.0.0.31 172.21.80.187 0.0.0.0 eq 21
deny tcp 172.21.80.64 0.0.0.31 172.21.80.187 0.0.0.0 eq 20
deny tcp 172.21.80.64 0.0.0.31 172.21.80.187 0.0.0.0 eq 21
deny tcp 172.21.80.0 0.0.0.63 172.21.80.187 0.0.0.0 eq 20
deny tcp 172.21.80.0 0.0.0.63 172.21.80.187 0.0.0.0 eq 21
deny tcp 172.21.80.160 0.0.0.15 172.21.80.187 0.0.0.0 eq 20
deny tcp 172.21.80.160 0.0.0.15 172.21.80.187 0.0.0.0 eq 21
deny tcp 172.21.80.176 0.0.0.7 172.21.80.187 0.0.0.0 eq 20
deny tcp 172.21.80.176 0.0.0.7 172.21.80.187 0.0.0.0 eq 21
deny tcp 172.21.80.0 0.0.0.63 172.21.80.188 0.0.0.0 eq 80
deny tcp 172.21.80.0 0.0.0.63 172.21.80.188 0.0.0.0 eq 443
deny tcp 172.21.80.176 0.0.0.7 172.21.80.188 0.0.0.0 eq 80
deny tcp 172.21.80.176 0.0.0.7 172.21.80.188 0.0.0.0 eq 443
deny tcp 172.21.80.160 0.0.0.15 172.21.80.188 0.0.0.0 eq 80
deny tcp 172.21.80.160 0.0.0.15 172.21.80.188 0.0.0.0 eq 443
permit ip any any
exit
int vlan 510
ip access-group FILTRO-FTPWEBCAJ in
int vlan 520
ip access-group FILTRO-FTPWEBCAJ in
int vlan 530
ip access-group FILTRO-FTPWEBCAJ in
int vlan 540
ip access-group FILTRO-FTPWEBCAJ in
int vlan 550
ip access-group FILTRO-FTPWEBCAJ in
int vlan 570
ip access-group FILTRO-FTPWEBCAJ in
int vlan 580
ip access-group FILTRO-FTPWEBCAJ in
end

! GRABAR CONFIGURACION
write
write memory
copy running-config startup-config
```

### SWTICH DE ACCESO 1: ADMINISTRACION (SWCAJ1)

```kotlin
! CONFIGURACION SEGURIDAD BASICA
enable
configure terminal
hostname SWCAJ1
enable secret Caj#20
line console 0
password Caj>20
login
exit
line vty 0 15
password Caj>20
login
exit
banner motd $ *** SOLO PERSONAL AUTORIZADO *** $

! CREACION Y NOMBRAMIENTO DE VLANS
VLAN 510
name Administracion
VLAN 520
name Logistica
VLAN 530
name Finanzas
VLAN 540
name Marketing
VLAN 550
name Ventas
VLAN 560
name Servidores
VLAN 570
name WifiEjecutivo
VLAN 580
name WifiCliente
VLAN 99
name Management&Native
exit

! ASIGNACION DE VLAN A PCS
interface range f0/1-12
switchport mode access
switchport access VLAN 510
!
interface range f0/13-24
switchport mode access
switchport access VLAN 510
exit

! ASIGNACION DE ENLACE TRUNCAL CON LOS MLS
interface range g0/1
switchport mode trunk
switchport trunk native vlan 99

! DESHABILITAR VLAN 1
interface Vlan1
no ip address
shutdown

! ASIGNAR IP EN VLAN 99
interface Vlan99
ip address 172.21.99.66 255.255.255.240
exit
ip default-gateway 172.21.99.65
end

! GRABAR CONFIGURACION
write
write memory
copy running-config startup-config
```

### SWTICH DE ACCESO 2: LOGISTICA (SWCAJ2)

```kotlin
! CONFIGURACION SEGURIDAD BASICA
enable
configure terminal
hostname SWCAJ2
enable secret Caj#20
line console 0
password Caj>20
login
exit
line vty 0 15
password Caj>20
login
exit
banner motd $ *** SOLO PERSONAL AUTORIZADO *** $

! CREACION Y NOMBRAMIENTO DE VLANS
VLAN 510
name Administracion
VLAN 520
name Logistica
VLAN 530
name Finanzas
VLAN 540
name Marketing
VLAN 550
name Ventas
VLAN 560
name Servidores
VLAN 570
name WifiEjecutivo
VLAN 580
name WifiCliente
VLAN 99
name Management&Native
exit

! ASIGNACION DE VLAN A PCS
interface range f0/1-12
switchport mode access
switchport access VLAN 520
!
interface range f0/13-24
switchport mode access
switchport access VLAN 520
exit

! ASIGNACION DE ENLACE TRUNCAL CON LOS MLS
interface range g0/1
switchport mode trunk
switchport trunk native vlan 99

! DESHABILITAR VLAN 1
interface Vlan1
no ip address
shutdown

! ASIGNAR IP EN VLAN 99
interface Vlan99
ip address 172.21.99.67 255.255.255.240
exit
ip default-gateway 172.21.99.65
end

! GRABAR CONFIGURACION
write
write memory
copy running-config startup-config
```

### SWTICH DE ACCESO 3: FINANZAS (SWCAJ3)

```kotlin
! CONFIGURACION SEGURIDAD BASICA
enable
configure terminal
hostname SWCAJ3
enable secret Caj#20
line console 0
password Caj>20
login
exit
line vty 0 15
password Caj>20
login
exit
banner motd $ *** SOLO PERSONAL AUTORIZADO *** $

! CREACION Y NOMBRAMIENTO DE VLANS
VLAN 510
name Administracion
VLAN 520
name Logistica
VLAN 530
name Finanzas
VLAN 540
name Marketing
VLAN 550
name Ventas
VLAN 560
name Servidores
VLAN 570
name WifiEjecutivo
VLAN 580
name WifiCliente
VLAN 99
name Management&Native
exit

! ASIGNACION DE VLAN A PCS
interface range f0/1-12
switchport mode access
switchport access VLAN 530
!
interface range f0/13-24
switchport mode access
switchport access VLAN 530
exit

! ASIGNACION DE ENLACE TRUNCAL CON LOS MLS
interface range g0/1
switchport mode trunk
switchport trunk native vlan 99

! DESHABILITAR VLAN 1
interface Vlan1
no ip address
shutdown

! ASIGNAR IP EN VLAN 99
interface Vlan99
ip address 172.21.99.68 255.255.255.240
exit
ip default-gateway 172.21.99.65
end

! GRABAR CONFIGURACION
write
write memory
copy running-config startup-config
```

### SWTICH DE ACCESO 4: MARKETING (SWCAJ4)

```kotlin
! CONFIGURACION SEGURIDAD BASICA
enable
configure terminal
hostname SWCUS4
enable secret Caj#20
line console 0
password Caj>20
login
exit
line vty 0 15
password Caj>20
login
exit
banner motd $ *** SOLO PERSONAL AUTORIZADO *** $

! CREACION Y NOMBRAMIENTO DE VLANS
VLAN 510
name Administracion
VLAN 520
name Logistica
VLAN 530
name Finanzas
VLAN 540
name Marketing
VLAN 550
name Ventas
VLAN 560
name Servidores
VLAN 570
name WifiEjecutivo
VLAN 580
name WifiCliente
VLAN 99
name Management&Native
exit

! ASIGNACION DE VLAN A PCS
interface range f0/1-12
switchport mode access
switchport access VLAN 540
!
interface range f0/13-24
switchport mode access
switchport access VLAN 540
exit

! ASIGNACION DE ENLACE TRUNCAL CON LOS MLS
interface range g0/1
switchport mode trunk
switchport trunk native vlan 99

! DESHABILITAR VLAN 1
interface Vlan1
no ip address
shutdown

! ASIGNAR IP EN VLAN 99
interface Vlan99
ip address 172.21.99.69 255.255.255.240
exit
ip default-gateway 172.21.99.65
end

! GRABAR CONFIGURACION
write
write memory
copy running-config startup-config
```

### SWTICH DE ACCESO 5: VENTAS (SWCAJ5)

```kotlin
! CONFIGURACION SEGURIDAD BASICA
enable
configure terminal
hostname SWCAJ5
enable secret Caj#20
line console 0
password Caj>20
login
exit
line vty 0 15
password Caj>20
login
exit
banner motd $ *** SOLO PERSONAL AUTORIZADO *** $

! CREACION Y NOMBRAMIENTO DE VLANS
VLAN 510
name Administracion
VLAN 520
name Logistica
VLAN 530
name Finanzas
VLAN 540
name Marketing
VLAN 550
name Ventas
VLAN 560
name Servidores
VLAN 570
name WifiEjecutivo
VLAN 580
name WifiCliente
VLAN 99
name Management&Native
exit

! ASIGNACION DE VLAN A PCS
interface range f0/1-12
switchport mode access
switchport access VLAN 550
!
interface range f0/13-24
switchport mode access
switchport access VLAN 550
exit

! ASIGNACION DE ENLACE TRUNCAL CON LOS MLS
interface range g0/1
switchport mode trunk
switchport trunk native vlan 99

! DESHABILITAR VLAN 1
interface Vlan1
no ip address
shutdown

! ASIGNAR IP EN VLAN 99
interface Vlan99
ip address 172.21.99.70 255.255.255.240
exit
ip default-gateway 172.21.99.65
end

! GRABAR CONFIGURACION
write
write memory
copy running-config startup-config
```

### SWTICH DE ACCESO 6: WIFI EJECUTIVO Y CLIENTE (SWCAJ6)

```kotlin
! CONFIGURACION SEGURIDAD BASICA
enable
configure terminal
hostname SWCAJ6
enable secret Caj#20
line console 0
password Caj>20
login
exit
line vty 0 15
password Caj>20
login
exit
banner motd $ *** SOLO PERSONAL AUTORIZADO *** $

! CREACION Y NOMBRAMIENTO DE VLANS
VLAN 510
name Administracion
VLAN 520
name Logistica
VLAN 530
name Finanzas
VLAN 540
name Marketing
VLAN 550
name Ventas
VLAN 560
name Servidores
VLAN 570
name WifiEjecutivo
VLAN 580
name WifiCliente
VLAN 99
name Management&Native
exit

! ASIGNACION DE VLAN A PCS
interface f0/1
switchport mode access
switchport access VLAN 580
!
interface f0/2
switchport mode access
switchport access VLAN 570
exit

! ASIGNACION DE ENLACE TRUNCAL CON LOS MLS
interface range g0/1
switchport mode trunk
switchport trunk native vlan 99

! DESHABILITAR VLAN 1
interface Vlan1
no ip address
shutdown

! ASIGNAR IP EN VLAN 99
interface Vlan99
ip address 172.21.99.71 255.255.255.240
exit
ip default-gateway 172.21.99.65
end

! GRABAR CONFIGURACION
write
write memory
copy running-config startup-config
```

### SWTICH DE ACCESO 7: SERVIDORES (SWCAJ7)

```kotlin
! CONFIGURACION SEGURIDAD BASICA
enable
configure terminal
hostname SWCAJ7
enable secret Caj#20
line console 0
password Caj>20
login
exit
line vty 0 15
password Caj>20
login
exit
banner motd $ *** SOLO PERSONAL AUTORIZADO *** $

! CREACION Y NOMBRAMIENTO DE VLANS
VLAN 510
name Administracion
VLAN 520
name Logistica
VLAN 530
name Finanzas
VLAN 540
name Marketing
VLAN 550
name Ventas
VLAN 560
name Servidores
VLAN 570
name WifiEjecutivo
VLAN 580
name WifiCliente
VLAN 99
name Management&Native
exit

! ASIGNACION DE VLAN A PCS
interface range f0/1-12
switchport mode access
switchport access VLAN 560
!
interface range f0/13-24
switchport mode access
switchport access VLAN 560
exit

! ASIGNACION DE ENLACE TRUNCAL CON LOS MLS
interface range g0/1
switchport mode trunk
switchport trunk native vlan 99

! DESHABILITAR VLAN 1
interface Vlan1
no ip address
shutdown

! ASIGNAR IP EN VLAN 99
interface Vlan99
ip address 172.21.99.72 255.255.255.240
exit
ip default-gateway 172.21.99.65
end

! GRABAR CONFIGURACION
write
write memory
copy running-config startup-config
```
