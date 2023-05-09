# El instalador Fedora Live USB no arranca en ciertas motherboards.
# Memoria USB booteable no arranca en placas madres antiguas.
# Instalar fedora 38 en computadoras antigua, computadoras del 2012
# No puedo instalar fedora 37, 38

¡Hola a todos! Bienvenidos a este canal de YouTube.


Source:
https://discussion.fedoraproject.org/t/install-media-dont-boot-in-uefi-mode-on-certain-motherboards/71376/1

Existen las siguientes tres soluciones.

- Instalar fedora 36 y hacer un upgrade.
- Modificar tu placa madre a BIOS en vez de UEFI e instalar fedora 38.
- Modificar el instalador Fedora Live USB stick, memoria USB booteable.

En este caso vamos a modificar el **Fedora Live USB**.

## Modificar el Fedora Live USB stick.

### Requisitos
- Necesitamos una computadora con Sistema Operativo Linux.
- Un Fedora Live USB.
- Tener permisos de super usuario.

Insertamos el dispositivo usb.

Revisamos el dispositivo usb **sdx**, donde **x** puede ser a, b, c, d, e, f, etc.
En este caso el dispositivo usb esta en **sde**.

```bash
lsblk
```
```bash
NAME             MAJ:MIN RM   SIZE RO TYPE MOUNTPOINTS
sda                8:0    0 447.1G  0 disk 
├─sda1             8:1    0   100G  0 part 
│ └─forvm-vmtest 253:0    0   200G  0 lvm  
└─sda2             8:2    0 149.6G  0 part 
  └─forvm-vmtest 253:0    0   200G  0 lvm  
sdb                8:16   0   1.8T  0 disk 
├─sdb1             8:17   0   839G  0 part 
├─sdb2             8:18   0  27.9G  0 part 
└─sdb3             8:19   0  93.2G  0 part 
  └─vg2-vms      253:1    0  93.2G  0 lvm  
sdc                8:32   0 223.6G  0 disk 
├─sdc1             8:33   0   100M  0 part /boot/efi
├─sdc2             8:34   0    16M  0 part 
├─sdc3             8:35   0  99.4G  0 part 
├─sdc4             8:36   0   509M  0 part 
├─sdc5             8:37   0     1G  0 part 
├─sdc6             8:38   0     1G  0 part /boot
└─sdc7             8:39   0 121.6G  0 part /home
                                           /
sdd                8:48   1     0B  0 disk 
sde                8:64   1   7.2G  0 disk 
├─sde1             8:65   1   1.9G  0 part 
├─sde2             8:66   1  11.6M  0 part 
└─sde3             8:67   1   300K  0 part 
sr0               11:0    1  1024M  0 rom  
zram0            252:0    0     8G  0 disk [SWAP]
```
Vamos a montar en este caso la partición sde2, una forma de identificarlo es por el tamaño, debes escoger el que tiene entre 10 a 12 MB.
Creamos un directorio **esp** en el directorio HOME y montamos la partición ESP en este directorio, luego ingresamos al directorio **esp**.

```bash
cd
mkdir esp
sudo mount /dev/sde2 esp
cd esp
```

Eliminamos el archivo **grubia32.efi, BOOTIA32.EFI, BOOTX64.EFI**
```bash
sudo rm -rf EFI/BOOT/BOOTX64.EFI EFI/BOOT/BOOTIA32.EFI EFI/BOOT/grubia32.efi
```

Descargamos un archivo **shim-x64-15.4-5.x86_64.rpm**
```bash
cd ..
wget https://dl.fedoraproject.org/pub/fedora/linux/releases/36/Everything/x86_64/os/Packages/s/shim-x64-15.4-5.x86_64.rpm
```

Creamos un directorio y descomprimimos el archivo **shim-x64-15.4-5.x86_64.rpm** en ese directorio.
```bash
mkdir shimdir
rpm2cpio shim-x64-15.4-5.x86_64.rpm > shim.cpio
cpio -idmvD shimdir < shim.cpio
ls shimdir/
```

Copiamos el archivo **BOOTX64.EFI** del directorio **shimdir* al directorio montado en **esp**

```bash
sudo cp shimdir/boot/efi/EFI/BOOT/BOOTX64.EFI esp/EFI/BOOT/
```
Listo, ahora solo desmontamos **esp**.

```bash
sudo umount esp
```

Por ultimo podemos eliminar los archivos y directorio.

- shim.cpio
- shimdir
- shim-x64-15.4-5.x86_64.rpm

```bash
rm -rf shim.cpio shimdir shim-x64-15.4-5.x86_64.rpm
```