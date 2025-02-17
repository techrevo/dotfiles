# Setting Up a Lightweight Labwc Desktop Environment on Debian

This guide will walk you through setting up a **lightweight** and **fully functional** desktop environment using **Labwc** on **Debian**. An equivalent installation can also be performed on other operating systems by searching for the relevant packages in the supported repositories.

---

## **Step 1: Update Your System**
Before installing anything, update your system:
```sh
sudo apt update && sudo apt upgrade -y
```

---

## **Step 3: Install Labwc**

Labwc is a window manager for **Wayland**, so you need additional tools for a functional setup.

### **Option 1: Install Labwc from Debian Sid (Recommended)**
Labwc is available in the Debian **Sid** (unstable) repository. We will temporarily enable Sid to install it.

#### **1️⃣ Add the Debian Sid Repository Temporarily**
```sh
echo "deb http://deb.debian.org/debian sid main" | sudo tee /etc/apt/sources.list.d/sid.list
```

#### **2️⃣ Prevent Full System Upgrades from Sid**
```sh
echo 'Package: *' | sudo tee /etc/apt/preferences.d/sid

echo 'Pin: release a=sid' | sudo tee -a /etc/apt/preferences.d/sid

echo 'Pin-Priority: 100' | sudo tee -a /etc/apt/preferences.d/sid
```

#### **3️⃣ Install Labwc from Sid**
```sh
sudo apt update
sudo apt install -t sid labwc
```

#### **4️⃣ Remove the Sid Repository (Optional but Recommended)**
```sh
sudo rm /etc/apt/sources.list.d/sid.list
sudo rm /etc/apt/preferences.d/sid
sudo apt update
```

### **Option 2: Compile Labwc from Source**
If you prefer to compile the latest version from source, follow these steps:

#### **1️⃣ Install Dependencies**
```sh
sudo apt install build-essential meson ninja-build pkg-config \
  libwayland-dev libxkbcommon-dev libpango1.0-dev libcairo2-dev \
  libgdk-pixbuf2.0-dev libglib2.0-dev libxml2-dev scdoc git
```

#### **2️⃣ Clone the Labwc Repository**
```sh
git clone https://github.com/labwc/labwc.git
cd labwc
```

#### **3️⃣ Compile and Install**
```sh
meson setup build
ninja -C build
sudo ninja -C build install
```

Now you can start **Labwc** with:
```sh
labwc
```

---

## **Step 4: Copy Labwc Configuration**

Clone this repository and select LabWC folder, then copy ```.config/labwc``` into your home directory:
```sh
mkdir -p ~/.config/labwc
cp labwc/.config/labwc ~/.config/labwc/
```

Start Labwc manually:
```sh
labwc
```

To launch Labwc automatically at login, create an **X session script**:
```sh
echo "exec labwc" > ~/.xsession
```
Install Xinit for managing login sessions:
```sh
sudo apt install xinit
```
Start Labwc with:
```sh
startx
```

---

## **Step 5: Configure Waybar (Status Bar)**
Labwc does not include a status bar, so we use **Waybar**. Create the configuration folder:
```sh
mkdir -p ~/.config/waybar
nano ~/.config/waybar/config.jsonc
```
Paste this configuration:
```json
{
    "layer": "top",
    "modules-left": ["clock", "network"],
    "modules-center": ["cpu", "memory"],
    "modules-right": ["pulseaudio", "battery"],
    "clock": { "format": "{:%H:%M:%S}" },
    "network": { "format-wifi": "  {essid} {signalStrength}%", "format-ethernet": "  {ipaddr}" },
    "cpu": { "format": "  {usage}%" },
    "memory": { "format": "  {used}MiB" },
    "pulseaudio": { "format": "  {volume}%" },
    "battery": { "format": "  {capacity}%" }
}
```
Start **Waybar**:
```sh
waybar &
```

---

## **Step 6: Configure Wofi (Application Launcher)**

Create the Wofi configuration folder:
```sh
mkdir -p ~/.config/wofi
nano ~/.config/wofi/config
```
Paste this configuration:
```ini
[wofi]
width=600
height=400
columns=1
prompt=Run:
show=drun
allow_images=true
insensitive=false
no_actions=false
term=foot
```

### **Bind Wofi to Super+D**
Modify the Labwc keybindings:
```sh
nano ~/.config/labwc/rc.xml
```
Find the `<keyboard>` section and add:
```xml
<keybind key="Super+D">
    <action name="execute">
        <command>wofi --show drun</command>
    </action>
</keybind>
```
Reload Labwc:
```sh
pkill labwc && labwc
```
Now, press **Super + D** to launch Wofi.

---

## **Step 7: Improve UI Appearance**
Install **LXAppearance** and **Papirus icon theme**:
```sh
sudo apt install -y lxappearance papirus-icon-theme
```
Run:
```sh
lxappearance
```
Select a **GTK theme and icon pack**.

---

## **Step 8: Autostart Essential Applications**
To start **Waybar, Foot, and PCManFM** at login, create an autostart script:
```sh
mkdir -p ~/.config/labwc
nano ~/.config/labwc/autostart
```
Add:
```sh
waybar &
foot --server &
pcmanfm --daemon &
```

---

## **Step 8: Restart and Test**
Reboot your system:
```sh
reboot
```

Or restart Labwc without rebooting:
```sh
pkill labwc && labwc
```

---

# 🎉 Final Setup

You now have a **fast and lightweight Wayland desktop environment** with:
- **Labwc** as the Window Manager
- **Waybar** as the Status Bar
- **Foot** as the Terminal
- **PCManFM** as the File Manager
- **Wofi** as the Application Launcher

If you need more customization, let me know! 🚀

