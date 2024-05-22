# Eliminar partición local-LVM

Al instalar Proxmox 6.x se crea 2 particiones por defecto llamadas `local` y `local-lvm` en esta ultima solo se guardan los discos de las VM por lo que eliminaremos esa partición y la crearemos para poder almacenar las iso, backup, plantillas, etc.

Lo primero es eliminar la partición `lvm-thin` que es la que nos interesa para ello desde la consola ejecutaremos los siguientes comandos: 

```bash
root@pve:~# lvremove /dev/pve/data
Do you really want to remove and DISCARD active logical volumen pve/data? [y/n]: y
  Logical volume "data" successfully removed
```
 
Verificar el espacio libre con `vgdisplay` y fijarnos en el espacio libre en `Free PE / Size`: 

```bash 
root@pve:~# vgdisplay
 --- Volume group ---
 [...]
 Alloc PE / Size       3392 / 13.25 GiB
 Free  PE / Size       9279 / <250.25 GiB
 VG UUID               4Wpf9c-oAXk-OGmD-ndsZ-BTbg-npwB-F45oTl
 ```

Crear el nuevo volumen con un tamaño un poco mas pequeño (250.24G): 

```bash
root@pve:~# lvcreate -L 250.24G -n data pve
Rounding up size to full physical extent 250.24 GiB
Logical volume "data" created.
```

Formatear el nuevo volumen:

```bash
root@pve:~# mkfs.ext4 /dev/pve/data
```

Modificar el archivo `fstab` y agregar al final los parámetros para que se monte el volumen al iniciar el sistema:

```bash
root@pve:~# nano /etc/fstab

[...]
/dev/pve/data /media/hdd ext4 rw 0 2
```

Crear carpeta de montaje y probar a montar:

```bash
root@pve:~# mkdir /media/hdd
root@pve:~# mount -a
```

Comprobar que se a montado:

```bash
root@pve:~# df -lh
Filesystem             Size      Used  Avail Use% Mounted on
[...]
/dev/mapper/pve-data   250.24G   24M   250G   1% /media/hdd
```

Eliminar partición `local-lvm` que aparece en la interfaz web del proxmox y agregar el nuevo volumen creado, puede ser por la web o por consola:

```bash
root@pve:~# nano /etc/pve/storage.cfg

dir: local
        path /var/lib/vz
        content iso,vztmpl,backup
 
#lvmthin: local-lvm
#        thinpool data
#        vgname pve
#        content rootdir,images 
 
dir: salva
        path /media/salva
        content images,snippets,iso,backup,rootdir,vztmpl
        maxfiles 4
        shared 0
```
        
$\color{#50fa7b}{\textsf{Final}}$