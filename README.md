# handshake-harvester-auto
WiFi Handshake Capture & Cracking Tool

This is a lightweight Bash script designed to automate the process of capturing WPA/WPA2 handshakes from nearby WiFi networks and optionally attempting to crack them using a wordlist. The script handles all the tedious steps — from enabling monitor mode to scanning for networks, deauth attacks, and handshake verification — with minimal user intervention.

Features:

-Automatically detects available wireless interfaces.

-Scans for nearby WPA/WPA2 networks and lists their BSSID, channel, and SSID.

-Performs deauthentication attacks to capture handshakes efficiently.

-Supports both aircrack-ng and hashcat for cracking captured handshakes.

-Logs all activity for review and debugging.

-Clean and safe cleanup routine to restore your system’s network state.

-Modular design for easy customization and extension.

-Usage:

sudo ./wpa_capture.sh [wordlist] [interface]


-wordlist (optional): Path to the wordlist you want to use for cracking (default: rockyou.txt).

-interface (optional): Wireless interface to use (default: first available interface).

Note:
This script is intended for educational purposes and authorized penetration testing only. Always have permission before testing any network.
