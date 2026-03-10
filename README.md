# fedora-setup
My personal list of commands to setup fedora on an Nvidia system. 

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

## Update the system
* Go into KDE Discover or GNOME Software Center and install all the latest updates.
* Alternatively, you can run `sudo dnf update` to update from the terminal. 

## Install RPM Fusion repositories
`sudo dnf install https://mirrors.rpmfusion.org/free/fedora/rpmfusion-free-release-$(rpm -E %fedora).noarch.rpm https://mirrors.rpmfusion.org/nonfree/fedora/rpmfusion-nonfree-release-$(rpm -E %fedora).noarch.rpm`

## Install Nvidia Drivers 
`sudo dnf install akmod-nvidia xorg-x11-drv-nvidia-cuda xorg-x11-drv-nvidia-cuda-libs libva-nvidia-driver libva-utils vdpauinfo vulkan`

`sudo dnf mark user akmod-nvidia`

## Swap Nvidia Drivers to open modules
`sudo sh -c 'echo "%_with_kmod_nvidia_open 1" > /etc/rpm/macros.nvidia-kmod'`

`sudo akmods --rebuild`

## Install extra codecs
`sudo dnf install sox sox-plugins-nonfree svt-av1 gstreamer1-svt-av1 gstreamer1-svt-vp9 svt-vp9 libheif libheif-freeworld rav1e dav1d vkd3d-compiler`

`sudo dnf swap ffmpeg-free ffmpeg --allowerasing`

`sudo dnf update @multimedia --setopt="install_weak_deps=False" --exclude=PackageKit-gstreamer-plugin`

## Install specific Nvidia codecs
`sudo dnf install libva-nvidia-driver.{i686,x86_64} libva-utils vdpauinfo`

`sudo dnf install nvidia-query-resource-opengl nvidia-texture-tools`

`sudo dnf install freeglut-devel libX11-devel libXi-devel libXmu-devel make mesa-libGLU-devel freeimage-devel glfw --skip-unavailable`

## Replace Fedora Flatpak with Flathub
`flatpak remote-add --if-not-exists flathub https://flathub.org/repo/flathub.flatpakrepo`

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

## The guide to install the CachyOS kernel on Fedora (optional)
https://github.com/CachyOS/copr-linux-cachyos
