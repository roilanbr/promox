# Instalción
Priero descargamos el iso, para ello vamos a la web de Promox [https://www.proxmox.com/en/downloads](https://www.proxmox.com/en/downloads) y descargamos la última versión del iso.

Al insertar el CD-ROM o USB con Proxmox nos sale la pantalla de binvenida.

  * Escogemos la opcion: `Install Proxmox VE`
  * Aceptamos la licencia: `I agree`
  * Escogemos el HDD donde instalar: `Next`
  * Escogemos la localidad zona horaria:
    - Country: `Cuba`
    - Time zone: `America/Havana`
    - Keyboard Layout: `U.S. English`
  * Ponemos la contraseña y email:
    - Password: `clave_root`
    - Confirm: `clave_root`
    - E-mail: `admin@midominio.cu`
  * Configuramos la red:
    - Management Interface: `interfaz_para_administrar`
    - Hostname (FQDN): `pve.midominio.cu`
    - IP Address: `192.168.0.2`
    - Netmask: `255.255.255.0`
    - Gateway: `192.168.0.1`
    - DNS Server: `192.168.0.2`
  * Verificar que los datos llenados son los deseados: `Install`
  * Terminar la instalación: `Reboot`

Al reiniciar escogeremos la primera opcion del GRUB:\
`* Proxmox Virtual Environment GNU/Linux`

Esperamos que inicie el servidor hasta que esté en la la pantalla de autenticarnos, tambien en la misma pantalla aparecera la `URL` para administrar el Proxmox.

En un navegador abrimos la URL [https://192.168.0.2:8006](https://192.168.0.2:8006), nos saldrá una advertencia del certificado autofirmado, aceptamos el certificado y nos saldra la ventana de autenticarnos en la consola web del Proxmox.
