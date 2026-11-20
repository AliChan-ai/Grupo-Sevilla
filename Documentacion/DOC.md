# Proyecto intermodular — Grupo Sevilla



<div align="center">
  <img src="imagenes/pagina-1-imagen-4-Photoroom.png" alt="Redes y comunicaciones Sevilla" width="470" />
</div>

<div align="center">

**Grupo Sevilla SL**

</div>

**Curso:** 2026–2027  
**Ciclo:** 2.º ASIX  
**Fecha del documento:** 25 de septiembre de 2026  
**Repositorio:** [Grupo-Sevilla](https://github.com/AliChan-ai/Grupo-Sevilla)

---

## Índice

1. [Fase 2: Instalación y configuración de Proxmox y VLANs](#fase-2-instalación-y-configuración-de-proxmox-y-vlans)
   1. [Instalar y poner en funcionamiento Proxmox](#1-instalar-y-poner-en-funcionamiento-proxmox)
   2. [Direcciones IP y redes VLAN](#2-direcciones-ip-y-redes-vlan)
   3. [Conexión al Cisco 3560 mediante consola](#3-conexión-al-cisco-3560-mediante-consola)
   4. [Configuración de VLANs en el Cisco 3560](#4-configuración-de-vlans-en-el-cisco-3560)
2. [Fase 3: Despliegue de los equipos del laboratorio](#fase-3-despliegue-de-los-equipos-del-laboratorio)
   1. [Esquema de conexión en la VLAN 310](#1-esquema-de-conexión-en-la-vlan-310)
   2. [Requisitos de los equipos](#2-requisitos-de-los-equipos)
   3. [Tarea 1: Diseño del particionado](#3-tarea-1-diseño-del-particionado)
   4. [Tarea 2: Instalación del servidor](#4-tarea-2-instalación-del-servidor)
   5. [Tarea 3: Verificación después de la instalación](#5-tarea-3-verificación-después-de-la-instalación)
   6. [Instalación de los clientes Linux Mint](#6-instalación-de-los-clientes-linux-mint)
3. [Fase 4: Despliegue de servicios con Windows Server](#fase-4-despliegue-de-servicios-con-windows-server)
   1. [Tarea 1: Planificación de la infraestructura](#1-tarea-1-planificación-de-la-infraestructura)
   2. [Tarea 2: Instalación de Windows Server 2025](#2-tarea-2-instalación-de-windows-server-2025)
   3. [Tarea 3: Configuración del almacenamiento](#3-tarea-3-configuración-del-almacenamiento)
   4. [Tarea 4: Configuración TCP/IP del servidor](#4-tarea-4-configuración-tcpip-del-servidor)
   5. [Tarea 5: Servicio DHCP en Windows Server](#5-tarea-5-servicio-dhcp-en-windows-server)
   6. [Tarea 6: DHCP no autorizado y DHCP Snooping](#6-tarea-6-dhcp-no-autorizado-y-dhcp-snooping)

---

# Fase 2: Instalación y configuración de Proxmox y VLANs

Tenemos que preparar el servidor de la sede para poder virtualizar varios servidores y clientes. Para ello, utilizaremos Proxmox.

## 1. Instalar y poner en funcionamiento Proxmox

Primero, vamos a instalar Proxmox VE en nuestro servidor para poder crear y gestionar máquinas virtuales.

### Pasos para arrancar desde un USB

1. Conectamos el USB que contiene el instalador de Proxmox al servidor.
2. Encendemos o reiniciamos el servidor y entramos en la BIOS/UEFI pulsando la tecla correspondiente (normalmente F2, F12, DEL o ESC).
3. Entramos en el menú **Boot** y seleccionamos el USB como dispositivo de arranque.
4. Guardamos los cambios y reiniciamos el servidor si es necesario.
5. Cuando arranque desde el USB, aparecerá el menú de instalación de Proxmox.
6. Seleccionamos **Install Proxmox VE** para comenzar con la instalación.

Una vez iniciado el instalador de Proxmox, aparecerá el acuerdo de licencia (EULA). Lo leeremos y aceptaremos para poder continuar con la instalación.

<p align="center"><img src="imagenes/pagina-3-imagen-4.jpeg" alt="Instalador de Proxmox: acuerdo de licencia" width="520" /></p>

A continuación, el instalador nos pedirá configurar la cuenta de administrador. Tendremos que introducir un correo electrónico y crear una contraseña segura que utilizaremos para acceder a Proxmox. Es importante guardar estos datos para poder iniciar sesión más adelante.

<p align="center"><img src="imagenes/pagina-3-imagen-5.jpeg" alt="Configuración de la cuenta de administrador de Proxmox" width="500" /></p>

Después, configuraremos la dirección IP del servidor para que tenga conectividad con los otros ordenadores de la red. También indicaremos la máscara de red, la puerta de enlace y el servidor DNS para asegurarnos de que la conexión funcione correctamente.

<p align="center"><img src="imagenes/pagina-4-imagen-4.jpeg" alt="Configuración de red durante la instalación de Proxmox" width="520" /></p>

Por último, revisaremos el resumen de la configuración para comprobar que todos los datos sean correctos. Si todo está bien, pulsaremos **Install** para comenzar la instalación de Proxmox. Esperaremos a que termine y, una vez finalizada, reiniciaremos el servidor para empezar a utilizarlo.

<p align="center"><img src="imagenes/pagina-4-imagen-5.jpeg" alt="Resumen de la instalación de Proxmox" width="520" /></p>

## 2. Direcciones IP y redes VLAN

Según el diagrama, nuestra sede utiliza la red `10.3.0.0/16` y tiene las siguientes VLAN:

| VLAN | Departamento | Red |
|---:|---|---|
| 310 | Servidores | `10.3.10.0/24` |
| 320 | RRHH | `10.3.20.0/24` |
| 330 | Dirección | `10.3.30.0/24` |
| 399 | Gestión | `10.3.99.0/24` |

## 3. Conexión al Cisco 3560 mediante consola

Primero, tendremos que instalar PuTTY en nuestro ordenador cliente. Este programa nos permitirá conectarnos al switch Cisco 3560 mediante una conexión por consola y ejecutar los comandos necesarios para configurarlo.

<p align="center"><img src="imagenes/pagina-4-imagen-6.jpeg" alt="Terminal utilizada durante la preparación de PuTTY" width="520" /></p>

Antes de conectar el switch Cisco 3560 al ordenador, identificaremos dónde se encuentra el puerto serial o de consola. Para ello, utilizaremos la imagen de referencia incluida en las capturas para localizar el puerto correcto y conectar el cable de consola.

<p align="center"><img src="imagenes/pagina-5-imagen-5.jpeg" alt="Comprobación del puerto serie en el equipo" width="500" /></p>

Una vez instalado PuTTY, conectaremos el cable de consola entre el ordenador y el switch. Después, abriremos el programa, seleccionaremos el tipo de conexión **Serial** e indicaremos el puerto COM correspondiente.

<p align="center"><img src="imagenes/pagina-5-imagen-4.jpeg" alt="Configuración de la conexión Serial en PuTTY" width="500" /></p>

Una vez conectado el cable y configurados los parámetros en PuTTY, pulsaremos **Open** para establecer la conexión. Si todo está correcto, podremos acceder a la consola del switch Cisco 3560 y empezar a introducir los comandos necesarios para su configuración.

<p align="center"><img src="imagenes/pagina-5-imagen-6.jpeg" alt="Consola del switch Cisco 3560" width="520" /></p>

## 4. Configuración de VLANs en el Cisco 3560

Primero, entraremos en el modo de configuración del switch utilizando el comando:

```text
conf t
```

Luego, crearemos las VLAN necesarias y asignaremos un nombre a cada una para identificar los diferentes departamentos de la sede de Sevilla.

<p align="center"><img src="imagenes/pagina-5-imagen-7.jpeg" alt="Comandos para crear y nombrar las VLAN" width="520" /></p>

A continuación, veremos las interfaces disponibles en el switch Cisco 3560. Esta información nos permitirá identificar los puertos que utilizaremos para conectar el servidor Proxmox y los futuros equipos, y así poder asignar cada puerto a la VLAN correspondiente.

<p align="center"><img src="imagenes/pagina-5-imagen-8.jpeg" alt="Listado de VLANs y estado de los puertos" width="520" /></p>

En esta imagen podemos ver la configuración de PuTTY que utilizamos para conectarnos al switch Cisco 3560 mediante el puerto serial. Seleccionamos el tipo de conexión **Serial**, indicamos el puerto `/dev/ttyUSB0` y establecemos una velocidad de **9600 baudios**.

<p align="center"><img src="imagenes/pagina-6-imagen-4.jpeg" alt="Parámetros de conexión de PuTTY" width="500" /></p>

Una vez configurados estos parámetros, pulsamos **Open** para acceder a la consola del switch. Desde allí, continuamos con la configuración de las VLAN que habíamos creado anteriormente y añadimos las que todavía faltaban.

<p align="center"><img src="imagenes/pagina-6-imagen-5.jpeg" alt="Configuración de las VLAN en el switch Cisco 3560" width="520" /></p>

Una vez conectados al switch Cisco 3560 mediante PuTTY, configuramos las VLAN necesarias para la sede de Sevilla. Para ello, entramos en el modo de configuración global con el comando `conf t` y creamos cada VLAN con su número, nombre y red IP correspondiente:

- **VLAN 310 — Servidores:** `10.3.10.0/24`
- **VLAN 320 — RRHH:** `10.3.20.0/24`
- **VLAN 330 — Dirección:** `10.3.30.0/24`
- **VLAN 399 — Gestión:** `10.3.99.0/24`

> **Nota:** las redes indicadas documentan el esquema IP de cada VLAN. La creación de una VLAN en el switch no configura por sí sola una dirección IP ni el enrutamiento de esa red.

---

# Fase 3: Despliegue de los equipos del laboratorio

Esta fase corresponde a la **Práctica 3: Despliegue de equipos del laboratorio**. Con Proxmox instalado y las VLAN configuradas en el switch, desplegamos los equipos del laboratorio del grupo:

1. Un **servidor virtualizado** con Ubuntu Server LTS, instalado en modo UEFI y con el sistema, el arranque, los datos de los usuarios y la swap en discos separados.
2. Un **cliente Linux virtualizado** con Linux Mint.
3. Un **cliente Linux físico** con Linux Mint, instalado en el equipo asignado al grupo.

### ¿Por qué separamos el sistema en varios discos?

Siguiendo las recomendaciones de LPIC-1 (objetivo 102.1), instalar Linux en un único disco o en una única partición raíz (`/`) es una mala práctica en un servidor de producción. Separar el árbol de directorios en discos y particiones independientes aporta:

- **Aislamiento de fallos:** si `/home` o `/var` se llenan, la raíz (`/`) sigue funcionando y el sistema no se bloquea por falta de espacio.
- **Rendimiento y tolerancia a fallos:** cada directorio puede estar en un disco de tecnología distinta o en RAID (por ejemplo, el sistema en SSD y los datos de usuarios en discos de mayor capacidad).
- **Seguridad:** cada partición se puede montar en `/etc/fstab` con opciones restrictivas como `noexec`, `nosuid` o `nodev`.
- **Actualizaciones independientes:** se puede reinstalar o migrar el sistema en `/` sin tocar los datos de los usuarios en `/home`.

## 1. Esquema de conexión en la VLAN 310

Antes de entrar en Proxmox, planificamos en la pizarra cómo se conectan los equipos dentro de la VLAN 310 (Servidores, red `10.3.10.0/24`):

| Equipo | Puerto del 3560 | Modo | Dirección IP |
|---|---|---|---|
| Switch Cisco 3560 (interfaz de la VLAN 310) | — | — | `10.3.10.1` |
| Servidor Proxmox | `0/22` | Access, VLAN 310 | `10.3.10.2/24` |
| Ordenador cliente (host PC) | `0/23` | Access, VLAN 310 | `10.3.10.3/24` |

Los dos puertos se configuran en modo **access** dentro de la VLAN 310, de modo que el servidor Proxmox y el ordenador cliente quedan en la misma red y pueden comunicarse. Dentro de Proxmox se alojarán las máquinas virtuales de los servidores (S1, S2…).

<p align="center"><img src="imagenes/01_Esquema_red_pizarra.jpeg" alt="Esquema en la pizarra de la VLAN 310 con el switch 3560, Proxmox y el PC cliente" width="560" /></p>

## 2. Requisitos de los equipos

| Equipo | Sistema operativo | Firmware | Almacenamiento |
|---|---|---|---|
| Servidor (virtual, en Proxmox) | Ubuntu Server LTS (24.04.1) | UEFI obligatorio | 4 discos independientes |
| Cliente virtual (en Proxmox) | Linux Mint | UEFI o BIOS | 1 disco, particionado estándar |
| Cliente físico (equipo del grupo) | Linux Mint | UEFI o BIOS | 1 disco, particionado estándar |

## 3. Tarea 1: Diseño del particionado

Antes de iniciar el instalador, diseñamos el particionado del servidor. En Proxmox los discos de la máquina se conectan al controlador VirtIO SCSI, así que dentro de Ubuntu aparecen como `/dev/sda`, `/dev/sdb`, `/dev/sdc` y `/dev/sdd`.

| Disco | Disco en Proxmox | Dispositivo en Ubuntu | Tamaño del disco | Partición | Tamaño | Sistema de ficheros | Punto de montaje | Uso |
|---|---|---|---|---|---|---|---|---|
| Disco 1 | `scsi0` | `/dev/sda` | 1,5 GB | `/dev/sda1` | 538 MB | vfat (FAT32) | `/boot/efi` | Partición de sistema EFI, al principio del disco |
| Disco 1 | `scsi0` | `/dev/sda` | 1,5 GB | `/dev/sda2` | Resto (986 MB) | ext4 | `/boot` | Núcleo Linux e initramfs |
| Disco 2 | `scsi1` | `/dev/sdb` | 10 GB | `/dev/sdb1` | 10 GB (todo el disco) | swap | — | Memoria de intercambio |
| Disco 3 | `scsi2` | `/dev/sdc` | 20 GB | `/dev/sdc1` | 20 GB (todo el disco) | ext4 | `/` | Sistema base |
| Disco 4 | `scsi3` | `/dev/sdd` | 30 GB | `/dev/sdd1` | 30 GB (todo el disco) | ext4 | `/home` | Datos de los usuarios |

> Los nombres `/dev/sdX` siguen el orden de los discos en Proxmox; se confirman con `lsblk` en la Tarea 3.

**Justificación del diseño:**

- **`/boot/efi` en vfat:** el firmware UEFI solo sabe leer particiones FAT, por eso la partición EFI tiene que ser FAT32 y estar al principio del disco 1.
- **`/boot` en ext4:** solo guarda el núcleo y el initramfs, que son ficheros estáticos. Tenerlo aparte asegura que el sistema pueda arrancar aunque haya problemas en `/`.
- **`/` y `/home` en ext4:** ext4 es el sistema de ficheros por defecto de Ubuntu, es estable y tiene journaling. Cada uno ocupa su disco entero, así que los datos de los usuarios quedan aislados del sistema.
- **swap en un disco propio:** la memoria de intercambio no compite por espacio ni por accesos con el sistema ni con los datos.

> **Nota sobre el disco 1:** la práctica pide un disco de 1 GB con la partición EFI (mínimo recomendado de 512 MB) y `/boot` (1 GB). Las dos juntas no caben en 1 GB, así que creamos el disco 1 con **1,5 GB**: 538 MB para la partición EFI y el resto (986 MB, prácticamente 1 GB) para `/boot`.

Los clientes Linux Mint se instalan con el **particionado estándar** que propone el instalador, en un único disco.

## 4. Tarea 2: Instalación del servidor

### 4.1. Acceso a la interfaz web de Proxmox

Desde el ordenador cliente abrimos el navegador y accedemos a la interfaz web de Proxmox escribiendo la IP del servidor y el puerto `8006`:

```text
https://10.3.10.2:8006
```

El navegador mostrará un aviso de certificado, porque Proxmox utiliza un certificado autofirmado. Lo aceptamos para continuar. En la pantalla de inicio de sesión introducimos el usuario `root`, la contraseña que definimos durante la instalación y el dominio **Linux PAM standard authentication**, y pulsamos **Login**.

<p align="center"><img src="imagenes/02_Login_Proxmox.jpeg" alt="Pantalla de inicio de sesión de Proxmox" width="420" /></p>

Una vez dentro, vemos el panel **Datacenter** con nuestro nodo `gruposevilla` y sus almacenamientos: `local` (ISO, plantillas y copias de seguridad) y `local-lvm` (discos de las máquinas virtuales).

<p align="center"><img src="imagenes/03_Panel_Datacenter_inicial.jpeg" alt="Panel Datacenter de Proxmox con el nodo gruposevilla" width="520" /></p>

### 4.2. Subir la imagen ISO de Ubuntu Server

Para instalar un sistema operativo en una máquina virtual necesitamos su imagen ISO dentro de Proxmox. Para ello, seleccionamos el almacenamiento **local (gruposevilla)**, entramos en **ISO Images** y pulsamos **Upload**. Elegimos el archivo `ubuntu-24.04.1-live-server-amd64.iso` desde el ordenador y pulsamos **Upload**.

<p align="center"><img src="imagenes/04_Subir_ISO_Ubuntu.jpeg" alt="Ventana para subir la ISO de Ubuntu Server a Proxmox" width="420" /></p>

Cuando termina la subida, la ISO aparece en la lista de **ISO Images** del almacenamiento `local`, con un tamaño de 2,77 GB.

<p align="center"><img src="imagenes/05_ISO_subida_en_storage_local.jpeg" alt="ISO de Ubuntu 24.04.1 subida al almacenamiento local" width="520" /></p>

### 4.3. Crear la máquina virtual

Pulsamos el botón **Create VM**, en la parte superior derecha, para abrir el asistente de creación de máquinas virtuales. Lo recorremos pestaña a pestaña.

**General.** Indicamos el nodo `gruposevilla`, el identificador de la máquina (**VM ID** `100`) y su nombre, `Ubuntuserver`.

<p align="center"><img src="imagenes/06_Crear_VM_General.jpeg" alt="Asistente Create VM: pestaña General" width="520" /></p>

**OS.** Marcamos **Use CD/DVD disc image file (iso)**, seleccionamos el almacenamiento `local` y la ISO `ubuntu-24.04.1-live-server-amd64.iso`. Como sistema invitado dejamos **Linux** con la versión **6.x – 2.6 Kernel**.

<p align="center"><img src="imagenes/07_Crear_VM_OS.jpeg" alt="Asistente Create VM: pestaña OS" width="520" /></p>

**System.** Dejamos la tarjeta gráfica y el tipo de máquina por defecto y el controlador SCSI **VirtIO SCSI single**. Como BIOS elegimos **OVMF (UEFI)**, marcamos **Add EFI Disk** y guardamos el disco EFI en `local-lvm`. Con esto la máquina arranca en **modo UEFI**, como exige la práctica. El disco EFI de Proxmox solo guarda las variables del firmware y no es uno de los cuatro discos del diseño.

<p align="center"><img src="imagenes/08_Crear_VM_System.jpeg" alt="Asistente Create VM: pestaña System" width="520" /></p>

**Disks.** En el asistente se creó un primer disco, `scsi0`, en el almacenamiento `local-lvm` con un tamaño de **32 GB** y sin caché. Este disco no cumple el diseño de la Tarea 1, así que después lo sustituimos por los cuatro discos de la práctica (apartado 4.5).

<p align="center"><img src="imagenes/09_Crear_VM_Disks.jpeg" alt="Asistente Create VM: pestaña Disks" width="520" /></p>

**CPU y Memory.** Asignamos 1 socket con 1 núcleo (tipo de CPU `x86-64-v2-AES`) y **2048 MB** de memoria RAM. Estos valores se pueden ver en el resumen final.

**Network.** Conectamos la tarjeta de red al puente `vmbr0`, que es la interfaz de red del servidor Proxmox, con el modelo **VirtIO (paravirtualized)** y el cortafuegos activado. Así, la máquina virtual sale a la red por el mismo puerto que el servidor.

<p align="center"><img src="imagenes/10_Crear_VM_Network.jpeg" alt="Asistente Create VM: pestaña Network" width="520" /></p>

**Confirm.** Revisamos el resumen de la configuración y pulsamos **Finish** para crear la máquina:

| Parámetro | Valor |
|---|---|
| `vmid` / `name` | `100` / `Ubuntuserver` |
| `nodename` | `gruposevilla` |
| `sockets` / `cores` / `cpu` | `1` / `1` / `x86-64-v2-AES` |
| `memory` | `2048` MB |
| `ostype` | `l26` (Linux) |
| `ide2` | `local:iso/ubuntu-24.04.1-live-server-amd64.iso,media=cdrom` |
| `scsi0` | `local-lvm:32,iothread=on` |
| `scsihw` | `virtio-scsi-single` |
| `efidisk0` | `local-lvm:1,efitype=4m,pre-enrolled-keys=1` |
| `net0` | `virtio,bridge=vmbr0,firewall=1` |

<p align="center"><img src="imagenes/11_Crear_VM_Confirm.jpeg" alt="Asistente Create VM: resumen de la configuración" width="520" /></p>

### 4.4. Comprobación de la máquina virtual

Al volver al panel **Datacenter**, la nueva máquina virtual aparece en la lista de recursos como **100 (Ubuntuserver)**, dentro del nodo `gruposevilla`.

<p align="center"><img src="imagenes/12_VM_100_Ubuntuserver_creada.jpeg" alt="Panel Datacenter con la máquina virtual 100 (Ubuntuserver) creada" width="520" /></p>

### 4.5. Añadir los cuatro discos del diseño

Con la máquina apagada, seleccionamos **100 (Ubuntuserver) → Hardware** y ajustamos los discos al diseño de la Tarea 1:

1. Seleccionamos el disco de 32 GB (`scsi0`), pulsamos **Detach** y después **Remove** para eliminarlo.
2. Pulsamos **Add → Hard Disk** y creamos los cuatro discos en `local-lvm`, con el bus **SCSI**:

| Disco | Bus/Device | Tamaño |
|---|---|---|
| Disco 1 — arranque y EFI | `scsi0` | 1,5 GB |
| Disco 2 — swap | `scsi1` | 10 GB |
| Disco 3 — sistema base | `scsi2` | 20 GB |
| Disco 4 — datos de usuarios | `scsi3` | 30 GB |

3. En **Options → Boot Order** comprobamos que la máquina arranca primero desde el CD (`ide2`) para iniciar el instalador.

Todos los discos usan el controlador **VirtIO SCSI single**, el almacenamiento `local-lvm` y la caché por defecto (**No cache**).

<p align="center"><img src="imagenes/P3_01_Proxmox_disco1_scsi0_1.5GB_EFI_boot.jpg" alt="Disco scsi0 de 1,5 GB para EFI y /boot" width="420" /> <img src="imagenes/P3_02_Proxmox_disco2_scsi1_10GB_swap.jpg" alt="Disco scsi1 de 10 GB para la swap" width="420" /></p>
<p align="center"><img src="imagenes/P3_03_Proxmox_disco3_scsi2_20GB_raiz.jpg" alt="Disco scsi2 de 20 GB para la raíz" width="420" /> <img src="imagenes/P3_04_Proxmox_disco4_scsi3_30GB_home.jpg" alt="Disco scsi3 de 30 GB para /home" width="420" /></p>

### 4.6. Instalación de Ubuntu Server con particionado manual

1. Iniciamos la máquina (**Start**) y abrimos la **Console**. La máquina arranca en modo UEFI desde la ISO de Ubuntu Server.
2. Seguimos el instalador (idioma, teclado, red) hasta llegar a **Guided storage configuration**.
3. Elegimos **Custom storage layout**, que es el particionado manual. No usamos el particionado automático.

<p align="center"><img src="imagenes/P3_05_Instalador_custom_storage_layout.jpg" alt="Opción Custom storage layout en el instalador" width="520" /></p>

4. Creamos las particiones en cada disco tal como las diseñamos en la Tarea 1:
   - En `scsi0` (1,5 GB): marcamos el disco como **Use as Boot Device**, lo que crea la partición EFI (`/boot/efi`, fat32), y en el espacio libre que queda añadimos una partición ext4 montada en `/boot`.

<p align="center"><img src="imagenes/P3_06_Particion_boot_en_scsi0.jpg" alt="Creación de la partición /boot en ext4 en el disco scsi0" width="480" /></p>

   - En `scsi1` (10 GB): una partición que ocupa todo el disco, con formato **swap**.

<p align="center"><img src="imagenes/P3_07_Particion_swap_en_scsi1.jpg" alt="Creación de la partición swap en el disco scsi1" width="480" /></p>

   - En `scsi2` (20 GB): una partición ext4 que ocupa todo el disco, montada en `/`.
   - En `scsi3` (30 GB): una partición ext4 que ocupa todo el disco, montada en `/home`.

5. Revisamos el resumen. En **Dispositivos utilizados** se ve que cada punto de montaje está en su propio disco: `/boot/efi` (fat32) y `/boot` (ext4) en `scsi0`, la swap en `scsi1`, `/` en `scsi2` y `/home` en `scsi3`.

<p align="center"><img src="imagenes/P3_08_Resumen_final_particionado.jpg" alt="Resumen final del particionado manual con los cuatro discos" width="560" /></p>

6. Confirmamos y completamos la instalación. En **Profile configuration** indicamos el nombre `Sevilla`, el nombre del servidor `sevilla` y el usuario `grupo-s`.

<p align="center"><img src="imagenes/P3_09_Configuracion_perfil_usuario.jpg" alt="Configuración del perfil de usuario en el instalador" width="520" /></p>

7. Al terminar, quitamos la ISO de la máquina (**Hardware → CD/DVD Drive → Do not use any media**) y reiniciamos. El sistema arranca solo desde el disco y podemos iniciar sesión con `grupo-s`. En el mensaje de bienvenida ya se ve que `/home` tiene su propio espacio (29,36 GB).

<p align="center"><img src="imagenes/P3_10_Login_tras_reinicio_home_29GB.jpg" alt="Inicio de sesión en Ubuntu Server tras reiniciar" width="520" /></p>

## 5. Tarea 3: Verificación después de la instalación

Una vez que el sistema arranca, entramos en la terminal y ejecutamos las siguientes comprobaciones.

**1. El sistema ha arrancado en modo UEFI.** Si existe el directorio `/sys/firmware/efi`, el sistema se ha iniciado en modo UEFI:

```bash
ls /sys/firmware/efi
[ -d /sys/firmware/efi ] && echo "Arranque en modo UEFI" || echo "Arranque en modo BIOS"
```

En la salida de `df -h` (punto 2) ya aparece montado el sistema de ficheros `efivarfs` en `/sys/firmware/efi/efivars`. Este sistema de ficheros solo existe cuando el equipo ha arrancado en modo UEFI, así que es una prueba de que el servidor arranca en UEFI.

<p align="center"><img src="imagenes/P3_13_df_h_servidor_UEFI.jpg" alt="Salida de lsblk -f y df -h en el servidor, con efivarfs montado en /sys/firmware/efi/efivars" width="560" /></p>

> **Pendiente:** captura de `ls /sys/firmware/efi` para tener también la comprobación directa del directorio.

**2. Cada punto de montaje está en su disco.** Con `lsblk` vemos qué partición y qué disco físico corresponden a `/`, `/home`, `/boot`, `/boot/efi` y la swap. Con `df -h` vemos el tamaño y el espacio usado de cada punto de montaje:

```bash
lsblk -f
df -h
```

<p align="center"><img src="imagenes/P3_12_lsblk_f_servidor.jpg" alt="Salida de lsblk -f en el servidor con los cuatro discos y sus puntos de montaje" width="680" /></p>

La salida de `lsblk -f` confirma que cada punto de montaje está en su propio disco, tal como lo diseñamos en la Tarea 1:

| Disco | Partición | Sistema de ficheros | UUID | Punto de montaje | Tamaño (`df -h`) |
|---|---|---|---|---|---|
| `sda` | `sda1` | vfat (FAT32) | `1045-05B4` | `/boot/efi` | 537 MB |
| `sda` | `sda2` | ext4 | `f0da3466-75a6-4f46-a4b1-5f574c23c612` | `/boot` | 953 MB |
| `sdb` | `sdb1` | swap | `8dcca519-fcfa-46b3-abcd-4a4495525766` | `[SWAP]` | 10 GB |
| `sdc` | `sdc1` | ext4 | `fc93c48c-fed6-4172-a2d4-862199f5b299` | `/` | 20 GB |
| `sdd` | `sdd1` | ext4 | `faa07fe5-54ab-45ec-b050-407ee3add9d1` | `/home` | 30 GB |

También comprobamos que la swap del disco 2 está activa con `free -h` y `swapon --show`: el sistema dispone de 9 GiB de swap en la partición `/dev/sdb1`.

```bash
free -h
swapon --show
```

<p align="center"><img src="imagenes/P3_14_free_swapon_servidor.jpg" alt="Salida de free -h y swapon --show con la swap en /dev/sdb1" width="560" /></p>

> **Nota:** en `lsblk` aparece todavía `sr0` con la ISO de Ubuntu Server. Hay que quitarla de la máquina (**Hardware → CD/DVD Drive → Do not use any media**) para que no quede ningún medio de instalación conectado.

**3. Montaje automático por UUID.** El fichero `/etc/fstab` tiene que montar todos los puntos críticos al arrancar, identificando cada partición por su UUID:

```bash
cat /etc/fstab
sudo blkid
```

Con `blkid` podemos comparar los UUID de `/etc/fstab` con los de cada partición.

> **Pendiente:** captura del resultado.

## 6. Instalación de los clientes Linux Mint

### 6.1. Cliente virtualizado

1. Subimos la ISO de Linux Mint al almacenamiento `local` de Proxmox, igual que hicimos con la de Ubuntu Server (apartado 4.2). La imagen `linuxmint-22.3-cinnamon-64bit.iso` (3,09 GB) aparece en **ISO Images** junto a la de Ubuntu Server.

<p align="center"><img src="imagenes/P3_15_ISO_LinuxMint_en_Proxmox.jpg" alt="ISO de Linux Mint 22.3 subida al almacenamiento local de Proxmox" width="520" /></p>

2. Creamos una nueva máquina virtual con **Create VM** (VM 102). En la pestaña **OS** seleccionamos la ISO de Linux Mint y como sistema invitado **Linux 6.x – 2.6 Kernel**.

<p align="center"><img src="imagenes/P3_16_CrearVM_Mint_OS.jpg" alt="Asistente Create VM: pestaña OS con la ISO de Linux Mint" width="480" /></p>

3. En la pestaña **System** elegimos BIOS **OVMF (UEFI)** con disco EFI en `local-lvm` y el controlador **VirtIO SCSI single**. Después le asignamos un único disco y la tarjeta de red en el puente `vmbr0`.

<p align="center"><img src="imagenes/P3_17_CrearVM_Mint_System_UEFI.jpg" alt="Asistente Create VM: pestaña System con OVMF (UEFI)" width="420" /></p>

4. Arrancamos la máquina desde la ISO. Se inicia la sesión en vivo de **Linux Mint 22.3 Cinnamon**, y desde el icono del escritorio lanzamos el instalador.

<p align="center"><img src="imagenes/P3_18_Mint_live_escritorio.jpg" alt="Escritorio de la sesión en vivo de Linux Mint 22.3" width="440" /></p>

5. Instalamos Linux Mint con la opción de particionado estándar del instalador (**Borrar disco e instalar Linux Mint**), que usa el único disco de la máquina.

<p align="center"><img src="imagenes/P3_19_Mint_instalando.jpg" alt="Instalador de Linux Mint copiando archivos" width="440" /></p>

6. Al terminar, quitamos la ISO y reiniciamos. La máquina arranca desde el disco y muestra la pantalla de inicio de sesión del usuario `sevilla`.

<p align="center"><img src="imagenes/P3_20_Mint_login_instalado.jpg" alt="Pantalla de inicio de sesión de Linux Mint ya instalado" width="440" /></p>

> **Pendiente:** captura de los pasos del instalador donde se elige el particionado estándar.

### 6.2. Cliente físico

1. Arrancamos el equipo físico del grupo desde un USB con Linux Mint (igual que en la Fase 2, apartado 1).
2. Instalamos Linux Mint en el único disco del equipo con el particionado estándar.
3. Conectamos el equipo a un puerto de la VLAN 310 del switch (por ejemplo, `0/23`, con la IP `10.3.10.3/24`) para que llegue al servidor y a la interfaz de Proxmox.

> **Pendiente:** capturas de la instalación y de la conectividad con el servidor.

---

# Fase 4: Despliegue de servicios con Windows Server

Esta fase corresponde a la **Práctica 4: Despliegue de servicios con Windows Server**. La dirección de la empresa quiere centralizar la autenticación de los usuarios, la administración de los equipos, los recursos compartidos y las copias de seguridad. Para ello desplegamos un servidor **Windows Server 2025** en Proxmox que, además, asignará automáticamente la configuración IP a los clientes mediante **DHCP**. Para proteger la red frente a servidores DHCP no autorizados, activaremos **DHCP Snooping** en el switch Cisco 3560.

## 1. Tarea 1: Planificación de la infraestructura

### 1.1. Características de la máquina virtual

| Componente | Configuración requerida |
|---|---|
| Sistema operativo | Windows Server 2025 |
| Firmware | UEFI (OVMF) |
| Disco 0 | 60 GB (sistema operativo) |
| Disco 1 | 50 GB (datos) |
| Disco 2 | 50 GB (datos) |
| Disco 3 | 20 GB (copias de seguridad) |
| Memoria RAM | 8 GB mínimo |
| Procesadores | 2 vCPU |
| Adaptador de red | Puente `vmbr0` (red del laboratorio) |
| Nombre del servidor | `WS2025-ASIX333` (según el nombre que aparece en Proxmox) |

Antes de instalar comprobamos que la máquina utiliza UEFI y que tiene conectados los cuatro discos virtuales. En Proxmox seleccionamos **OVMF (UEFI)** y el almacenamiento `local-lvm` para el disco EFI.

<p align="center"><img src="imagenes/WhatsApp Image 2026-11-13 at 09.26.03.jpeg" alt="Configuración del sistema de la máquina virtual con firmware OVMF (UEFI)" width="560" /></p>

**Aquí lo que hemos hecho es seleccionar OVMF (UEFI) como firmware de la máquina virtual y añadir el disco EFI en Proxmox.** De esta forma, la máquina queda preparada para iniciar Windows Server utilizando UEFI.

En la pestaña **Disks** añadimos los cuatro discos virtuales mediante el controlador **VirtIO SCSI single**. Los tamaños configurados son 60 GB, 50 GB, 50 GB y 20 GB.

<p align="center"><img src="imagenes/WhatsApp Image 2026-11-13 at 09.28.39.jpeg" alt="Configuración del disco scsi0 de 60 GB en Proxmox" width="500" /></p>

**Aquí lo que hemos hecho es configurar el primer disco virtual (`scsi0`) con una capacidad de 60 GB, que se utilizará para instalar el sistema operativo.**

<p align="center"><img src="imagenes/WhatsApp Image 2026-11-13 at 09.28.45.jpeg" alt="Configuración del disco scsi1 de 50 GB en Proxmox" width="500" /></p>

**Aquí lo que hemos hecho es configurar el segundo disco virtual (`scsi1`) con 50 GB de capacidad para almacenar datos.**

<p align="center"><img src="imagenes/WhatsApp Image 2026-11-13 at 09.28.49.jpeg" alt="Configuración del disco scsi2 de 50 GB en Proxmox" width="500" /></p>

**Aquí lo que hemos hecho es configurar el tercer disco virtual (`scsi2`) con otros 50 GB, destinado a los datos departamentales.**

<p align="center"><img src="imagenes/WhatsApp Image 2026-11-13 at 09.28.54.jpeg" alt="Configuración del disco scsi3 de 20 GB en Proxmox" width="500" /></p>

**Aquí lo que hemos hecho es configurar el cuarto disco virtual (`scsi3`) con 20 GB, que utilizaremos para guardar las copias de seguridad.**

### 1.2. Esquema de discos y volúmenes

| Disco | Tamaño | Volumen | Etiqueta | Finalidad |
|---|---|---|---|---|
| Disco 0 | 60 GB | `C:` | Sistema | Sistema operativo |
| Disco 1 | 50 GB | `D:` | Recursos_Compartidos | Recursos compartidos corporativos |
| Disco 2 | 50 GB | `E:` | datos_departamentales | Datos departamentales |
| Disco 3 | 20 GB | `F:` | copias_de_seguridad | Copias de seguridad |

Separamos el sistema, los datos y las copias en discos distintos para que un problema en uno no afecte a los demás y para que las copias de seguridad no estén en el mismo disco que los datos que protegen.

### 1.3. Esquema de red (propuesta)

El servidor se conecta a la VLAN 310 (Servidores, `10.3.10.0/24`), igual que el resto de equipos del laboratorio.

| Equipo | Dirección IP | Configuración |
|---|---|---|
| Switch Cisco 3560 (puerta de enlace) | `10.3.10.1` | Estática |
| Servidor Proxmox | `10.3.10.2/24` | Estática |
| Cliente físico | `10.3.10.3/24` | Dirección fija o reserva DHCP (pendiente de confirmar) |
| Servidor Windows Server 2025 | `10.3.10.10/24` | Estática propuesta |
| Clientes | `10.3.10.100` – `10.3.10.200` | DHCP propuesto |

> **Importante:** en la captura de configuración de red de Windows Server aparece la dirección `10.3.10.100`, no la `10.3.10.10` de esta propuesta. Como `.100` también aparece dentro del rango DHCP propuesto, hay que confirmar qué dirección tendrá finalmente el servidor y evitar que una IP fija quede dentro del conjunto que se entrega dinámicamente.

### 1.4. Nombre del servidor y del dominio

- **Servidor:** `WS2025-ASIX333`, que es el nombre que aparece en la consola de Proxmox.
- **Dominio:** `sevilla.local` (propuesta inicial).

> **Pendiente:** confirmar el nombre definitivo del dominio. En una captura posterior del asistente DHCP aparece `sevilla.com`, por lo que hay que decidir cuál se utilizará y mantener el mismo nombre en toda la configuración.

## 2. Tarea 2: Instalación de Windows Server 2025

1. Creamos la máquina en Proxmox con BIOS **OVMF (UEFI)**, disco EFI y los cuatro discos de la tabla anterior.
2. Arrancamos desde la ISO y llegamos a la pantalla de selección del disco donde se instalará Windows Server 2025. En esa pantalla aparecen los cuatro discos virtuales con capacidades de 20 GB, 60 GB, 50 GB y 50 GB. Seleccionamos el disco de **60 GB** para instalar el sistema operativo.

<p align="center"><img src="imagenes/WhatsApp Image 2026-11-13 at 10.07.23.jpeg" alt="Selección del disco de 60 GB para instalar Windows Server 2025" width="560" /></p>

**Aquí lo que hemos hecho es seleccionar el disco virtual de 60 GB para instalar Windows Server.** En la pantalla se ven también los otros tres discos, que se reservarán para los datos y las copias de seguridad.

3. Una vez finalizada la instalación, Windows solicita establecer la contraseña de la cuenta **Administrador**.

<p align="center"><img src="imagenes/WhatsApp Image 2026-11-13 at 10.22.59.jpeg" alt="Configuración de la contraseña de la cuenta Administrador de Windows Server" width="560" /></p>

**Aquí lo que hemos hecho es establecer la contraseña de la cuenta Administrador para poder iniciar sesión en Windows Server una vez terminada la instalación.**

> **Pendiente:** añadir una captura del inicio de sesión del servidor y otra de `msinfo32` donde se vea que el modo de BIOS es UEFI. La captura de Proxmox demuestra que se seleccionó OVMF, pero conviene documentar también la comprobación desde Windows.

## 3. Tarea 3: Configuración del almacenamiento

En **Administración de discos** (`diskmgmt.msc`) inicializamos los discos secundarios y creamos volúmenes simples. Los formateamos en **NTFS**, dejamos el tamaño de unidad de asignación predeterminado y asignamos las letras y etiquetas correspondientes.

<p align="center"><img src="imagenes/WhatsApp Image 2026-11-16 at 09.09.49.jpeg" alt="Asistente para formatear un volumen en NTFS" width="560" /></p>

**Aquí lo que hemos hecho es seleccionar el sistema de archivos NTFS para formatear el volumen y dejar el tamaño de unidad de asignación predeterminado.**

<p align="center"><img src="imagenes/WhatsApp Image 2026-11-16 at 09.12.03.jpeg" alt="Configuración de la etiqueta copias_de_seguridad para el volumen de copias" width="560" /></p>

**Aquí lo que hemos hecho es asignar la etiqueta `copias_de_seguridad` al volumen destinado a guardar las copias. También mantenemos el formato NTFS y el formato rápido.**

<p align="center"><img src="imagenes/WhatsApp Image 2026-11-16 at 09.18.55.jpeg" alt="Configuración de la etiqueta Recursos_Compartidos para el volumen compartido" width="560" /></p>

**Aquí lo que hemos hecho es asignar la etiqueta `Recursos_Compartidos` al volumen que utilizaremos para los recursos compartidos corporativos.**

Una vez creados y formateados los volúmenes, comprobamos su estado desde **Administración de discos**. En la captura se observan los tres discos secundarios en línea y con formato NTFS:

- `D:` — `Recursos_Compartidos`, aproximadamente 50 GB.
- `E:` — `datos_departamentales`, aproximadamente 50 GB.
- `F:` — `copias_de_seguridad`, aproximadamente 20 GB.

<p align="center"><img src="imagenes/WhatsApp Image 2026-11-16 at 09.19.59.jpeg" alt="Administración de discos con los volúmenes D, E y F creados en NTFS" width="680" /></p>

**Aquí lo que hemos hecho es comprobar que los tres volúmenes están creados, aparecen en línea y utilizan NTFS. También podemos ver que cada uno tiene la letra y la etiqueta que le hemos asignado.**

## 4. Tarea 4: Configuración TCP/IP del servidor

Configuramos una dirección IP estática en el adaptador Ethernet de Windows Server. En la captura aparece la dirección `10.3.10.100` con máscara `255.255.255.0`.

<p align="center"><img src="imagenes/WhatsApp Image 2026-11-20 at 08.30.52.jpeg" alt="Propiedades IPv4 del adaptador Ethernet de Windows Server con IP estática" width="560" /></p>

**Aquí lo que hemos hecho es entrar en las propiedades IPv4 del adaptador Ethernet y seleccionar la configuración manual. En la captura se ve la dirección IP `10.3.10.100` y la máscara `255.255.255.0`. Los campos de puerta de enlace y DNS aparecen sin completar en esta imagen.**

Después comprobamos la configuración desde la consola de Windows y realizamos una prueba de conectividad con la dirección `10.3.10.1`.

<p align="center"><img src="imagenes/WhatsApp Image 2026-11-20 at 08.37.00.jpeg" alt="Comprobación de la configuración de red y ping desde el equipo Windows" width="560" /></p>

**Aquí lo que hemos hecho es consultar la configuración de red desde la consola de Windows y ejecutar un `ping` hacia `10.3.10.1`. En la pantalla se observan respuestas correctas, por lo que hay conectividad con esa dirección de la red.**

> **Pendiente de revisar:** la dirección `10.3.10.100` coincide con el inicio del rango DHCP que aparece en la planificación y con el rango que se configura más adelante. También hay que completar y comprobar la puerta de enlace y el DNS que correspondan al diseño definitivo. No se debe dejar la IP fija del servidor dentro del rango que se asigna a los clientes.

## 5. Tarea 5: Servicio DHCP en Windows Server

Una vez instalado el rol **Servidor DHCP**, abrimos la consola DHCP desde **Administrador del servidor → Herramientas → DHCP**.

<p align="center"><img src="imagenes/WhatsApp Image 2026-11-20 at 10.05.39.jpeg" alt="Consola de administración DHCP abierta en Windows Server" width="560" /></p>

**Aquí lo que hemos hecho es abrir la consola de administración DHCP para gestionar el servicio y crear el ámbito que utilizarán los equipos clientes.**

### 5.1. Creación del ámbito

En el asistente para crear un ámbito indicamos un nombre y un rango de direcciones IP. En una de las capturas se ve el rango desde `10.3.10.10` hasta `10.3.10.200`.

<p align="center"><img src="imagenes/WhatsApp Image 2026-11-20 at 10.11.27.jpeg" alt="Asistente DHCP para definir el intervalo de direcciones IP del ámbito" width="560" /></p>

**Aquí lo que hemos hecho es introducir el intervalo de direcciones que podrá entregar el servidor DHCP a los clientes.**

> **Importante:** en esta captura también aparece la máscara `255.252.255.0`, que no corresponde a la red `/24` que estamos utilizando. Para la VLAN 310 la máscara debe ser `255.255.255.0`. Hay que revisar este dato en el ámbito y corregirlo si se guardó con ese valor.

### 5.2. Configuración de DNS y dominio

En el asistente configuramos el nombre de dominio y el servidor DNS que recibirán los clientes. La captura muestra el dominio `sevilla.com` y el servidor DNS `10.3.10.100`.

<p align="center"><img src="imagenes/WhatsApp Image 2026-11-20 at 10.27.32.jpeg" alt="Asistente DHCP con el dominio sevilla.com y el servidor DNS 10.3.10.100" width="560" /></p>

**Aquí lo que hemos hecho es indicar el nombre de dominio que se entregará a los clientes y añadir la dirección IP del servidor DNS.**

### 5.3. Comprobación del ámbito

Después de completar el asistente, volvemos a la consola DHCP. En ella aparece el ámbito identificado como `10.3.10.0` y con el nombre `Sevilla`.

<p align="center"><img src="imagenes/WhatsApp Image 2026-11-20 at 10.28.31.jpeg" alt="Consola DHCP con el ámbito 10.3.10.0 Sevilla" width="560" /></p>

**Aquí lo que hemos hecho es comprobar que el ámbito `Sevilla` aparece creado dentro de IPv4 en la consola de administración DHCP.**

> **Pendiente:** faltan capturas de las exclusiones, de la puerta de enlace (opción 003), de las concesiones y de la reserva DHCP del cliente físico. También falta mostrar `ipconfig /all` en un cliente virtual y en el cliente físico para demostrar qué dirección reciben y qué servidor DHCP se la ha entregado.

> **Revisión de los valores:** las capturas muestran `10.3.10.100` como IP de Windows Server y como servidor DNS, mientras que el asistente DHCP muestra `10.3.10.10` como inicio del rango y `sevilla.com` como dominio. Hay que confirmar los valores definitivos y evitar que el servidor tenga una IP que pueda entregar el propio ámbito DHCP.

## 6. Tarea 6: DHCP no autorizado y DHCP Snooping

### 6.1. Preparación del servidor Ubuntu

Para simular un servidor DHCP no autorizado utilizamos un equipo físico con Ubuntu Server. Durante la instalación configuramos el perfil del equipo y completamos la puesta en marcha del sistema.

<p align="center"><img src="imagenes/WhatsApp Image 2026-11-06 at 08.33.53.jpeg" alt="Configuración del perfil del servidor Ubuntu utilizado para la práctica" width="560" /></p>

**Aquí lo que hemos hecho es completar la configuración del perfil de Ubuntu Server, indicando el nombre del usuario y del equipo que utilizaremos para administrar el servidor.**

<p align="center"><img src="imagenes/WhatsApp Image 2026-11-06 at 08.37.11.jpeg" alt="Equipo físico utilizado para preparar el servidor Ubuntu en el laboratorio" width="560" /></p>

**Aquí se muestra el equipo físico del laboratorio que utilizamos para preparar y administrar el servidor Ubuntu.**

<p align="center"><img src="imagenes/WhatsApp Image 2026-11-06 at 09.55.50.jpeg" alt="Pantalla de bienvenida de Ubuntu Server 24.04.5 LTS" width="560" /></p>

**Aquí lo que hemos hecho es comprobar que Ubuntu Server ha terminado de arrancar y que podemos acceder al sistema para continuar con la configuración del servicio DHCP.**

### 6.2. Instalación y comprobación del servicio DHCP en Ubuntu

Instalamos el servicio `isc-dhcp-server` y comprobamos su estado con:

```bash
sudo apt install isc-dhcp-server
sudo systemctl status isc-dhcp-server
```

<p align="center"><img src="imagenes/WhatsApp Image 2026-11-06 at 10.37.35.jpeg" alt="Estado del servicio isc-dhcp-server en ejecución en Ubuntu" width="560" /></p>

**Aquí lo que hemos hecho es consultar el estado del servicio `isc-dhcp-server`. En la salida aparece como `active (running)`, por lo que el servicio está iniciado en Ubuntu.**

### 6.3. Configuración del ámbito DHCP no autorizado

Editamos el archivo de configuración del servicio:

```bash
sudo nano /etc/dhcp/dhcpd.conf
```

En la captura se observa un ámbito de pruebas para la red `10.3.10.0/24`, con un rango de direcciones diferente al que utiliza el servidor Windows y una puerta de enlace incorrecta para simular el servidor DHCP fraudulento.

<p align="center"><img src="imagenes/WhatsApp Image 2026-11-20 at 10.32.58.jpeg" alt="Configuración del ámbito DHCP fraudulento en dhcpd.conf" width="560" /></p>

**Aquí lo que hemos hecho es editar `dhcpd.conf` y definir el ámbito de pruebas para la red `10.3.10.0/24`. El servidor Ubuntu ofrece direcciones entre `10.3.10.201` y `10.3.10.230` y anuncia como puerta de enlace `10.3.10.254`, con el objetivo de simular una configuración DHCP incorrecta.**

### 6.4. Activación de DHCP Snooping en el Cisco 3560

La configuración prevista para proteger la red es la siguiente:

```text
conf t
ip dhcp snooping
ip dhcp snooping vlan 310
no ip dhcp snooping information option
interface fastEthernet 0/22
 ip dhcp snooping trust
end
show ip dhcp snooping
show ip dhcp snooping binding
```

El puerto `0/22` es por donde entran las respuestas del Windows Server (a través de Proxmox), así que es el único **trusted**. Los puertos por los que pueden entrar respuestas de servidores no autorizados deben permanecer **untrusted**.

> **Nota:** `no ip dhcp snooping information option` evita que el switch añada la opción 82 a las peticiones DHCP. Se incluye porque puede ser necesario en este entorno para que Windows Server procese correctamente las solicitudes.

> **Pendiente:** todavía faltan las capturas de los comandos de configuración y verificación de DHCP Snooping (`show ip dhcp snooping` y `show ip dhcp snooping binding`), el esquema con los puertos definitivos y las pruebas desde un cliente antes y después de activar la protección. También falta demostrar que el cliente deja de recibir las ofertas del Ubuntu fraudulento y continúa recibiendo la configuración correcta del Windows Server.

---

<div align="center">

**Grupo Sevilla · Proyecto intermodular · 2.º ASIX · Curso 2026–2027**

</div>
