# Setting Up a Lightweight Labwc Desktop Environment on Debian

This guide will walk you through setting up a **lightweight** and **fully functional** desktop environment using **Labwc** on **Debian**.

---

## **Step 1: Update Your System**
Before installing anything, update your system:
```sh
sudo apt update && sudo apt upgrade -y
```

---

## **Step 2: Install Labwc and Essential Packages**

Labwc is a window manager for **Wayland**, so you need additional tools for a functional setup.

### **Option 1: Install Labwc from External Repository (Recommended)**
Since Labwc is not included in the official Debian repositories, you can install it from the **SwayOS** repository.

#### **1️⃣ Add the Repository**
```sh
echo "deb [signed-by=/usr/share/keyrings/swayos-archive-keyring.gpg] https://debian.swayos.dev bookworm main" | sudo tee /etc/apt/sources.list.d/swayos.list
```

#### **2️⃣ Add the GPG Key**
```sh
wget -O- https://debian.swayos.dev/key.asc | sudo tee /usr/share/keyrings/swayos-archive-keyring.gpg
```

#### **3️⃣ Update and Install Labwc**
```sh
sudo apt update
sudo apt install labwc
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

## **Step 3: Create Labwc Configuration**

Create the default configuration:
```sh
mkdir -p ~/.config/labwc
cp /etc/xdg/labwc/*.xml ~/.config/labwc/
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

## **Step 4: Configure Waybar (Status Bar)**
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

## **Step 5: Configure Wofi (Application Launcher)**

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

## **Step 6: Improve UI Appearance**
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

## **Step 7: Autostart Essential Applications**
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

