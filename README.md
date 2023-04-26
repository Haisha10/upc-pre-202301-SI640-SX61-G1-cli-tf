# CONFIGURACIÓN DE LOS DISPOSITIVOS DE RED EN TODAS LAS SEDES

Trabajo Final de Redes y Comunicaciones de Datos

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
interface GigabitEthernet0/1
ip address 172.21.17.138 255.255.255.248

! ASIGNAR IP ROUTER - MLSLIM2
interface GigabitEthernet0/2
ip address 172.21.17.146 255.255.255.248


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
VLAN 10
name Administracion
VLAN 20
name Logistica
VLAN 30
name Finanzas
VLAN 40
name Marketing
VLAN 50
name Ventas
VLAN 60
name Servidores
VLAN 70
name WifiEjecutivo
VLAN 80
name WifiCliente
VLAN 99
name Management&Native
exit

! ASIGNACION DE ENLACE TRUNCAL CON SWITCHES DE ACCESO
interface range GigabitEthernet1/0/11-17
switchport trunk native vlan 99
switchport mode trunk
switchport nonegotiate

! ASIGNAR IP MLS - ROUTER
interface GigabitEthernet1/0/1
no switchport
ip address 172.21.17.139 255.255.255.248

! DESHABILITAR VLAN1
interface Vlan1
no ip address
shutdown

! ASIGNAR IP DE LA MLS EN LAS VLANS
interface Vlan10
ip address 172.21.16.1 255.255.255.128
!
interface Vlan20
ip address 172.21.16.129 255.255.255.192
!
interface Vlan30
ip address 172.21.17.33  255.255.255.224
!
interface Vlan40
ip address 172.21.16.193 255.255.255.192
!
interface Vlan50
ip address 172.21.17.1 255.255.255.224
!
interface Vlan60
ip address 172.21.17.129 255.255.255.248
!
interface Vlan70
ip address 172.21.17.97 255.255.255.224
!
interface Vlan80
ip address 172.21.17.65 255.255.255.224
!
interface Vlan99
ip address 172.21.99.1 255.255.255.0
!
exit

! CREAR DHCP PARA CADA VLAN
ip dhcp pool POOL-VLAN10
network 172.21.16.0 255.255.255.128
default-router 172.21.16.1
!
ip dhcp pool POOL-VLAN20
network 172.21.16.128 255.255.255.192
default-router 172.21.16.129
!
ip dhcp pool POOL-VLAN30
network 172.21.17.32 255.255.255.224
default-router 172.21.17.33
!
ip dhcp pool POOL-VLAN40
network 172.21.16.192 255.255.255.192
default-router 172.21.16.193
!
ip dhcp pool POOL-VLAN50
network 172.21.17.0 255.255.255.224
default-router 172.21.17.1
!
ip dhcp pool POOL-VLAN60
network 172.21.17.128 255.255.255.248
default-router 172.21.17.129
!
ip dhcp pool POOL-VLAN70
network 172.21.17.96 255.255.255.224
default-router 172.21.17.97
!
ip dhcp pool POOL-VLAN80
network 172.21.17.64 255.255.255.224
default-router 172.21.17.65
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
VLAN 10
name Administracion
VLAN 20
name Logistica
VLAN 30
name Finanzas
VLAN 40
name Marketing
VLAN 50
name Ventas
VLAN 60
name Servidores
VLAN 70
name WifiEjecutivo
VLAN 80
name WifiCliente
VLAN 99
name Management&Native
exit

! ASIGNACION DE ENLACE TRUNCAL CON SWITCHES DE ACCESO
interface range GigabitEthernet1/0/11-17
switchport trunk native vlan 99
switchport mode trunk
switchport nonegotiate

! ASIGNAR IP MLS - ROUTER
interface GigabitEthernet1/0/1
no switchport
ip address 172.21.17.149 255.255.255.248

! DESHABILITAR VLAN1
interface Vlan1
no ip address
shutdown

! ASIGNAR IP DE LA MLS EN LAS VLANS
interface Vlan10
ip address 172.21.16.2 255.255.255.128
!
interface Vlan20
ip address 172.21.16.130 255.255.255.192
!
interface Vlan30
ip address 172.21.17.34  255.255.255.224
!
interface Vlan40
ip address 172.21.16.194 255.255.255.192
!
interface Vlan50
ip address 172.21.17.2 255.255.255.224
!
interface Vlan60
ip address 172.21.17.130 255.255.255.248
!
interface Vlan70
ip address 172.21.17.98 255.255.255.224
!
interface Vlan80
ip address 172.21.17.66 255.255.255.224
!
interface Vlan99
ip address 172.21.99.2 255.255.255.0
!
exit

! CREAR DHCP PARA CADA VLAN
ip dhcp pool POOL-VLAN10
network 172.21.16.0 255.255.255.128
default-router 172.21.16.2
!
ip dhcp pool POOL-VLAN20
network 172.21.16.128 255.255.255.192
default-router 172.21.16.130
!
ip dhcp pool POOL-VLAN30
network 172.21.17.32 255.255.255.224
default-router 172.21.17.34
!
ip dhcp pool POOL-VLAN40
network 172.21.16.192 255.255.255.192
default-router 172.21.16.194
!
ip dhcp pool POOL-VLAN50
network 172.21.17.0 255.255.255.224
default-router 172.21.17.2
!
ip dhcp pool POOL-VLAN60
network 172.21.17.128 255.255.255.248
default-router 172.21.17.130
!
ip dhcp pool POOL-VLAN70
network 172.21.17.96 255.255.255.224
default-router 172.21.17.98
!
ip dhcp pool POOL-VLAN80
network 172.21.17.64 255.255.255.224
default-router 172.21.17.66
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
VLAN 10
name Administracion
VLAN 20
name Logistica
VLAN 30
name Finanzas
VLAN 40
name Marketing
VLAN 50
name Ventas
VLAN 60
name Servidores
VLAN 70
name WifiEjecutivo
VLAN 80
name WifiCliente
VLAN 99
name Management&Native
exit

! ASIGNACION DE VLAN A PCS
interface range f0/1-12
switchport mode access
switchport access vlan 10
!
interface range f0/13-24
switchport mode access
switchport access vlan 10
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
ip address 172.21.99.11 255.255.255.0
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
VLAN 10
name Administracion
VLAN 20
name Logistica
VLAN 30
name Finanzas
VLAN 40
name Marketing
VLAN 50
name Ventas
VLAN 60
name Servidores
VLAN 70
name WifiEjecutivo
VLAN 80
name WifiCliente
VLAN 99
name Management&Native
exit

! ASIGNACION DE VLAN A PCS
interface range f0/1-12
switchport mode access
switchport access vlan 20
!
interface range f0/13-24
switchport mode access
switchport access vlan 20
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
ip address 172.21.99.12 255.255.255.0
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
VLAN 10
name Administracion
VLAN 20
name Logistica
VLAN 30
name Finanzas
VLAN 40
name Marketing
VLAN 50
name Ventas
VLAN 60
name Servidores
VLAN 70
name WifiEjecutivo
VLAN 80
name WifiCliente
VLAN 99
name Management&Native
exit

! ASIGNACION DE VLAN A PCS
interface range f0/1-12
switchport mode access
switchport access vlan 30
!
interface range f0/13-24
switchport mode access
switchport access vlan 30
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
ip address 172.21.99.13 255.255.255.0
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
VLAN 10
name Administracion
VLAN 20
name Logistica
VLAN 30
name Finanzas
VLAN 40
name Marketing
VLAN 50
name Ventas
VLAN 60
name Servidores
VLAN 70
name WifiEjecutivo
VLAN 80
name WifiCliente
VLAN 99
name Management&Native
exit

! ASIGNACION DE VLAN A PCS
interface range f0/1-12
switchport mode access
switchport access vlan 40
!
interface range f0/13-24
switchport mode access
switchport access vlan 40
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
ip address 172.21.99.14 255.255.255.0
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
VLAN 10
name Administracion
VLAN 20
name Logistica
VLAN 30
name Finanzas
VLAN 40
name Marketing
VLAN 50
name Ventas
VLAN 60
name Servidores
VLAN 70
name WifiEjecutivo
VLAN 80
name WifiCliente
VLAN 99
name Management&Native
exit

! ASIGNACION DE VLAN A PCS
interface range f0/1-12
switchport mode access
switchport access vlan 50
!
interface range f0/13-24
switchport mode access
switchport access vlan 50
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
ip address 172.21.99.15 255.255.255.0
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
VLAN 10
name Administracion
VLAN 20
name Logistica
VLAN 30
name Finanzas
VLAN 40
name Marketing
VLAN 50
name Ventas
VLAN 60
name Servidores
VLAN 70
name WifiEjecutivo
VLAN 80
name WifiCliente
VLAN 99
name Management&Native
exit

! ASIGNACION DE VLAN A PCS
interface range f0/1-12
switchport mode access
switchport access vlan 60
!
interface range f0/13-24
switchport mode access
switchport access vlan 60
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
ip address 172.21.99.16 255.255.255.0
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
VLAN 10
name Administracion
VLAN 20
name Logistica
VLAN 30
name Finanzas
VLAN 40
name Marketing
VLAN 50
name Ventas
VLAN 60
name Servidores
VLAN 70
name WifiEjecutivo
VLAN 80
name WifiCliente
VLAN 99
name Management&Native
exit

! ASIGNACION DE VLAN A PCS
interface f0/1
switchport mode access
switchport access vlan 80
!
interface f0/2
switchport mode access
switchport access vlan 70
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
ip address 172.21.99.17 255.255.255.0
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
interface GigabitEthernet0/0/1
ip address 172.21.33.90 255.255.255.248

! ASIGNAR IP A ROUTERS DE OTRAS SEDES E ISP
interface Serial0/1/1
 ip address 172.22.254.6 255.255.255.252
 clock rate 2000000
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
VLAN 10
name Administracion
VLAN 20
name Logistica
VLAN 30
name Finanzas
VLAN 40
name Marketing
VLAN 50
name Ventas
VLAN 60
name Servidores
VLAN 70
name WifiEjecutivo
VLAN 80
name WifiCliente
VLAN 99
name Management&Native
exit

! ASIGNACION DE ENLACE TRUNCAL CON SWITCHES DE ACCESO
interface range GigabitEthernet1/0/11-17
switchport trunk native vlan 99
switchport mode trunk
switchport nonegotiate

! ASIGNAR IP MLS - ROUTER
interface GigabitEthernet1/0/1
no switchport
ip address 172.21.33.91 255.255.255.248

! DESHABILITAR VLAN1
interface Vlan1
no ip address
shutdown

! ASIGNAR IP DE LA MLS EN LAS VLANS
interface Vlan10
ip address 172.21.32.1 255.255.255.128
!
interface Vlan20
ip address 172.21.32.129 255.255.255.192
!
interface Vlan30
ip address 172.21.33.1  255.255.255.224
!
interface Vlan40
ip address 172.21.32.193 255.255.255.224
!
interface Vlan50
ip address 172.21.32.225 255.255.255.224
!
interface Vlan60
ip address 172.21.33.81 255.255.255.248
!
interface Vlan70
ip address 172.21.33.65 255.255.255.240
!
interface Vlan80
ip address 172.21.33.33 255.255.255.224
!
interface Vlan99
ip address 172.21.99.3 255.255.255.0
!
exit

! CREAR DHCP PARA CADA VLAN
ip dhcp pool POOL-VLAN10
network 172.21.32.0 255.255.255.128
default-router 172.21.32.1
!
ip dhcp pool POOL-VLAN20
network 172.21.32.128 255.255.255.192
default-router 172.21.32.129
!
ip dhcp pool POOL-VLAN30
network 172.21.33.0  255.255.255.224
default-router 172.21.33.1
!
ip dhcp pool POOL-VLAN40
network 172.21.32.192 255.255.255.224
default-router 172.21.32.193
!
ip dhcp pool POOL-VLAN50
network 172.21.32.224 255.255.255.224
default-router 172.21.32.225
!
ip dhcp pool POOL-VLAN60
network 172.21.33.80 255.255.255.248
default-router 172.21.33.81
!
ip dhcp pool POOL-VLAN70
network 172.21.33.64 255.255.255.240
default-router 172.21.33.65
!
ip dhcp pool POOL-VLAN80
network 172.21.33.32 255.255.255.224
default-router 172.21.33.33
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
VLAN 10
name Administracion
VLAN 20
name Logistica
VLAN 30
name Finanzas
VLAN 40
name Marketing
VLAN 50
name Ventas
VLAN 60
name Servidores
VLAN 70
name WifiEjecutivo
VLAN 80
name WifiCliente
VLAN 99
name Management&Native
exit

! ASIGNACION DE VLAN A PCS
interface range f0/1-12
switchport mode access
switchport access vlan 10
!
interface range f0/13-24
switchport mode access
switchport access vlan 10
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
ip address 172.21.99.21 255.255.255.0
exit
ip default-gateway 172.21.99.3
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
VLAN 10
name Administracion
VLAN 20
name Logistica
VLAN 30
name Finanzas
VLAN 40
name Marketing
VLAN 50
name Ventas
VLAN 60
name Servidores
VLAN 70
name WifiEjecutivo
VLAN 80
name WifiCliente
VLAN 99
name Management&Native
exit

! ASIGNACION DE VLAN A PCS
interface range f0/1-12
switchport mode access
switchport access vlan 20
!
interface range f0/13-24
switchport mode access
switchport access vlan 20
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
ip address 172.21.99.22 255.255.255.0
exit
ip default-gateway 172.21.99.3
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
VLAN 10
name Administracion
VLAN 20
name Logistica
VLAN 30
name Finanzas
VLAN 40
name Marketing
VLAN 50
name Ventas
VLAN 60
name Servidores
VLAN 70
name WifiEjecutivo
VLAN 80
name WifiCliente
VLAN 99
name Management&Native
exit

! ASIGNACION DE VLAN A PCS
interface range f0/1-12
switchport mode access
switchport access vlan 30
!
interface range f0/13-24
switchport mode access
switchport access vlan 30
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
ip address 172.21.99.23 255.255.255.0
exit
ip default-gateway 172.21.99.3
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
VLAN 10
name Administracion
VLAN 20
name Logistica
VLAN 30
name Finanzas
VLAN 40
name Marketing
VLAN 50
name Ventas
VLAN 60
name Servidores
VLAN 70
name WifiEjecutivo
VLAN 80
name WifiCliente
VLAN 99
name Management&Native
exit

! ASIGNACION DE VLAN A PCS
interface range f0/1-12
switchport mode access
switchport access vlan 40
!
interface range f0/13-24
switchport mode access
switchport access vlan 40
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
ip address 172.21.99.24 255.255.255.0
exit
ip default-gateway 172.21.99.3
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
VLAN 10
name Administracion
VLAN 20
name Logistica
VLAN 30
name Finanzas
VLAN 40
name Marketing
VLAN 50
name Ventas
VLAN 60
name Servidores
VLAN 70
name WifiEjecutivo
VLAN 80
name WifiCliente
VLAN 99
name Management&Native
exit

! ASIGNACION DE VLAN A PCS
interface range f0/1-12
switchport mode access
switchport access vlan 50
!
interface range f0/13-24
switchport mode access
switchport access vlan 50
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
ip address 172.21.99.25 255.255.255.0
exit
ip default-gateway 172.21.99.3
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
VLAN 10
name Administracion
VLAN 20
name Logistica
VLAN 30
name Finanzas
VLAN 40
name Marketing
VLAN 50
name Ventas
VLAN 60
name Servidores
VLAN 70
name WifiEjecutivo
VLAN 80
name WifiCliente
VLAN 99
name Management&Native
exit

! ASIGNACION DE VLAN A PCS
interface range f0/1-12
switchport mode access
switchport access vlan 60
!
interface range f0/13-24
switchport mode access
switchport access vlan 60
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
ip address 172.21.99.26 255.255.255.0
exit
ip default-gateway 172.21.99.3
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
VLAN 10
name Administracion
VLAN 20
name Logistica
VLAN 30
name Finanzas
VLAN 40
name Marketing
VLAN 50
name Ventas
VLAN 60
name Servidores
VLAN 70
name WifiEjecutivo
VLAN 80
name WifiCliente
VLAN 99
name Management&Native
exit

! ASIGNACION DE VLAN A PCS
interface f0/1
switchport mode access
switchport access vlan 70
!
interface f0/2
switchport mode access
switchport access vlan 80
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
ip address 172.21.99.27 255.255.255.0
exit
ip default-gateway 172.21.99.3
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
interface GigabitEthernet0/0/1
ip address 172.21.48.210 255.255.255.248

! ASIGNAR IP A ROUTERS DE OTRAS SEDES E ISP
interface Serial0/2/1
 ip address 172.22.254.10 255.255.255.252
 clock rate 2000000
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
VLAN 10
name Administracion
VLAN 20
name Logistica
VLAN 30
name Finanzas
VLAN 40
name Marketing
VLAN 50
name Ventas
VLAN 60
name Servidores
VLAN 70
name WifiEjecutivo
VLAN 80
name WifiCliente
VLAN 99
name Management&Native
exit

! ASIGNACION DE ENLACE TRUNCAL CON SWITCHES DE ACCESO
interface range GigabitEthernet1/0/11-17
switchport trunk native vlan 99
switchport mode trunk
switchport nonegotiate

! ASIGNAR IP MLS - ROUTER
interface GigabitEthernet1/0/1
no switchport
ip address 172.21.48.211 255.255.255.248

! DESHABILITAR VLAN1
interface Vlan1
no ip address
shutdown

! ASIGNAR IP DE LA MLS EN LAS VLANS
interface Vlan10
ip address 172.21.48.1 255.255.255.192
!
interface Vlan20
ip address 172.21.48.65 255.255.255.224
!
interface Vlan30
ip address 172.21.48.161 255.255.255.240
!
interface Vlan40
ip address 172.21.48.97 255.255.255.224
!
interface Vlan50
ip address 172.21.48.129 255.255.255.224
!
interface Vlan60
ip address 172.21.48.201 255.255.255.248
!
interface Vlan70
ip address 172.21.48.193 255.255.255.248
!
interface Vlan80
ip address 172.21.48.177 255.255.255.240
!
interface Vlan99
ip address 172.21.99.4 255.255.255.0
!
exit

! CREAR DHCP PARA CADA VLAN
ip dhcp pool POOL-VLAN10
network 172.21.48.0 255.255.255.192
default-router 172.21.48.1
!
ip dhcp pool POOL-VLAN20
network 172.21.48.64 255.255.255.224
default-router 172.21.48.65
!
ip dhcp pool POOL-VLAN30
network 172.21.48.160 255.255.255.240
default-router 172.21.48.161
!
ip dhcp pool POOL-VLAN40
network 172.21.48.96 255.255.255.224
default-router 172.21.48.97
!
ip dhcp pool POOL-VLAN50
network 172.21.48.128 255.255.255.224
default-router 172.21.48.129
!
ip dhcp pool POOL-VLAN60
network 172.21.48.200 255.255.255.248
default-router 172.21.48.201
!
ip dhcp pool POOL-VLAN70
network 172.21.48.192 255.255.255.248
default-router 172.21.48.193
!
ip dhcp pool POOL-VLAN80
network 172.21.48.176 255.255.255.240
default-router 172.21.48.177
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
VLAN 10
name Administracion
VLAN 20
name Logistica
VLAN 30
name Finanzas
VLAN 40
name Marketing
VLAN 50
name Ventas
VLAN 60
name Servidores
VLAN 70
name WifiEjecutivo
VLAN 80
name WifiCliente
VLAN 99
name Management&Native
exit

! ASIGNACION DE VLAN A PCS
interface range f0/1-12
switchport mode access
switchport access vlan 10
!
interface range f0/13-24
switchport mode access
switchport access vlan 10
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
ip address 172.21.99.31 255.255.255.0
exit
ip default-gateway 172.21.99.4
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
VLAN 10
name Administracion
VLAN 20
name Logistica
VLAN 30
name Finanzas
VLAN 40
name Marketing
VLAN 50
name Ventas
VLAN 60
name Servidores
VLAN 70
name WifiEjecutivo
VLAN 80
name WifiCliente
VLAN 99
name Management&Native
exit

! ASIGNACION DE VLAN A PCS
interface range f0/1-12
switchport mode access
switchport access vlan 20
!
interface range f0/13-24
switchport mode access
switchport access vlan 20
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
ip address 172.21.99.32 255.255.255.0
exit
ip default-gateway 172.21.99.4
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
VLAN 10
name Administracion
VLAN 20
name Logistica
VLAN 30
name Finanzas
VLAN 40
name Marketing
VLAN 50
name Ventas
VLAN 60
name Servidores
VLAN 70
name WifiEjecutivo
VLAN 80
name WifiCliente
VLAN 99
name Management&Native
exit

! ASIGNACION DE VLAN A PCS
interface range f0/1-12
switchport mode access
switchport access vlan 30
!
interface range f0/13-24
switchport mode access
switchport access vlan 30
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
ip address 172.21.99.33 255.255.255.0
exit
ip default-gateway 172.21.99.4
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
VLAN 10
name Administracion
VLAN 20
name Logistica
VLAN 30
name Finanzas
VLAN 40
name Marketing
VLAN 50
name Ventas
VLAN 60
name Servidores
VLAN 70
name WifiEjecutivo
VLAN 80
name WifiCliente
VLAN 99
name Management&Native
exit

! ASIGNACION DE VLAN A PCS
interface range f0/1-12
switchport mode access
switchport access vlan 40
!
interface range f0/13-24
switchport mode access
switchport access vlan 40
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
ip address 172.21.99.34 255.255.255.0
exit
ip default-gateway 172.21.99.4
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
VLAN 10
name Administracion
VLAN 20
name Logistica
VLAN 30
name Finanzas
VLAN 40
name Marketing
VLAN 50
name Ventas
VLAN 60
name Servidores
VLAN 70
name WifiEjecutivo
VLAN 80
name WifiCliente
VLAN 99
name Management&Native
exit

! ASIGNACION DE VLAN A PCS
interface range f0/1-12
switchport mode access
switchport access vlan 50
!
interface range f0/13-24
switchport mode access
switchport access vlan 50
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
ip address 172.21.99.35 255.255.255.0
exit
ip default-gateway 172.21.99.4
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
VLAN 10
name Administracion
VLAN 20
name Logistica
VLAN 30
name Finanzas
VLAN 40
name Marketing
VLAN 50
name Ventas
VLAN 60
name Servidores
VLAN 70
name WifiEjecutivo
VLAN 80
name WifiCliente
VLAN 99
name Management&Native
exit

! ASIGNACION DE VLAN A PCS
interface f0/1
switchport mode access
switchport access vlan 80
!
interface f0/2
switchport mode access
switchport access vlan 70
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
ip address 172.21.99.36 255.255.255.0
exit
ip default-gateway 172.21.99.4
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
VLAN 10
name Administracion
VLAN 20
name Logistica
VLAN 30
name Finanzas
VLAN 40
name Marketing
VLAN 50
name Ventas
VLAN 60
name Servidores
VLAN 70
name WifiEjecutivo
VLAN 80
name WifiCliente
VLAN 99
name Management&Native
exit

! ASIGNACION DE VLAN A PCS
interface range f0/1-12
switchport mode access
switchport access vlan 60
!
interface range f0/13-24
switchport mode access
switchport access vlan 60
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
ip address 172.21.99.37 255.255.255.0
exit
ip default-gateway 172.21.99.4
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
interface GigabitEthernet0/0/1
ip address 172.21.64.212 255.255.255.248

! ASIGNAR IP A ROUTERS DE OTRAS SEDES E ISP
interface Serial0/1/0
 ip address 172.22.254.14 255.255.255.252
 clock rate 2000000
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
VLAN 10
name Administracion
VLAN 20
name Logistica
VLAN 30
name Finanzas
VLAN 40
name Marketing
VLAN 50
name Ventas
VLAN 60
name Servidores
VLAN 70
name WifiEjecutivo
VLAN 80
name WifiCliente
VLAN 99
name Management&Native
exit

! ASIGNACION DE ENLACE TRUNCAL CON SWITCHES DE ACCESO
interface range GigabitEthernet1/0/11-17
switchport trunk native vlan 99
switchport mode trunk
switchport nonegotiate

! ASIGNAR IP MLS - ROUTER
interface GigabitEthernet1/0/1
no switchport
ip address 172.21.64.213 255.255.255.248

! DESHABILITAR VLAN1
interface Vlan1
no ip address
shutdown

! ASIGNAR IP DE LA MLS EN LAS VLANS
interface Vlan10
ip address 172.21.64.1 255.255.255.192
!
interface Vlan20
ip address 172.21.64.65 255.255.255.224
!
interface Vlan30
ip address 172.21.64.145 255.255.255.240
!
interface Vlan40
ip address 172.21.64.97 255.255.255.224
!
interface Vlan50
ip address 172.21.64.129 255.255.255.240
!
interface Vlan60
ip address 172.21.64.193 255.255.255.248
!
interface Vlan70
ip address 172.21.64.177 255.255.255.240
!
interface Vlan80
ip address 172.21.64.161 255.255.255.240
!
interface Vlan99
ip address 172.21.99.5 255.255.255.0
!
exit

! CREAR DHCP PARA CADA VLAN
ip dhcp pool POOL-VLAN10
network 172.21.64.0 255.255.255.192
default-router 172.21.64.1
!
ip dhcp pool POOL-VLAN20
network 172.21.64.64 255.255.255.224
default-router 172.21.64.65
!
ip dhcp pool POOL-VLAN30
network 172.21.64.144 255.255.255.240
default-router 172.21.64.145
!
ip dhcp pool POOL-VLAN40
network 172.21.64.96 255.255.255.224
default-router 172.21.64.97
!
ip dhcp pool POOL-VLAN50
network 172.21.64.128 255.255.255.240
default-router 172.21.64.129
!
ip dhcp pool POOL-VLAN60
network 172.21.64.192 255.255.255.248
default-router 172.21.64.193
!
ip dhcp pool POOL-VLAN70
network 172.21.64.176 255.255.255.240
default-router 172.21.64.177
!
ip dhcp pool POOL-VLAN80
network 172.21.64.160 255.255.255.240
default-router 172.21.64.161
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
VLAN 10
name Administracion
VLAN 20
name Logistica
VLAN 30
name Finanzas
VLAN 40
name Marketing
VLAN 50
name Ventas
VLAN 60
name Servidores
VLAN 70
name WifiEjecutivo
VLAN 80
name WifiCliente
VLAN 99
name Management&Native
exit

! ASIGNACION DE VLAN A PCS
interface range f0/1-12
switchport mode access
switchport access vlan 10
!
interface range f0/13-24
switchport mode access
switchport access vlan 10
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
ip address 172.21.99.41 255.255.255.0
exit
ip default-gateway 172.21.99.5
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
VLAN 10
name Administracion
VLAN 20
name Logistica
VLAN 30
name Finanzas
VLAN 40
name Marketing
VLAN 50
name Ventas
VLAN 60
name Servidores
VLAN 70
name WifiEjecutivo
VLAN 80
name WifiCliente
VLAN 99
name Management&Native
exit

! ASIGNACION DE VLAN A PCS
interface range f0/1-12
switchport mode access
switchport access vlan 20
!
interface range f0/13-24
switchport mode access
switchport access vlan 20
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
ip address 172.21.99.42 255.255.255.0
exit
ip default-gateway 172.21.99.5
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
VLAN 10
name Administracion
VLAN 20
name Logistica
VLAN 30
name Finanzas
VLAN 40
name Marketing
VLAN 50
name Ventas
VLAN 60
name Servidores
VLAN 70
name WifiEjecutivo
VLAN 80
name WifiCliente
VLAN 99
name Management&Native
exit

! ASIGNACION DE VLAN A PCS
interface range f0/1-12
switchport mode access
switchport access vlan 30
!
interface range f0/13-24
switchport mode access
switchport access vlan 30
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
ip address 172.21.99.43 255.255.255.0
exit
ip default-gateway 172.21.99.5
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
VLAN 10
name Administracion
VLAN 20
name Logistica
VLAN 30
name Finanzas
VLAN 40
name Marketing
VLAN 50
name Ventas
VLAN 60
name Servidores
VLAN 70
name WifiEjecutivo
VLAN 80
name WifiCliente
VLAN 99
name Management&Native
exit

! ASIGNACION DE VLAN A PCS
interface range f0/1-12
switchport mode access
switchport access vlan 40
!
interface range f0/13-24
switchport mode access
switchport access vlan 40
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
ip address 172.21.99.44 255.255.255.0
exit
ip default-gateway 172.21.99.5
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
VLAN 10
name Administracion
VLAN 20
name Logistica
VLAN 30
name Finanzas
VLAN 40
name Marketing
VLAN 50
name Ventas
VLAN 60
name Servidores
VLAN 70
name WifiEjecutivo
VLAN 80
name WifiCliente
VLAN 99
name Management&Native
exit

! ASIGNACION DE VLAN A PCS
interface range f0/1-12
switchport mode access
switchport access vlan 50
!
interface range f0/13-24
switchport mode access
switchport access vlan 50
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
ip address 172.21.99.45 255.255.255.0
exit
ip default-gateway 172.21.99.5
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
VLAN 10
name Administracion
VLAN 20
name Logistica
VLAN 30
name Finanzas
VLAN 40
name Marketing
VLAN 50
name Ventas
VLAN 60
name Servidores
VLAN 70
name WifiEjecutivo
VLAN 80
name WifiCliente
VLAN 99
name Management&Native
exit

! ASIGNACION DE VLAN A PCS
interface f0/1
switchport mode access
switchport access vlan 80
!
interface f0/2
switchport mode access
switchport access vlan 70
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
ip address 172.21.99.46 255.255.255.0
exit
ip default-gateway 172.21.99.5
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
VLAN 10
name Administracion
VLAN 20
name Logistica
VLAN 30
name Finanzas
VLAN 40
name Marketing
VLAN 50
name Ventas
VLAN 60
name Servidores
VLAN 70
name WifiEjecutivo
VLAN 80
name WifiCliente
VLAN 99
name Management&Native
exit

! ASIGNACION DE VLAN A PCS
interface range f0/1-12
switchport mode access
switchport access vlan 60
!
interface range f0/13-24
switchport mode access
switchport access vlan 60
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
ip address 172.21.99.47 255.255.255.0
exit
ip default-gateway 172.21.99.5
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
interface GigabitEthernet0/0/1
ip address 172.21.80.194 255.255.255.248

! ASIGNAR IP A ROUTERS DE OTRAS SEDES E ISP
interface Serial0/2/0
 ip address 172.22.254.18 255.255.255.252
 clock rate 2000000
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
VLAN 10
name Administracion
VLAN 20
name Logistica
VLAN 30
name Finanzas
VLAN 40
name Marketing
VLAN 50
name Ventas
VLAN 60
name Servidores
VLAN 70
name WifiEjecutivo
VLAN 80
name WifiCliente
VLAN 99
name Management&Native
exit

! ASIGNACION DE ENLACE TRUNCAL CON SWITCHES DE ACCESO
interface range GigabitEthernet1/0/11-17
switchport trunk native vlan 99
switchport mode trunk
switchport nonegotiate

! ASIGNAR IP MLS - ROUTER
interface GigabitEthernet1/0/1
no switchport
ip address 172.21.80.195 255.255.255.248

! DESHABILITAR VLAN1
interface Vlan1
no ip address
shutdown

! ASIGNAR IP DE LA MLS EN LAS VLANS
interface Vlan10
ip address 172.21.80.1 255.255.255.192
!
interface Vlan20
ip address 172.21.80.65 255.255.255.224
!
interface Vlan30
ip address 172.21.80.145 255.255.255.240
!
interface Vlan40
ip address 172.21.80.97 255.255.255.224
!
interface Vlan50
ip address 172.21.80.129 255.255.255.240
!
interface Vlan60
ip address 172.21.80.185 255.255.255.248
!
interface Vlan70
ip address 172.21.80.177 255.255.255.248
!
interface Vlan80
ip address 172.21.80.161 255.255.255.240
!
interface Vlan99
ip address 172.21.99.6 255.255.255.0
!
exit

! CREAR DHCP PARA CADA VLAN
ip dhcp pool POOL-VLAN10
network 172.21.80.0 255.255.255.192
default-router 172.21.80.1
!
ip dhcp pool POOL-VLAN20
network 172.21.80.64 255.255.255.224
default-router 172.21.80.65
!
ip dhcp pool POOL-VLAN30
network 172.21.80.144 255.255.255.240
default-router 172.21.80.145
!
ip dhcp pool POOL-VLAN40
network 172.21.80.96 255.255.255.224
default-router 172.21.80.97
!
ip dhcp pool POOL-VLAN50
network 172.21.80.128 255.255.255.240
default-router 172.21.80.129
!
ip dhcp pool POOL-VLAN60
network 172.21.80.184 255.255.255.248
default-router 172.21.80.185
!
ip dhcp pool POOL-VLAN70
network 172.21.80.176 255.255.255.248
default-router 172.21.64.177
!
ip dhcp pool POOL-VLAN80
network 172.21.80.160 255.255.255.240
default-router 172.21.64.161
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
VLAN 10
name Administracion
VLAN 20
name Logistica
VLAN 30
name Finanzas
VLAN 40
name Marketing
VLAN 50
name Ventas
VLAN 60
name Servidores
VLAN 70
name WifiEjecutivo
VLAN 80
name WifiCliente
VLAN 99
name Management&Native
exit

! ASIGNACION DE VLAN A PCS
interface range f0/1-12
switchport mode access
switchport access vlan 10
!
interface range f0/13-24
switchport mode access
switchport access vlan 10
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
ip address 172.21.99.51 255.255.255.0
exit
ip default-gateway 172.21.99.6
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
VLAN 10
name Administracion
VLAN 20
name Logistica
VLAN 30
name Finanzas
VLAN 40
name Marketing
VLAN 50
name Ventas
VLAN 60
name Servidores
VLAN 70
name WifiEjecutivo
VLAN 80
name WifiCliente
VLAN 99
name Management&Native
exit

! ASIGNACION DE VLAN A PCS
interface range f0/1-12
switchport mode access
switchport access vlan 20
!
interface range f0/13-24
switchport mode access
switchport access vlan 20
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
ip address 172.21.99.52 255.255.255.0
exit
ip default-gateway 172.21.99.6
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
VLAN 10
name Administracion
VLAN 20
name Logistica
VLAN 30
name Finanzas
VLAN 40
name Marketing
VLAN 50
name Ventas
VLAN 60
name Servidores
VLAN 70
name WifiEjecutivo
VLAN 80
name WifiCliente
VLAN 99
name Management&Native
exit

! ASIGNACION DE VLAN A PCS
interface range f0/1-12
switchport mode access
switchport access vlan 30
!
interface range f0/13-24
switchport mode access
switchport access vlan 30
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
ip address 172.21.99.53 255.255.255.0
exit
ip default-gateway 172.21.99.6
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
VLAN 10
name Administracion
VLAN 20
name Logistica
VLAN 30
name Finanzas
VLAN 40
name Marketing
VLAN 50
name Ventas
VLAN 60
name Servidores
VLAN 70
name WifiEjecutivo
VLAN 80
name WifiCliente
VLAN 99
name Management&Native
exit

! ASIGNACION DE VLAN A PCS
interface range f0/1-12
switchport mode access
switchport access vlan 40
!
interface range f0/13-24
switchport mode access
switchport access vlan 40
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
ip address 172.21.99.54 255.255.255.0
exit
ip default-gateway 172.21.99.6
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
VLAN 10
name Administracion
VLAN 20
name Logistica
VLAN 30
name Finanzas
VLAN 40
name Marketing
VLAN 50
name Ventas
VLAN 60
name Servidores
VLAN 70
name WifiEjecutivo
VLAN 80
name WifiCliente
VLAN 99
name Management&Native
exit

! ASIGNACION DE VLAN A PCS
interface range f0/1-12
switchport mode access
switchport access vlan 50
!
interface range f0/13-24
switchport mode access
switchport access vlan 50
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
ip address 172.21.99.55 255.255.255.0
exit
ip default-gateway 172.21.99.6
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
VLAN 10
name Administracion
VLAN 20
name Logistica
VLAN 30
name Finanzas
VLAN 40
name Marketing
VLAN 50
name Ventas
VLAN 60
name Servidores
VLAN 70
name WifiEjecutivo
VLAN 80
name WifiCliente
VLAN 99
name Management&Native
exit

! ASIGNACION DE VLAN A PCS
interface f0/1
switchport mode access
switchport access vlan 80
!
interface f0/2
switchport mode access
switchport access vlan 70
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
ip address 172.21.99.56 255.255.255.0
exit
ip default-gateway 172.21.99.6
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
VLAN 10
name Administracion
VLAN 20
name Logistica
VLAN 30
name Finanzas
VLAN 40
name Marketing
VLAN 50
name Ventas
VLAN 60
name Servidores
VLAN 70
name WifiEjecutivo
VLAN 80
name WifiCliente
VLAN 99
name Management&Native
exit

! ASIGNACION DE VLAN A PCS
interface range f0/1-12
switchport mode access
switchport access vlan 60
!
interface range f0/13-24
switchport mode access
switchport access vlan 60
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
ip address 172.21.99.57 255.255.255.0
exit
ip default-gateway 172.21.99.6
end

! GRABAR CONFIGURACION
write
write memory
copy running-config startup-config
```
