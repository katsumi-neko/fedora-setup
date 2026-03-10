# fedora-setup
My personal list of commands to setup fedora on an Nvidia or AMD GPU system.
 * Skip the Nvidia steps if you have an AMD GPU

## Quality of life DNF options
* This is what my `/etc/dnf/dnf.conf` looks like.
```
[main]
fastestmirror=True
max_parallel_downloads=10
defaultyes=True
installonly_limit=3
skip_if_unavailable=True
```

## Install RPM Fusion repositories
`sudo dnf install https://mirrors.rpmfusion.org/free/fedora/rpmfusion-free-release-$(rpm -E %fedora).noarch.rpm https://mirrors.rpmfusion.org/nonfree/fedora/rpmfusion-nonfree-release-$(rpm -E %fedora).noarch.rpm`

## Update the system
* Go into KDE Discover or GNOME Software Center and install all the latest updates.
* Alternatively, you can run `sudo dnf update` to update from the terminal. 

## Install Nvidia GPU Drivers 
`sudo dnf install akmod-nvidia xorg-x11-drv-nvidia-cuda xorg-x11-drv-nvidia-cuda-libs libva-nvidia-driver libva-utils vdpauinfo vulkan`
* It is recommended to wait up to 5 minutes before proceeding/rebooting, the akmod needs to build and it is running in the background.

`sudo dnf mark user akmod-nvidia`

## Swap Nvidia Drivers to open modules
`sudo sh -c 'echo "%_with_kmod_nvidia_open 1" > /etc/rpm/macros.nvidia-kmod'`

`sudo akmods --rebuild`
* Same as above, it will take up to 5 minutes for the akmod to rebuild. Do not reboot

## Install extra codecs
`sudo dnf install sox sox-plugins-nonfree svt-av1 gstreamer1-svt-vp9 svt-vp9 libheif libheif-freeworld rav1e dav1d vkd3d-compiler`

`sudo dnf swap ffmpeg-free ffmpeg --allowerasing`

`sudo dnf update @multimedia --setopt="install_weak_deps=False" --exclude=PackageKit-gstreamer-plugin`

## Install Nvidia specific codecs
`sudo dnf install libva-nvidia-driver.{i686,x86_64} libva-utils vdpauinfo`

`sudo dnf install nvidia-query-resource-opengl nvidia-texture-tools`

`sudo dnf install freeglut-devel libX11-devel libXi-devel libXmu-devel make mesa-libGLU-devel freeimage-devel glfw`

## Install AMD GPU specific codecs
`sudo dnf swap mesa-va-drivers mesa-va-drivers-freeworld`
`sudo dnf swap mesa-va-drivers.i686 mesa-va-drivers-freeworld.i686`

## Replace Fedora Flatpak with Flathub
`sudo flatpak remote-add --if-not-exists flathub https://flathub.org/repo/flathub.flatpakrepo`

`flatpak remote-modify --no-filter --enable flathub`

`flatpak install --reinstall flathub $(flatpak list --app-runtime=org.fedoraproject.Platform --columns=application | tail -n +1 )`

`flatpak remote-delete fedora`

## lib.so fix
* I had an error with lib.so not being found for a specific application, this fixed that for me. Probably not needed for most people.
`cd /usr/lib64/`

`sudo ln -s libbz2.so.1.0.8 libbz2.so.1.0`

## Fix slow bootup
* the NetworkManager-wait-online.service can sometimes delay bootup by up to 10-15 seconds
`sudo systemctl disable NetworkManager-wait-online.service`

## Final words
* If you are on an Nvidia system, it is good practice to wait 5 minutes after installing updates, to allow the akmods to finish running in the background (otherwise you might end up booting to a black screen!)
* It's good practice to stay relatively up to date, you will run into fewer issues this way. I personally update once per week, and have KDE setup to notify me weekly to update.
* It's a good idea to check out the official Fedora documentation and learn how to manage and use your system. This is just a guide to help users get up and running the same way I have on my system.
* If you have any issues while following this guide, feel free to open an issue and I will do my best to help/resolve it!

## The guide to install the CachyOS kernel on Fedora (optional)
https://github.com/CachyOS/copr-linux-cachyos
