# Case 001 - Windows PC Connected to Wi‑Fi but Unable to Access the Internet

## Status

✅ Resolved

--- 

## Date

October 18, 2026

---

## Environment

- Windows 11 Desktop
- Intel Wireless-AC 9560 Wireless Adapter
- Home Wi-Fi Network

> Note: All IP addresses, MAC addresses, SSIDs, and network identifiers have been replaced with example values for privacy.

---

## Problem

A Windows desktop successfully connected to Wi-Fi but could not access the internet.

Other devices connected to the same Wi-Fi network had internet access without any issues.

The same desktop could connect to a mobile hotspot and browse the internet normally.

---

## Initial Symptoms

- Connected to Wi-Fi successfully
- Received a valid IP address
- Browser showed no internet access
- Unable to browse websites
- Unable to reach the default gateway
- Laptop and smartphone worked normally on the same network
- Mobile hotspot worked normally

---

## Investigation

### Step 1 - Verify IP Configuration

Command:

```cmd
ipconfig
```

Result:

```text
IPv4 Address: 192.168.10.100
Subnet Mask: 255.255.255.0
Default Gateway: 192.168.10.1
```

Finding:

The computer received a valid IP address and gateway from DHCP.

Conclusion:

✅ DHCP was working correctly.

---

### Step 2 - Test Communication with the Gateway

Command:

```cmd
ping 192.168.10.1
```

Result:

```text
Request timed out.
```

Finding:

The computer could not communicate with the default gateway.

Conclusion:

❌ Local network communication problem suspected.

---

### Step 3 - Verify ARP Resolution

Command:

```cmd
arp -a
```

Result:

```text
192.168.10.1 aa-bb-cc-dd-ee-ff dynamic
```

Finding:

The router's MAC address was successfully learned.

Conclusion:

✅ Layer 2 communication was functioning.

✅ The wireless adapter could see the router.

---

### Step 4 - Test Internet Connectivity

Command:

```cmd
ping 8.8.8.8
```

Result:

```text
Request timed out.
```

Finding:

The computer could not reach external networks.

---

### Step 5 - Verify Routing

Command:

```cmd
route print
```

Important Route:

```text
0.0.0.0    0.0.0.0    192.168.10.1
```

Finding:

The default route was configured correctly.

Conclusion:

✅ Routing configuration was correct.

---

### Step 6 - Verify Wireless Adapter Functionality

Connected the computer to a mobile hotspot.

Result:

```text
Internet access worked immediately.
```

Finding:

The wireless adapter functioned correctly on another network.

Conclusion:

✅ Wireless adapter healthy.

✅ Windows networking stack healthy.

✅ Issue isolated to the relationship between the desktop and the home network.

---

### Step 7 - Verify Virtual Network Adapters

Checked and disabled:

- VMware adapters
- VirtualBox adapters

Result:

```text
No change.
```

Conclusion:

✅ Virtual adapters were not causing the problem.

---

### Step 8 - Verify Wireless Information

Command:

```cmd
netsh wlan show interfaces
```

Verified:

- Connected status
- Strong signal strength
- Correct SSID
- Proper authentication settings

Conclusion:

✅ Wireless signal and connection quality were good.

---

## Root Cause Analysis

At this point:

- DHCP was working
- Routing was working
- ARP resolution was working
- The wireless adapter worked on another network
- Virtual adapters were ruled out
- Signal strength was excellent

Because the same PC worked on a mobile hotspot, attention shifted away from the wireless adapter and Windows itself.

The evidence indicated a possible issue between the router and the device's original MAC address.

---

## Resolution

A new locally administered MAC address was assigned to the wireless adapter.

Original MAC Address:

```text
AA-BB-CC-DD-EE-01
```

New MAC Address:

```text
02-A1-B2-C3-D4-E5
```

Verification Command:

```powershell
Get-NetAdapter | Format-Table Name, InterfaceDescription, MacAddress
```

After reconnecting to Wi-Fi, internet access was restored immediately.

---

## Result

✅ Internet access restored

✅ Gateway reachable

✅ Websites loaded normally

✅ Network communication restored

✅ No additional changes required

---

## Probable Root Cause

The exact root cause could not be confirmed without access to router logs.

However, evidence suggests the issue was associated with how the router handled the device's original MAC address.

Possible causes include:

- Corrupted DHCP lease
- Stale ARP entry
- MAC filtering rule
- Router firmware issue
- Corrupted device record on the router

Changing the MAC address caused the router to treat the device as a new client and rebuild its network records.

---
