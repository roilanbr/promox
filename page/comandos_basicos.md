# Archivos de configuración

Lo primero a tener en cuenta es donde se encuentra los archivos de configuración y los disco de las Maquinas Virtuales(VM) y los contenedores(LXC).

Los archivos de configuración de Proxmox están en:
```bash
ls -F /etc/pve/
authkey.pub     authkey.pub.old  datacenter.cfg   firewall/	
ha/             local            lxc              nodes/
openvz          priv/            pve-root-ca.pem  pve-www.key  
qemu-server     sdn/             storage.cfg      user.cfg
virtual-guest/  vzdump.cron
```

Para modificar la configuración de la VM estan en:

```bash
ls /etc/pve/nodes/pve/qemu-server/
101.conf  102.conf  103.conf
```

Para modificar la configuración de los LXC estan en:

```bash
ls /etc/pve/nodes/pve/lxc/
104.conf  105.conf  106.conf
```

Los discos de las VM y LXC respectivamente están en `/var/lib/vz/images/xxx/vm-xxx-disk-0.qcow2`:

```bash
ls -F /var/lib/vz/images/101/
vm-101-disk-0.qcow2
```


# Comandos básicos Proxmox

**Comando qm para VM**
* qm list            (listar)
* qm destroy <vmid>  (eliminar)
* qm reset <vmid>    (resetear)
* qm shutdown <vmid> (apagar)
* qm start <vmid>    (encender)
* qm stop <vmid>     (Apagar)
* qm resize <vmid><disco><+tamaño> (Incrementa el disco virtual)
* qm config <vmid>   (ver configuración de la VM)
* qm sendkey <vmid> <key> (enviar pulsación de tecla)

> Nota:\
> Para mas comando ejecute `qm` sin parametros


**Comando pct para LXC**
* pct create <vmid> <ruta_plantilla> - Crear un LXC
* pct enter <vmid>    - Entrar al contenedor y ejecutar un shell como root
* pct list            - Listar
* pct fsck <vmid>     - Comprovar sistema de archivo
* pct reset <vmid>    - Resetear
* pct shutdown <vmid> - Apagar
* pct start <vmid>    - Encender
* pct stop <vmid>     - Parar
* pct resize <vmid> <disk> <size> - Incrementa el disco virtual
* pct config <vmid>   - Ver configuración

Para mas comando ejecute `pct` sin parametros


# Gestionar las salvas

**Crear salvas de VM y LXC**

`vzdump <vmid> [OPCIONES]`

[OPCIONES]
* --compress <0|1|gzip|lzo|zstd> - Comprimir predeterminado = 0 
* --dumpdir <ruta>               - Ruta del directorio donde realizar la salva
* --mailnotification             - Notificar siempre el estado de la salva
* --mailto <email>               - Dirección de correo a notificar
* --remove <numero>              - Eliminar las salva más antiguas predeterminado = 1 
* --storage <almacen>            - Nombre del almacenamiento donde salvar

**Restaurar salvas de VM y LXC**

Debemos de apagar la VM o LXC

Para VM: `qmrestore <archive> <vmid> [OPTIONS]`

```bash
qmrestore 101 /var/lib/vz/dump/vzdump-qemu-101-2021_07_03-00_00_03.vma.zst 101 --storage local-lvm
```

Para LXC: `pct restore <vmid> <ruta salva>--storage `

```bash
pct restore 102 /var/lib/vz/dump/vzdump-lxc-102-2021_07_03-00_15_49.tar.zst --storage local-lvm
```


# Montar USB en VM
Conectar un USB directamente a una máquina virtual. Primero tenemos que comprobar que el USB está conectado físicamente al host, para ello usaremos el comando `lsusb`:

```bash
lsusb -t
/:  Bus 02.Port 1: Dev 1, Class=root_hub, Driver=ohci-pci/12p, 12M
    |__ Port 1: Dev 2, If 0, Class=Human Interface Device, Driver=usbhid, 12M
/:  Bus 01.Port 1: Dev 1, Class=root_hub, Driver=ehci-pci/2p, 480M
    |__ Port 1: Dev 9, If 0, Class=Mass Storage, Driver=usb-storage, 480M
```

Si queremos conectar la memoria USB (Bus 01 | Port 1), editamos la configuración de la VM añadiendo al final `usb1: host=1-1`.

`/etc/pve/nodes/pve/qemu-server/100.conf`

```bash
bootdisk: ide0
cores: 1
ide0: salva:101/vm-101-disk-0.qcow2,size=1G
ide2: none,media=cdrom
memory: 16
name: debian10
net0: rtl8139=62:69:24:3A:63:AB,bridge=vmbr0,firewall=1
numa: 0
ostype: l26
scsihw: virtio-scsi-pci
smbios1: uuid=7f47d2e8-a1c4-4098-91bb-43663b66aa12
sockets: 1
vmgenid: 72f1e9c1-772c-4807-a90d-51ba4ccc743e
usb1: host=1-1
```

Una vez encendia la VM comprovamos que detecta el USB:

```bash
qm monitor 101
Entering Qemu Monitor for VM 101 - type 'help' for help

qm> info usb
Device 0.1, Port 1, Speed 12 Mb/s, Product QEMU USB Tablet
Device 1.1, Port 1, Speed 480 Mb/s, Mass Storage
```


# Informacion de la memoria ram de LXC

```bash
qm monitor 106
Entering Qemu Monitor for VM 106 - type 'help' for help

qm> info memory_size_summary
base memory: 2147483648
plugged memory: 0
```



