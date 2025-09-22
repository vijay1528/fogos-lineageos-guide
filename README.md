![image](https://github.com/user-attachments/assets/8ca0811e-b031-4fe4-8ded-b322b9fdb7c7)

## Build LineageOS For Moto g45/g34 (fogos)
* This guide follows LineageOS official [guide](https://wiki.lineageos.org/devices/fogos/build/variant2)
* I'm creating this to make rom building more user friendly

## Disclaimer / Note
* Look i'm not responsible for bricked devices, dead phone or your girlfriend ran away with someone.
* You are going through this guide bcoz u wanna use your free time to try building custom rom for your OWN device repeat own device.
* Your warranty will be void if you fuck with your device or its software.
* If i have missed some linux commands/dependencies, spelling mistakes then correct yourself and can check official guide

# Requirements

Motorola moto g45/g34
Linux prefer 22.02
+64gb ram, +300gb storage nvme/ssd, CPU cores(prefer more than your age but have minimum 4 cores)

And most important common sense, use chatGPT/girlfriendGPT or any model u want, if need help with linux commands.

## LFG, go through step by step

** Update System Packages**
```bash
apt update && apt install -y sudo
sudo apt update && sudo apt upgrade -y
```

** Install General Utilities and Tools**
```bash
sudo apt update && sudo apt install -y autoconf automake bsdmainutils build-essential clang curl ffmpeg gcc git htop jq libffi-dev libgbm1 libjpeg-dev libleveldb-dev libopenblas-dev libsqlite3-dev libssl-dev lsof lz4 make nano ncdu nvme-cli pkg-config screen tar tmux unzip wget zlib1g-dev iptables -y
```

** LineageOS Dependencies**
```bash
sudo apt update
sudo apt install bc bison build-essential curl flex g++-multilib gcc-multilib git gnupg gperf \
imagemagick lib32ncurses5-dev lib32readline-dev lib32z1-dev liblz4-tool libncurses5-dev libsdl1.2-dev \
libssl-dev libxml2 libxml2-utils lzop pngcrush rsync schedtool squashfs-tools x11proto-core-dev \
xsltproc zip zlib1g-dev openjdk-11-jdk repo
```

** Java**
```bash
sudo update-alternatives --config java
sudo update-alternatives --config javac
```

** Install Python**
```bash
sudo apt install python3 python3-pip python3-venv python3-dev -y
sudo apt install python-is-python3
```

** Create the directories**
```bash
mkdir -p ~/bin
mkdir -p ~/android/lineage
```

** Install the repo command**
```bash
curl https://storage.googleapis.com/git-repo-downloads/repo > ~/bin/repo
chmod a+x ~/bin/repo
```

** Put the ~/bin directory in your path of execution**
```bash
# set PATH so it includes user's private bin if it exists
if [ -d "$HOME/bin" ] ; then
    PATH="$HOME/bin:$PATH"
fi
```
```bash
# update your environment
source ~/.profile
```

** Configure git**
```bash
# Do not remove quotes
git config --global user.email "you@example.com"
git config --global user.name "YourUserNameNotMine"
```
* replace you@example.com and YourUserNameNotMine with yours

```bash
sudo apt update
sudo apt install git-lfs
git lfs install
```
```bash
git config --global trailer.changeid.key "Change-Id"
```

** Turn on caching to speed up build**
```bash
export USE_CCACHE=1
export CCACHE_EXEC=/usr/bin/ccache

echo 'export USE_CCACHE=1' >> ~/.bashrc
echo 'export CCACHE_EXEC=/usr/bin/ccache' >> ~/.bashrc
source ~/.bashrc

sudo apt update
sudo apt install ccache
ccache -M 50G
```

** Initialize the LineageOS source repository**
* Note: this is for lineage ver 22.2

```bash
cd ~/android/lineage
repo init -u https://github.com/LineageOS/android.git -b lineage-22.2 --git-lfs --no-clone-bundle
```

** Download the source code**
* Note: if you are on vps use tmux coz this will take time

```bash
repo sync
```

** Get/Extract proprietary blobs**
* Note: you can go through official methods from [LineageOS](https://wiki.lineageos.org/devices/fogos/build/variant2/#extract-proprietary-blobs)
* But in this guide im using [fogos](https://github.com/TheMuppets/proprietary_vendor_motorola_fogos) and [sm6375-common](https://github.com/TheMuppets/proprietary_vendor_motorola_sm6375-common) managed by his highness mikeNG

```bash
mkdir -p ~/android/lineage/vendor/motorola
```
```bash
cd ~/android/lineage/vendor/motorola
git clone -b lineage-22.2 https://github.com/TheMuppets/proprietary_vendor_motorola_fogos fogos
```
```bash
cd ~/android/lineage/vendor/motorola
git clone -b lineage-22.2 https://github.com/TheMuppets/proprietary_vendor_motorola_sm6375-common sm6375-common
```

** Preparing the build environment**
```bash
cd ~/android/lineage
source build/envsetup.sh
croot
```

** Prepare the device-specific code**
* Note: This will download your device’s [device specific configuration](https://github.com/LineageOS/android_device_motorola_fogos) and [kernel]https://github.com/LineageOS/android_kernel_motorola_sm6375

```bash
breakfast fogos
```

## Start the build
* Note: this will take time if u are on vps use tmux

```bash
croot
brunch fogos
```

## Find the zip and .imgs

```bash
cd $OUT
```
or
```bash
cd ~/android/lineage/out/target/product/fogos
ls
```

# You can see the flashable zip and .imgs

## Download the zip and .imgs if u are on vps
* Note: make sure to detach tmux if u are inside

** Install rclone**
```bash
curl https://rclone.org/install.sh | sudo bash
```
** Configure Mega/Gdrive or you can use GFdrive too lol**
```bash
rclone config
```

* configure your mega/gdrive or other cloud storage, i will be using mega (in rclone u will need mail and password and make sure to disable 2fa on mega account and create a folder called Roms)

** Install MEGA**
```bash
sudo apt install megatools
```

* make sure have configured mega in rclone properly

** Make sure ur inside right folder
```bash
cd ~/android/lineage/out/target/product/fogos
ls
```

** Upload to MEGA**
  ```bash
  rclone copy lineage-*.zip mega:/Roms/
  ```
  replace `lineage-*.zip` with your zip (full name) same for boot.img dtbo.img vendor_boot.img

## Install the build

**I need to explain this too huh?**









