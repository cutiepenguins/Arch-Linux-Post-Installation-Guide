# Cutie Penguins' Arch Linux Post-Installation Guide
Hello 🤭. In this guide, you will be informed about how do I prepare my Arch system after installation. So, let's begin!
## Install AUR Helper
Normally, everyone prefers [yay](https://github.com/Jguer/yay) but I prefer [paru](https://github.com/Morganamilo/paru) since it is written in [rust](https://www.rust-lang.org/).
### Install Paru AUR Helper
```
sudo pacman -S --needed base-devel git && git clone https://aur.archlinux.org/paru.git && cd paru && makepkg -si
```
## Install CachyOS Repositories and CachyOS Kernel - Optional
- [CachyOS](https://cachyos.org/) is a **performance focused Arch-based** Linux distribution. If you would like an **out of the box experience** (it can be considered out of the box compared to Arch) and need the best performance possible, I would simply install CachyOS.
- However, I just want to use their [greatly optimized repositories and kernel](https://github.com/CachyOS/linux-cachyos#cachyos-repositories) while configuring every other thing about my system on my own.
  - **Be careful, do not use CachyOS kernel if you need good battery life!** However, you can still install the kernel and use it while performing tasks with high loads such as gaming and switch to default **Linux** or **Linux-LTS** kernel when you need more battery life.
### Install
```
wget https://mirror.cachyos.org/cachyos-repo.tar.xz && tar xvf cachyos-repo.tar.xz && cd cachyos-repo && sudo ./cachyos-repo.sh && sudo pacman -Sy && sudo pacman -S linux-cachyos
```
## systemd-boot Configuration
**Command to configure systemd-boot**
```
sudo nano /boot/loader/loader.conf
```
**How should your loader.conf file look like**
```
default @saved
timeout 5
#console-mode keep
auto-entries 0
```
## Configure Intel GPU For Better Performance - Intel & X11 ONLY! - Optional
**Command to configure your Intel GPU**
```
sudo nano /etc/X11/xorg.conf.d/20-intel.conf
```
```
Section "Device"
  Identifier "Intel Graphics"
  Driver "modesetting"
  Option "Backlight" "intel_backlight"
  Option "AccelMethod" "glamor"
  Option "TearFree" "false"
  Option "RenderAccel" "true"
  Option "SwapbuffersWait" "false"
  Option "DRI" "2"
  Option "Throttle" "false"
  Option "FramebufferCompression" "false"
  VideoRam 24576
  Option "TripleBuffer" "false"
  Option "Shadow" "false"
  Option "LinearFramebuffer" "true"
  Option "RelaxedFencing" "false"
  Option "BufferCache" "true"
EndSection
```
## Install Necessary Packages
```
sudo pacman -S unrar unzip intel-ucode ufw auto-cpufreq flatpak fwupd fastfetch vlc noto-fonts-cjk noto-fonts-emoji  && sudo systemctl enable --now ufw.service
```
You can install `amd-ucode` instead of `intel-ucode` if you have an AMD CPU.
## Install Optional Packages
```
sudo pacman -S librewolf joplin-desktop onlyoffice okular easyeffects mint-themes mint-y-icons picom && paru -S xcursor-dmz mugshot spotify zoom
```
## Install Gaming Packages
You don't have to include `prismlauncher`, `jre8-openjdk`, `jre17-openjdk` and `jre21-openjdk` if you're not playing Minecraft.
```
sudo pacman -S cachyos-gaming-meta gamemode lib32-gamemode protonup-qt discord prismlauncher jre8-openjdk jre17-openjdk jre21-openjdk
```
## Enable auto-cpufreq
- auto-cpufreq is an automatic CPU speed & power optimizer. It is generally good for laptops but you can use it if you have a PC as well. It does everything for you.
- **Command to enable auto-cpufreq**
```
sudo auto-cpufreq --install
```
After the command, you don't have to do anything further but if you would like to configure it a bit more, continue reading the step.
### auto-cpufreq Configuration 
**Command to configure auto-cpufreq**:
```
sudo nano /etc/auto-cpufreq.conf
```
```
[charger]
governor = performance
energy_performance_preference = performance
energy_perf_bias = balance_performance
platform_profile = performance
scaling_min_freq = 800000 # this value is generally supported by all processors
scaling_max_freq = 3900000 # check your CPU's supported maximum processor frequency and change the value according to it
turbo = always

[battery] # You can change the values to be the same as charger section if you don't want to have good battery life but to have high performance on battery mode as well. You can just not include this section if you have a PC or a laptop with no battery.
governor = powersave
energy_performance_preference = power
energy_perf_bias = power
platform_profile = low-power
scaling_min_freq = 800000 # for batteries, any frequency lower than 800 MHz is unstable but if you want and if your CPU supports it, you can decrease the value
scaling_max_freq = 1000000 # for a balance of snappiness and power saving, maximum 1 GHz frequency is nice but of course you can increase the value if you want
turbo = never
enable_thresholds = true # do not include this option in your file if you don't have a battery or if you don't want to set a threshold
start_threshold = 75 # do not include this option in your file if you don't have a battery or if you don't want to set a threshold
stop_threshold = 80 # do not include this option in your file if you don't have a battery or if you don't want to set a threshold
```
## ZRAM Size Increase - Optional 
- For 16 GB RAM, Arch Linux dedicates 4 GB ZRAM which is not enough for me. That's why I increase it to 8 GB. You can skip this step if you don't know what you're doing.
- **Command to configure ZRAM**
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
## Configure picom - X11 ONLY! - Optional
**Command to configure picom**
```
sudo nano ~/.config/picom.conf
```
```
vsync = true;
backend = "glx";
fading = true;
```
## Configure Fish & Fastfetch - Optional
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
- After launching [EasyEffects](https://github.com/wwmm/easyeffects) and setting it to launch at boot from app settings, download [these impulses](https://github.com/shuhaowu/linux-thinkpad-speaker-improvements/tree/main/ThinkPadT495) for Dolby Atmos profiles.
- After downloading the files, move the files to a location you will remember the path of.
- Next, in EasyEffects, open `Effects` page and click on `Add Effect`. Then, add a `Convolver` and click on convolver.
- Click on `Impulses` and then `Import Impulse`, now add all of the .irs files you downloaded.
- Last, on `Impulses` page, it will be enough to load one of the presets (you can switch among presets).
## Conclusion
So, this was my Arch Linux post installation guide, I hope it is useful for you! Have a nice day ❤️
