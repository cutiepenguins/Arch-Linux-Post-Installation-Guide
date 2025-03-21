# Cutie Penguins' Arch Linux Post-Installation Guide
Hello 🤭. In this guide, you will be informed about the Arch Linux post-installation steps. So, let's begin!
## Install AUR Helper
Normally, everyone prefers [yay](https://github.com/Jguer/yay) but I prefer [paru](https://github.com/Morganamilo/paru) since it is written in [rust](https://www.rust-lang.org/).
### Install Paru AUR Helper
```
sudo pacman -S --needed base-devel git && git clone https://aur.archlinux.org/paru.git && cd paru && makepkg -si
```
## Install CachyOS Repositories and CachyOS Kernel (Optional)
- [CachyOS](https://cachyos.org/) is a **performance focused Arch-based** Linux distribution. If you would like an **out of the box experience** (it can be considered out of the box compared to Arch) and need the best performance possible, I would simply install CachyOS.
- However, I just want to use their [greatly optimized repositories and kernel](https://github.com/CachyOS/linux-cachyos#cachyos-repositories) while configuring every other thing about my system on my own.
  - **Be careful, do not use CachyOS kernel if you need good battery life!** However, you can still install the kernel and use it while performing tasks with high loads such as gaming and switch to default **Linux** or **Linux-LTS** kernel when you need more battery life.
### Install
```
wget https://mirror.cachyos.org/cachyos-repo.tar.xz && tar xvf cachyos-repo.tar.xz && cd cachyos-repo && sudo ./cachyos-repo.sh && sudo pacman -Sy && sudo pacman -S linux-cachyos
```
## GRUB Configuration
- **Command to configure GRUB**: 
```
sudo nano /etc/default/grub
```
- **Options to edit in GRUB** - *Don't forget to uncheck each option* 
```
GRUB_CMDLINE_LINUX_DEFAULT="mitigations=off" # do not remove existing parameters, just add the new parameter at last.
GRUB_DEFAULT=saved
GRUB_SAVEDEFAULT=true
GRUB_DISABLE_SUBMENU=y
```
- **Command to apply changes**:
```
sudo grub-mkconfig -o /boot/grub/grub.cfg
```
### Things You Should Know
- **CPU mitigations reduce performance by 30% on these CPUs:**
  - **AMD**: `Zen 1, Zen 1+, Zen 2`
  - **Intel**: `6th, 7th and 8th Generation`
  - This is why you might want to disable mitigations via `mitigations=off` kernel parameter especially if you're gaming. However, disabled mitigations might **introduce security risks**. If you care about **security** more than **performance**, do **not** disable mitigations.
- If you will be using **only one kernel** that you installed while installing Arch, you don't have to change the stats of `GRUB_DEFAULT, GRUB_SAVEDEFAULT` and `GRUB_DISABLE_SUBMENU` because we are changing their stats to be able to switch between kernels easily.
## Install Necessary Packages
```
sudo pacman -S unrar unzip intel-ucode ufw tlp flatpak fwupd fastfetch vlc noto-fonts-cjk noto-fonts-emoji spectacle gwenview && sudo systemctl enable --now tlp.service && sudo systemctl enable --now ufw.service
```
- You **don't** have to install these packages:
  - **intel-ucode**: if you don't have an Intel CPU
  - **ufw**: if you don't want to enable firewall
  - **tlp**: if you don't want to have better battery life
  - **flatpak**: if you will install everything using **AUR**
  - **fwupd**: if your computer is not supported by fwupd for firmware updates
## Install Gaming Packages
```
sudo pacman -S cachyos-gaming-meta gamemode lib32-gamemode protonup-qt
```
## Install Other Packages
For a more stable experience, I try to use Flatpak packages as much as possible, at least for basic apps. However, Flatpaks take up lots of space from your storage because Flatpak packages are containerized. If you care about your storage limit, use native packages for the apps you want to install instead of Flatpak.
```
flatpak install flathub org.onlyoffice.desktopeditors org.kde.okular net.cozic.joplin_desktop com.obsproject.Studio org.gimp.GIMP org.inkscape.Inkscape us.zoom.Zoom com.spotify.Client
```
## TLP Configuration - Power Management For Laptops
- You can use [TLP-UI](https://aur.archlinux.org/packages/tlpui) to configure [TLP](https://linrunner.de/tlp/index.html) via a graphical interface but I noticed it doesn't uncheck some options which prevents them from functioning, that's why it is safer to apply them manually.
- **Do not use TLP if you don't need battery life, because we are using TLP for the best battery life on battery mode and to switch between performance/power saving modes for our laptop, using [power-profiles-daemon](https://github.com/Rongronggg9/power-profiles-daemon) is easier and needs no configuration at all!!!**
- **Command to start editing TLP configuration file**:
```
sudo nano /etc/tlp.conf
```
**Make sure to uncheck each option you'd like to use**
```
TLP_ENABLE=1
TLP_DEFAULT_MODE=BAT
CPU_DRIVER_OPMODE_ON_BAT=active
CPU_SCALING_GOVERNOR_ON_BAT=powersave
CPU_ENERGY_PERF_POLICY_ON_BAT=power
CPU_MIN_PERF_ON_AC=100
CPU_MAX_PERF_ON_AC=100
CPU_MIN_PERF_ON_BAT=0
CPU_MAX_PERF_ON_BAT=30
CPU_BOOST_ON_AC=1 # ignore this option if your CPU doesn't support boost
CPU_BOOST_ON_BAT=0 # ignore this option if your CPU doesn't support boost
CPU_HWP_DYN_BOOST_ON_AC=1 # ignore this option if your CPU doesn't support dynamic boost
CPU_HWP_DYN_BOOST_ON_BAT=0 # ignore this option if your CPU doesn't support dynamic boost
NMI_WATCHDOG=0
PLATFORM_PROFILE_ON_BAT=low-power
AHCI_RUNTIME_PM_ON_BAT=auto
WIFI_PWR_ON_AC=off
WIFI_PWR_ON_BAT=on
WOL_DISABLE=Y
SOUND_POWER_SAVE_ON_AC=0
SOUND_POWER_SAVE_ON_BAT=1
PCIE_ASPM_ON_BAT=powersupersave
RUNTIME_PM_ON_BAT=auto
START_CHARGE_THRESH_BAT0=75 # ignore this option if your laptop doesn't support battery thresholds
STOP_CHARGE_THRESH_BAT0=80 # ignore this option if your laptop doesn't support battery thresholds
```
## ZRAM Size Increase (Optional) 
- For 16 GB RAM, Arch Linux dedicates 4 GB ZRAM which is not enough for me. That's why I increase it to 8 GB. You can skip this step if you don't know what you're doing.
- **Location**:
```
sudo nano /etc/systemd/zram-generator.conf
```
```
[zram0]
zram-size = 8192
```
```
sudo systemctl restart systemd-zram-setup@zram0.service
```
## Configure Fish && Fastfetch (Optional)
- If you would like your terminal to predict what you are going to type with colorful letters, you might want to use [Fish](https://fishshell.com/) for your terminal.
```
sudo pacman -S fish
```
```
chsh -s /usr/bin/fish # you should log out/reboot after running the command
```
- If you would like to see **fastfetch** every time you launch terminal, you should run the commands below:
```
  function fish_greeting
  fastfetch
  end
```
```
funcsave fish_greeting
```
## Dolby Atmos Setup (EasyEffects)
- After launching [EasyEffects](https://github.com/wwmm/easyeffects) and setting it to launch at boot from app settings, download [these impulses](https://github.com/shuhaowu/linux-thinkpad-speaker-improvements/tree/main/ThinkPadT495) for Dolby Atmos-like profiles.
- After downloading the files, move the files to a location you will remember the path of.
- Next, in EasyEffects, open `Effects` page and click on `Add Effect`. Then, add a `Convolver` and click on convolver.
- Click on `Impulses` and then `Import Impulse`, now add all of the .irs files you downloaded.
- Last, on `Impulses` page, it will be enough to load one of the presets (you can switch among presets).
## Conclusion
So, this was my Arch Linux post installation guide, I hope it is useful for you! Have a nice day ❤️
