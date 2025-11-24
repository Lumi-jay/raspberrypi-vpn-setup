## 1️⃣ VPN Configuration Lab
➡ [View VPN Lab Documentation](./01-VPN-Lab/README.md)
# Raspberry Pi VPN Setup Using WireGuard  

This project documents my hands-on experience  **setting up, configuring, and troubleshooting a VPN on Raspberry Pi using WireGuard**. This walkthrough is based on Wolfgang’s tutorial, with my own notes, fixes, and understanding. It highlights practical experience in **network configuration, Linux commands, and secure connectivity**.  

---

## 🎯 Objective  
- Build a **self-hosted VPN server** for secure remote access  
- Learn about **DNS, encryption, and IP routing**  
- Document each step as a cybersecurity learning project  

---

## ⚙️ Setup Used  
- Raspberry Pi 4 Model B  
- Raspberry Pi OS Lite (64-bit)  
- Wi-Fi connection (no Ethernet)  
- Router with port forwarding enabled  
- My public IP from ISP  
- FreeDNS account for Dynamic DNS  
- WireGuard clients: Android phone & laptop  
---

## 🪜 Step-by-Step Configuration  

### 1. Prepare Raspberry Pi
- Used an existing Raspberry Pi with Raspberry Pi OS already installed
- Connected via SSH to the Pi
- Ensured WireGuard and required tools were installed after ssh connection
  ```bash
  ssh pi@<raspberrypi_ip>

 Updated system:
 
 sudo apt update && sudo apt upgrade -y

### 2. Configure Dynamic DNS (FreeDNS + ddclient)
- Registered a domain at FreeDNS
- Installed ddclient:
  ```bash
  sudo apt install ddclient
  
- Edited the ddclient configuration:

      sudo nano /etc/ddclient.conf


- Example configuration:

      use=if, if=eth0 
      server=freedns.afraid.org 
      protocol=freedns 
      login=YOUR_FREEDNS_LOGIN 
      password=YOUR_FREEDNS_PASSWORD
      your-subdomain.mooo.com


- Restarted the ddclient service:

      sudo systemctl restart ddclient

### 3. Router Port Forwarding
1. Logged into my router’s web interface.
2. Located the **Port Forwarding** section.
3. Created a new rule to forward **UDP port 51820 (WireGuard default port)** to the Raspberry Pi’s local IP address.
4. Saved the changes and applied the rule.
5. Verified that the port forwarding rule was active and correctly pointing to the Pi.

### 4. Install and Configure WireGuard
1. **Download and run the WireGuard installer**
```
wget https://git.io/wireguard -O wireguard-install.sh && sudo bash wireguard-install.sh
```
- `wget` downloads the installer script from the web.
- `sudo bash wireguard-install.sh` runs the script with administrative privileges to install WireGuard and generate configuration files.

2. **Enter domain and client name**
- Entered **FreeDNS domain** when prompted (so WireGuard knows which public IP to connect to).
- Entered a **client name**, like `phone` or `laptop`, which creates a unique configuration for that device.

3. **Choose DNS server**
- Selected **Google DNS (8.8.8.8)** for reliable name resolution, so devices can properly resolve internet addresses while connected to the VPN.

4. **Scan the QR code with the WireGuard app**
- The installer generates a **QR code** containing the client configuration.
- Scanning it in the WireGuard app on your phone or laptop automatically sets up the VPN client without manually entering keys or settings.

## 5. Connect and Test VPN  

### Mobile Connection  

1. **Installed WireGuard App**  
   - Downloaded the WireGuard mobile app (iOS or Android).  
   - This app securely manages the VPN configuration and keys.  

2. **Scanned QR Code**  
   - Scanned the QR code generated from the Raspberry Pi setup.  
   - The app imported the configuration, which included:  
     - Server public key  
     - Server endpoint (FreeDNS domain or DDNS)  
     - Allowed IPs (e.g., `0.0.0.0/0`)  
     - Client’s private key  

3. **Turned Off Wi-Fi**  
   - Switched off Wi-Fi to test the VPN connection through mobile data.  
   - This ensured the connection worked from outside the local network.  

4. **Connected to the VPN**  
   - Toggled the WireGuard connection switch to “On.”  
   - The phone established a secure handshake with the Raspberry Pi on UDP port **51820**.  
   - Traffic was now encrypted and routed through the Raspberry Pi.  

5. **Verified VPN Connection**  
   - Visited [whatismyipaddress.com](https://whatismyipaddress.com).  
   - Confirmed the public IP matched the Raspberry Pi’s network instead of the mobile carrier.  
   - This confirmed that all traffic was passing securely through the VPN tunnel.  







