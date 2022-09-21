# Instalación de Arch-Linux en VM

In this document is described how to perform an standard installation of Arch-Linux on a virtual machine. It is supposed that we have the virtual machine already created and it has been booted into the live media of the distro.

I prepared this guide according to my personal preferences, and after doing this process several times. Try to understand the commands and configurations before applying them.

We begin checking if there is internet connection:

```
# ping google.com
```

Repositories are refreshed:

```
# pacman -Sy
```

Now we must partition the VM's disk:

1. We check for detected disks:
	
	```
	# lsblk
	```

2. Once we have identify the disk that we are going to partition, we execute `cfdisk`:

	```
	# cfdisk /dev/<disk>
	```

3. When executing that command, it will ask us what type of label do we want the disk to have. We must select `dos`.

4. After that, we will have an empty partitioning table for our disk. Now, we must create a new partition by pressing the `New` option from the menu.

5. Choose partition's size. It is ok to use the whole disk size as we are installing it on a VM.

6. This partition must be of `primary` type, so select that when the program asks you.

7. Now, select the `Bootable` option from the menu to make bootable the partition we've just created.

8. Lastly, we have to write the created partition table to the disk by selecting the `Write` option in the menu and typing `yes` to confirm the action. To get out from `cfdisk`, press the `Quit` option.

With our disk partitioned, we can execute the following command to build a Linux filesystem on the new partition:

```
# mkfs.ext4 /dev/<partition>
```

Once we have our partition ready, we must mount it:

```
# mount /dev/<partition> /mnt
```

At last, it is time for installation. Executing the following command installs a set of software packages which I consider useful for a basic distro installation at this point, but some of them aren't mandatory (ex. You could consider changing `neovim` for `nano` or other text editor):

```
# pacstrap /mnt base base-devel linux linux-firmware neovim man-db man-pages texinfo
```

After the system installation is complete, the `fstab` file must be generated:

```
# genfstab -U /mnt >> /mnt/etc/fstab
```

Now, we must login as root into the new system using the following command:

```
# arch-chroot /mnt
```

Once we are logged into the new system, we are going to make some final configurations.  

First, we are going to set the time zone:

```
# ln -sf /usr/share/zoneinfo/<region>/<city> /etc/localtime
```

And adjust time:

```
# hwclock --systohc
```

Edit the `/etc/locale.gen` file to uncomment `en_US.UTF-8 UTF-8` and other needed locales.

Generate the selected locales by running:

```
# locale-gen
```

Create the `/etc/locale.conf` file to set the LANG variable:

```
# echo "LANG=en_US.UTF-8" > /etc/locale.conf
```

Create the `/etc/hostname` file to set the hostname for your system:

```
# echo arch-lab > /etc/hostname
```

Change the root user password of your system:

```
# passwd
```

Create a user and make available for it to launch commands using `sudo`:

1. Install `sudo`:

	```
	# pacman -Syu sudo
	```

2. Create a sudo group:

	```
	# groupadd sudo
	```

3. Change the editor variable if `vim` package is not installed and edit the sudo configuration so the users who are present in the sudo group can use `sudo`. After executing visudo, uncomment the line `%sudo ALL=(ALL:ALL) ALL`:

	```
	// ONLY IF YOU DON'T HAVE VIM INSTALLED
	# EDITOR=<text-editor>
	//-------------------------------------

	# visudo
	```

4. Create a user, add it to the sudo group and change/set its password:

	```
	# useradd -mU <user-name>
	# usermod -aG sudo <user-name>
	# passwd <user-name>
	```

Install and setup grub bootloader:

1. Install `grub`:

	```
	# pacman -Syu grub
	```

2. Install grub to the disk:

	```
	# grub-install --recheck /dev/<disk>
	```

3. Generate the grub config file:

	```
	# grub-mkconfig -o /boot/grub/grub.cfg
	```

Type `exit` to exit from the chroot enviroment.

Unmount the partition:

```
# umount -R /mnt
```

Finally, reboot or shutdown your fresh system:

```
# shutdown now
```