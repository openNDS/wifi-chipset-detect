## Wi-fi Chipset Detect

***WiFi chipset + driver detection script – works with radios enabled or disabled.***

This project provides a stand alone script for OpenWrt to detect details of the physical wireless hardware without requiring the radios to be enabled. There are no dependencies over and above the basic OpenWrt flash image.

It is based on functionality originally built into the `apmond` daemon which was later incorporated into the Mesh11sd package where it reports radio capabilities and client connections on remote mesh nodes, collected in a central database residing on the mesh portal.

This package provides a json formatted output to the terminal screen and in the file `/tmp/wifidetect`

Most of the wireless hardware types are detected, due mostly to the co-operation of the members of the OpenWrt Forum testing their routers.

The thread can be seen here:

https://forum.openwrt.org/t/wifi-chipset-driver-detection-script-works-with-radios-enabled-or-disabled-please-show-your-test-outputs-nov-2025

### Example outputs ###

*Detect installed chipsets and drivers*:
```
root@meshnode-256d:~# wifi-chipset-detect
{
  "System@94:83:c4:5c:25:6d": {
    "Distribution": "OpenWrt",
    "Release": "SNAPSHOT",
    "Revision": "r31119-fe27cce1ec",
    "Target": "qualcommax/ipq60xx",
    "Architecture": "aarch64_cortex-a53",
    "Description": "OpenWrt SNAPSHOT r31119-fe27cce1ec",
    "Device": "GL.iNet GL-AXT1800",
    "phy": {
      "phy0": {
        "Chipset": "Qualcomm QCN9074",
        "Bands": [
          "5GHz"
        ],
        "Standards": [
          "802.11n",
          "802.11ac",
          "802.11ax"
        ],
        "MeshPoint": "802.11s",
        "Driver": "ath11k"
      },
      "phy1": {
        "Chipset": "Qualcomm QCN9074",
        "Bands": [
          "2.4GHz"
        ],
        "Standards": [
          "802.11n",
          "802.11ax"
        ],
        "MeshPoint": "802.11s",
        "Driver": "ath11k"
      }
    }
  }
}
root@meshnode-256d:~# 

```
***Show the version*:***
```
root@meshnode-256d:~# wifi-chipset-detect -v
wifi-chipset-detect: version 1.0.0
root@meshnode-256d:~# 

```
***Write debug information to /tmp/wifi-chipset.debug***:
```
root@meshnode-8ecb:~# wifi-chipset-detect debug
{
  "System@94:83:c4:a2:8e:c9": {
    "Distribution": "OpenWrt",
    "Release": "SNAPSHOT",
    "Revision": "r31857-501f4edb04",
    "Target": "mediatek/filogic",
    "Architecture": "aarch64_cortex-a53",
    "Description": "OpenWrt SNAPSHOT r31857-501f4edb04",
    "Device": "GL.iNet GL-MT6000",
    "phy": {
      "phy0": {
        "Chipset": "MediaTek Filogic 830/810/880 (MT798x integrated WiFi)",
        "Bands": [
          "2.4GHz"
        ],
        "Standards": [
          "802.11n",
          "802.11ax"
        ],
        "MeshPoint": "802.11s",
        "Driver": "mt798x-wmac"
      },
      "phy1": {
        "Chipset": "MediaTek Filogic 830/810/880 (MT798x integrated WiFi)",
        "Bands": [
          "5GHz"
        ],
        "Standards": [
          "802.11n",
          "802.11ac",
          "802.11ax"
        ],
        "MeshPoint": "802.11s",
        "Driver": "mt798x-wmac"
      }
    }
  }
}
root@meshnode-8ecb:~# 
root@meshnode-8ecb:~# cat /tmp/wifi-chipset.debug
date_ver="Sun Dec 21 19:22:52 GMT 2025 - wifi-chipset-detect Version 1.0.0"
labelmac="94:83:c4:a2:8e:c9"
device="GL.iNet GL-MT6000"
phyname="phy0"
default_driver="mt798x-wmac"
per_band_detection_bands="2.4GHz"
per_band_detection_standards="n ax"
bands="2.4GHz"
standards="n ax"
mesh="yes"
default_chipset_detect=""
filogic_modalias="of:NwifiT%28null%29Cmediatek,mt7986-wmac"
filogic_chipset="MediaTek Filogic 830/810/880 %28MT798x integrated WiFi%29"
phyname="phy1"
default_driver="mt798x-wmac"
per_band_detection_bands="5GHz"
per_band_detection_standards="n ac ax"
bands="5GHz"
standards="n ac ax"
mesh="yes"
default_chipset_detect=""
filogic_modalias="of:NwifiT%28null%29Cmediatek,mt7986-wmac"
filogic_chipset="MediaTek Filogic 830/810/880 %28MT798x integrated WiFi%29"
root@meshnode-8ecb:~# 


```


