# 📱 Android Home Gateway: Persistent Tailscale Subnet Router

This project transforms a rooted Android device (OnePlus 6T) into a permanent, "always-on" home network gateway. By running Tailscale as a system daemon via Magisk, we bypass Android's aggressive battery management to provide 24/7 remote access to local ISP media servers and home network resources.

---

## 🚀 Quick Start

- Install Modules: Flash `Tailscaled` and `MagiskSSH` in the Magisk app.
- Initialize: Connect via SSH and run `tailscale up` with your subnet routes.
- Protect: Install `ACCA` to lock battery charging between 70%–75%.

---

## ✨ Features

| Feature | Description |
| :--- | :--- |
| **Persistent VPN** | Runs as a Magisk daemon; survives reboots and avoids process killing. |
| **Subnet Routing** | Access your entire home network (192.168.0.0/24) from anywhere. |
| **Exit Node** | Route all internet traffic through your home ISP to access geo-blocked media. |
| **Remote Management** | Secure SSH access (Port 22) with Key-based authentication. |
| **Battery Longevity** | Automated charging control to prevent battery swelling during 24/7 plug-in. |

---

## 🛠 Installation

### 1. Prerequisites
- A rooted Android device with **Magisk** installed.
- A Tailscale account.
- SSH Keys generated on your client machine (Laptop/PC).

### 2. Install Magisk Modules
Download and install the following modules via the Magisk app:
- **[Tailscaled Magisk Module](https://github.com/anasfanani/magisk-tailscaled):** Runs the Tailscale binary as a background service.
- **[MagiskSSH](https://gitlab.com/d4rcm4rc/MagiskSSH):** Provides a robust SSH server.
- *Note: Place your public key in `/data/ssh/authorized_keys` to enable key-based auth.*

### 3. Configure Tailscale
Open a terminal (via ADB or a Terminal Emulator app) and run the following command to initialize the gateway:

```bash
# Replace <ISP_SUB_IP> with your ISP's specific media server subnet
tailscale up --advertise-routes=192.168.0.0/24,<ISP_SUB_IP> --advertise-exit-node
```

- Follow the URL provided in the output to log in.
- In the **Tailscale Admin Console**:
- Find your Android device.
- Go to **Edit Route Settings**.
- Enable all advertised subnets and the Exit Node toggle.
- Select **Disable Key Expiry** to ensure the connection never drops.

### 4. Battery Protection (ACCA)
To prevent battery degradation while the phone is plugged in 24/7:
- Install the **[ACCA App](https://f-droid.org/packages/mattecarra.accapp/)**.
- Set the **Stop Charging** limit to `75%`.
- Set the **Start Charging** limit to `70%`.
- Apply the settings (this will automatically install the necessary `acc` Magisk module).

---

## 📝 Usage

### Accessing the Phone via SSH
From your laptop (connected to Tailscale), use:
```bash
ssh root@<your-phone-tailscale-ip> -p 22
```

### Accessing Home Media Servers
Once Tailscale is active on your remote device (Laptop/Phone):
- Navigate to your ISP media server's local IP (e.g., `http://192.168.0.100`).
- The traffic will automatically route through your OnePlus 6T gateway.

### Using as an Exit Node
If you need to browse the web as if you were at home:
- **On Mobile:** Tailscale App > Three dots > Use Exit Node > Select your Android Gateway.
- **On Desktop:** Tailscale Icon > Exit Node > Select your Android Gateway.

---

## 💡 Notes & Tips

> [!IMPORTANT]
> **Why Magisk over the Play Store App?**
> Standard Android apps are subject to "Battery Optimization." Even with "Don't Optimize" checked, Android may kill the process during low memory states. The Magisk module runs at the system level (daemon), making it invisible to the Android task killer.

> [!TIP] Remote Scrcpy
> Mirror your phone's screen over the VPN by following these steps:
> 
> **1. Enable ADB over TCP/IP (Run via SSH on the phone)**
> ```bash
> setprop service.adb.tcp.port 5555
> stop adbd
> start adbd
> ```
> 
> **2. Connect and Launch (Run on your computer)**
> ```bash
> adb connect <your-phone-tailscale-ip>:5555
> scrcpy -e
> ```

> [!CAUTION]
> **Heat Management:**
> Even with ACCA, keeping a phone screen on or in a poorly ventilated area can cause heat. Keep the screen off and place the device in a cool, open area.

---

## ❓ Troubleshooting

**Q: SSH connection is refused.**
- Ensure the MagiskSSH module is active.
- Check if your public key is correctly placed in `/data/ssh/authorized_keys` with `600` permissions.
- Verify you are using Port 22.

**Q: Subnets are not reachable.**
- Verify you clicked "Approve" in the Tailscale Admin Console under "Route Settings."
- Run `tailscale status` via SSH to ensure the daemon is running.

**Q: ACCA is not stopping the charge.**
- Some kernels require a specific "Charging Switch." Open the ACCA app and run the "Switch Test" to find the one compatible with your device.