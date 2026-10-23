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

<div align="center">

**Grupo Sevilla · Proyecto intermodular · 2.º ASIX · Curso 2026–2027**

</div>
