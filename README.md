# Guía de Instalación de Arch Linux + Hyprland

Este README detalla el proceso paso a paso para instalar Arch Linux, configurar el entorno de red, entorno gráfico GNOME e instalar el gestor de ventanas Hyprland.

---
Descargar de https://archlinux.org/download/

🧊 Montar Imagen ISO en USB (con Rufus)
Antes de comenzar con la instalación de Arch Linux, necesitas crear un USB booteable con la imagen .iso.

Descarga Rufus desde su sitio oficial.
https://rufus.ie/es/

Inserta tu memoria USB.

Abre Rufus y selecciona:

Dispositivo: tu unidad USB.

Selección de arranque: selecciona la imagen archlinux-x.y.z-x86_64.iso.

Esquema de partición: GPT para sistemas UEFI, o MBR para BIOS.

Sistema de archivos: FAT32.

Haz clic en Empezar y espera a que finalice el proceso.

## 📥 Comprobación del ISO

```bash
# Verificar hash del archivo .iso
sha256sum archlinux-x.y.z-x86_64.iso
🚀 Arranque del Instalador

loadkeys es
setfont ter-224b.psf.gz
nano /etc/locale.gen

# Descomentar línea del idioma es_ES.UTF-8

-Conexión Wi-Fi

iwctl
device list
station <dispositivo> scan
station <dispositivo> get-networks
station <dispositivo> connect <nombre_wifi>
ping -c 1 google.es

-Configuración de Hora

timedatectl set-ntp true
timedatectl status

🧭 Comprobación EFI

ls /sys/firmware/efi/efivars/

💾 Particionado del Disco

fdisk -l
cfdisk /dev/sdX

-Crear tres particiones:

512M EFI

Resto del espacio menos 4-5 GB: Linux

4-5 GB: Linux swap

mkfs.vfat -F 32 /dev/sda1
mkfs.ext4 /dev/sda2
mkswap /dev/sda3
swapon /dev/sda3

📦 Instalación del Sistema Base

mount /dev/sda2 /mnt
mkdir -p /mnt/boot/efi
mount /dev/sda1 /mnt/boot/efi

pacstrap /mnt linux linux-firmware networkmanager grub wpa_supplicant base base-devel
genfstab -pU /mnt >> /mnt/etc/fstab

🛠️ Configuración Chroot

arch-chroot /mnt
pacman -Syu grub efibootmgr os-prober
grub-install --target=x86_64-efi --efi-directory=/boot/efi --bootloader-id=Arch
grub-install --target=x86_64-efi --efi-directory=/boot/efi --removable
grub-mkconfig -o /boot/grub/grub.cfg

👤 Configuración de Usuario

pacman -S dhcpcd netctl sudo vim nano
passwd
useradd -m tu_usuario
passwd barbagorda
usermod -aG wheel tu_usuario
groups barbagorda
bash
Copiar
Editar
nano /etc/sudoers
# Descomentar la línea: %wheel ALL=(ALL:ALL) ALL

🌐 Localización y Consola

nano /etc/locale.gen

# Descomentar es_ES.UTF-8 y en_US.UTF-8

locale-gen

nano /etc/vconsole.conf

# Agregar:
KEYMAP=es

🖥️ Instalación de GRUB BIOS (opcional)

grub-install --target=i386-pc /dev/sda
grub-mkconfig -o /boot/grub/grub.cfg

🌍 Configuración de Red y Hostname

echo (elegir hostname) > /etc/hostname

nano /etc/hosts
# Agregar:
127.0.0.1      localhost
::1            localhost
127.0.0.1      hostname.localhost hostname  (poner tu hostname)

exit
reboot now

✅ Post Instalación

ip a
systemctl start NetworkManager.service
systemctl enable NetworkManager.service
systemctl start wpa_supplicant.service
systemctl enable wpa_supplicant.service

🧰 Instalación de Paru (AUR Helper)

pacman -Syu git
mkdir -p ~/Desktop/tu_usuario/repos
cd ~/Desktop/tu_usuario/repos
git clone https://aur.archlinux.org/paru-bin.git
cd paru-bin
makepkg -si

🧱 Instalación de BlackArch (opcional)

cd ~/Desktop/tu_usuario/repos
mkdir blackarch && cd blackarch
curl -O https://blackarch.org/strap.sh
chmod +x strap.sh
sudo su
./strap.sh

# (opcional)
pacman -S impacket

🖼️ Instalación de Entorno Gráfico GNOME

pacman -S xorg xorg-server
pacman -S gnome
systemctl start gdm.service
systemctl enable gdm.service
pacman -S gtkmm

🌌 Instalación de Hyprland

# Hyprland con configuración personalizada
git clone https://github.com/JaKooLit/Arch-Hyprland.git
cd Arch-Hyprland
chmod +x install.sh
./install.sh

🎉 ¡ARCH LINUX INSTALADO CON ÉXITO! 🎉

Para una actualizacion completa del sistema y todos los programas instalados

sudo pacman -Syu
