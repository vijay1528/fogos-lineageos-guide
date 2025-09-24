![image](https://github.com/user-attachments/assets/8ca0811e-b031-4fe4-8ded-b322b9fdb7c7)

## Build LineageOS For Moto g45/g34 (fogos)
* This guide follows LineageOS official [guide](https://wiki.lineageos.org/devices/fogos/build/variant2)
* I'm creating this to make rom building more user friendly

* You can use this guide for other official devices too, will just need to tweak `Initialize the LineageOS source repository` & `Get/Extract proprietary blobs` & `codename` according to your device. check your device's official lineageos guide for more clearance

## Disclaimer / Note:-
* **Look i'm not responsible for bricked devices, dead phone or your girlfriend ran away with someone.**
* **You are going through this guide bcoz u wanna use your free time to try building custom rom for your `OWN` device repeat your `own` device.**
* **Your warranty will be void if you fuck with your device or its software haha..**
* **If i have missed some linux commands/dependencies, spelling mistakes then correct yourself or u can check lineage official guide.**

# Requirements

* Ubunbtu Linux prefer 22.04 LTS or newer
* +62gb ram, +400gb storage nvme/ssd, +12 CPU cores
* If u are going for VPS then prefer Baremetal/dedicated VPS

* Note: these are just recommendations

## READ ALL THE WORDS AND LINES CAREFULLY

## Basic configuration & setup

**Update System Packages**
```bash
apt update && apt install -y sudo
sudo apt update && sudo apt upgrade -y
```

**Install General Utilities and Tools**
```bash
sudo apt update && sudo apt install -y autoconf automake bsdmainutils build-essential clang curl ffmpeg gcc git htop jq libffi-dev libgbm1 libjpeg-dev libleveldb-dev libopenblas-dev libsqlite3-dev libssl-dev lsof lz4 make nano ncdu nvme-cli pkg-config screen tar tmux unzip wget zlib1g-dev iptables -y
```

**LineageOS Dependencies**
```bash
sudo apt update && sudo apt install bc bison build-essential ccache curl flex g++-multilib gcc-multilib git git-lfs gnupg gperf imagemagick protobuf-compiler python3-protobuf lib32readline-dev lib32z1-dev libdw-dev libelf-dev lz4 libsdl1.2-dev libssl-dev libxml2 libxml2-utils lzop pngcrush rsync schedtool squashfs-tools xsltproc zip zlib1g-dev -y
```

**For Ubuntu 23.10 (mantic) or newer, install libncurses5 from 23.04 (lunar) as follows**
```bash
# use only if u are on ubuntu 23 or newer
wget https://archive.ubuntu.com/ubuntu/pool/universe/n/ncurses/libtinfo5_6.3-2_amd64.deb && sudo dpkg -i libtinfo5_6.3-2_amd64.deb && rm -f libtinfo5_6.3-2_amd64.deb
wget https://archive.ubuntu.com/ubuntu/pool/universe/n/ncurses/libncurses5_6.3-2_amd64.deb && sudo dpkg -i libncurses5_6.3-2_amd64.deb && rm -f libncurses5_6.3-2_amd64.deb
```

**While for Ubuntu versions older than 23.10 (mantic), like 22.04 (mine)**
```bash
# for ubuntu 22
sudo apt update && sudo apt install lib32ncurses5-dev libncurses5 libncurses5-dev -y
```

**Java**
```bash
sudo apt update && sudo apt install openjdk-11-jdk -y
```

**Install Python**
```bash
sudo apt install python3 python3-pip python3-venv python3-dev -y
sudo apt install python-is-python3
```

**Create the directories**
```bash
mkdir -p ~/bin
mkdir -p ~/android/lineage
```

**Install the repo command**
```bash
curl https://storage.googleapis.com/git-repo-downloads/repo > ~/bin/repo
chmod a+x ~/bin/repo
```

**Put the ~/bin directory in your path of execution**
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

**Configure git**
```bash
# Do not remove quotes
git config --global user.email "you@example.com"
git config --global user.name "YourUserNameNotMine"
```
* replace you@example.com and YourUserNameNotMine with your github email and username

```bash
git lfs install
```
```bash
git config --global trailer.changeid.key "Change-Id"
```

**Turn on caching to speed up build**
```bash
export USE_CCACHE=1
export CCACHE_EXEC=/usr/bin/ccache

echo 'export USE_CCACHE=1' >> ~/.bashrc
echo 'export CCACHE_EXEC=/usr/bin/ccache' >> ~/.bashrc
source ~/.bashrc

ccache -M 50G

# Optional
ccache -o compression=true
```

## Initialize the LineageOS source repository
* Note: this is for lineage ver 22.2

```bash
cd ~/android/lineage
repo init -u https://github.com/LineageOS/android.git -b lineage-22.2 --git-lfs --no-clone-bundle
```
Enable color display in this user account (y/N)? type `y` then `enter`

**Download the source code**
* Note: if you are on vps use tmux coz this will take time

`tmux new -s session_name` for new session where `session_name` can be your choice for example `fogos`
  
`Ctrl + b, release, then press d` for deattachment, To leave tmux but keep it running in the background

`tmux attach -t session_name` for reattachment

```bash
repo sync
```

**Get/Extract proprietary blobs**
* Note: you can go through official methods from [LineageOS](https://wiki.lineageos.org/devices/fogos/build/variant2/#extract-proprietary-blobs)
* But in this guide im using [fogos](https://github.com/TheMuppets/proprietary_vendor_motorola_fogos) and [sm6375-common](https://github.com/TheMuppets/proprietary_vendor_motorola_sm6375-common) managed by his highness [mikeNG](https://github.com/mikeNG)

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

**Preparing the build environment**
```bash
cd ~/android/lineage
source build/envsetup.sh
croot
```

**Prepare the device-specific code**
* Note: This will download your device’s [device specific configuration](https://github.com/LineageOS/android_device_motorola_fogos) and [kernel](https://github.com/LineageOS/android_kernel_motorola_sm6375)
* There's another manual method to do this but go with official
```bash
breakfast fogos
```

## Start the build
* Note: this will take time if u are on vps use tmux

```bash
# only if u are on dedicated vps or local hardware
croot
brunch fogos
```

Note: if u are on non dedicated vps then use ``croot && mka bacon -jn`` where ``-jn`` defines parallel jobs, suppose im on 12core 62gb ram vps then i will use -j12, change `-jn` according to your setup

max_jobs_cpu = 12 × 1.5 = 18

max_jobs_ram = 62 / 5 ≈ 12

-jN = min(18, 12) = 12 → safe

So using -j12 or even -j11 is safe

## Find the zip and .imgs

```bash
cd $OUT
```
or
```bash
cd ~/android/lineage/out/target/product/fogos
ls
```

**You can see the flashable zip and .imgs**

## Download the zip and .imgs if u are on vps
* Note: make sure to detach tmux if u are inside

**Install rclone**
```bash
curl https://rclone.org/install.sh | sudo bash
```
**Configure Mega/Gdrive**
```bash
rclone config
```

* configure your mega/gdrive or other cloud storage using common sense, i will be using mega (in rclone u will need mail and password and make sure to disable 2fa on mega account and create a folder called Roms)

**Install MEGA**
```bash
sudo apt install megatools
```

* make sure have configured mega in rclone properly

**Make sure ur inside right folder**
```bash
cd ~/android/lineage/out/target/product/fogos
ls
```

**Upload to MEGA**
  ```bash
  rclone copy lineage-*.zip mega:/Roms/
  ```
  replace `lineage-*.zip` with your zip (full name) same for boot.img dtbo.img vendor_boot.img

# Install the build

## Unlock your phone's bootloader and follow official lineageOS installation [guide](https://wiki.lineageos.org/devices/fogos/install/variant2)










