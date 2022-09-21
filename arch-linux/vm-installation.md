# Instalación de Arch-Linux en VM

En este documento se describe como realizar una instalación estándar de Arch-Linux en una máquina virtual. Se asume que ya se dispone de una máquina virtual que ha sido iniciada con los medios de instalación de la distribución.

Se comienza comprobando si se tiene conexión con internet:

```
# ping google.com
```

Se actualizan los repositorios:

```
# pacman -Sy
```

Ahora se debe particionar el disco de la máquina virtual:

1. Comprobamos los discos detectados:
	
	```
	# lsblk
	```

2. Una vez hemos identificado el disco que vamos a particionar, ejecutamos `cfdisk`:

	```
	# cfdisk /dev/<disk>
	```

```
# mkfs.ext4 /dev/<partición>
```

```
# pacstrap /mnt base base-devel linux linux-firmware <text-editor> man-db man-pages texinfo
```