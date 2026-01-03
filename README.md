## Wi-fi Chipset Detect

***WiFi chipset + driver detection script – works with radios enabled or disabled.***

This project provides a stand alone script for OpenWrt to detect details of the physical wireless hardware without requiring the radios to be enabled. There are no dependencies over and above the basic OpenWrt flash image.

It is based on functionality originally built into the `apmond` daemon which was later incorporated into the Mesh11sd package where it reports radio capabilities and client connections on remote mesh nodes, collected in a central database residing on the mesh portal.

This package provides a json formatted output to the terminal screen and in the file `/tmp/wifidetect`

Most of the wireless hardware types are detected, due mostly to the co-operation of the members of the OpenWrt Forum testing their routers.

The thread can be seen here:

https://forum.openwrt.org/t/wifi-chipset-driver-detection-script-works-with-radios-enabled-or-disabled-please-show-your-test-outputs-nov-2025

## Key Fields Explained

The script outputs detailed information about each wireless PHY (radio) in JSON format. Here's what the most important fields mean:

| Field                  | Description                                                                 | Example Value                  | Notes / Special Cases                                                                 |
|------------------------|-----------------------------------------------------------------------------|--------------------------------|---------------------------------------------------------------------------------------|
| Bands                  | Supported frequency bands                                                   | `["2.4GHz", "5GHz"]`           | May include "6GHz" on Wi-Fi 6E/7 hardware                                              |
| Standards              | Detected 802.11 standards (filtered: no "ac" on 2.4 GHz)                    | `["802.11n", "802.11ax"]`      | "ac" is 5 GHz only; automatically removed on 2.4 GHz PHYs                            |
| Wi-FiGeneration        | Friendly Wi-Fi generation name                                              | `"Wi-Fi 6"`                    | Derived from standards (e.g., "Wi-Fi 6" if ax present, "Wi-Fi 7" if be)               |
| MIMO                   | Spatial streams (from MCS rates)                                            | `"2x2"`                        | Number of transmit/receive chains                                                     |
| Antennas               | Physical antenna count (from TX/RX mask)                                    | `"2x2"`                        | Usually matches MIMO, but may differ due to antenna config                            |
| MaxChannelWidth        | Practical maximum channel width (capped where known unstable)               | `"80 MHz"`                     | Realistic limit — may be lower than driver-reported value                             |
| ChannelWidthCap        | `true` if we applied a practical limit (e.g. stability on mt7615e)          | `true` / `false`               | Indicates when the value was adjusted for real-world usability                        |
| TXQS                   | FQ-CoDel-enabled intermediate TXQs support                                  | `true` / `false`               | Helps reduce bufferbloat and latency under load                                       |
| AIRTIME_FAIRNESS       | Airtime fairness scheduling support                                         | `true` / `false`               | Prevents slow clients from monopolizing airtime; improves mixed-client performance   |
| AQL_Extended           | Airtime Queue Limits (AQL) as a built-in extended feature                   | `true` / `false`               | From `iw phy info` — always-on if true                                                |
| AQL_Runtime            | AQL can be enabled at runtime (via debugfs/sysfs)                           | `true` / `false`               | Useful for mesh11sd or custom QoS scripts; common on ath11k/mt76                     |
| TheoreticalMaxMbps     | Rough theoretical maximum throughput per PHY (upper bound, Mbps)                   | `2400`                         | Reference only — real-world could be up to 50–70% lower due to overhead, interference, etc.       |
| Aggregated_MLO_MaxMbps     | Aggregated Multi Link Operation maximum rate Mbps (802.11be onwards) | `14040`                         | Reference only — real-world could be up to 50–70% lower due to overhead, interference, etc.       |
| Driver                 | Kernel driver in use                                                        | `"ath11k"`                     | Helps identify chipset family                                                         |

**Important notes**:

- **AIRTIME_FAIRNESS**:  
 ***Commonly true*** on MediaTek drivers (mt76, mt79xx series) — fully supported at the driver level.  
 ***Usually false*** on Qualcomm drivers (ath10k, ath11k) — the feature is **not exposed** in the open-source driver, even though the underlying firmware may implement some form of fairness internally. This is a known limitation of the current ath10k/ath11k implementations in OpenWrt.
- **AQL_Runtime**:  
 ***true*** on ath9k, ath10k, ath11k, and most mt76/mt79xx drivers — AQL can be manually enabled/tuned via debugfs/sysfs (great for mesh11sd or custom QoS).  
 ***false*** on very old kernels or drivers without the required backport.
- **ChannelWidthCap**:  
 ***true*** means the reported width was adjusted downward for stability (e.g., mt7615e reports 160 MHz but is limited in practice to 80 MHz).
- **TheoreticalMaxMbps**:  
 This is a rough upper bound based on MIMO streams, channel width, and standards. Real-world achieved speeds are unlikely to reach this value.
- **Aggregated_MLO_MaxMbps**:  
 This is a rough upper bound based on Multi Link Operation in 802.11be onwards. Real-world achieved speeds are unlikely to reach this value.
- **All values are detected** from `iw phy <phyname> info` and debugfs/sysfs — reliable even when radios are disabled.

Feel free to suggest improvements or report issues here.

### Example outputs ###

*Detect installed chipsets and drivers*:
```
root@meshnode-2a52:/tmp# wifi-chipset-detect
{
  "System@94:83:c4:5c:2a:51": {
    "Distribution": "OpenWrt",
    "Release": "SNAPSHOT",
    "Revision": "r29091-7aa3dfdbda",
    "Target": "qualcommax/ipq60xx",
    "Architecture": "aarch64_cortex-a53",
    "Description": "OpenWrt SNAPSHOT r29091-7aa3dfdbda",
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
        "Wi-FiGeneration": "Wi-Fi 6",
        "MIMO": "2x2",
        "MaxChannelWidth": "160 MHz",
        "ChannelWidthCap": false,
        "Antennas": "2x2",
        "TheoreticalMaxMbps": "2400",
        "TXQS": true,
        "AIRTIME_FAIRNESS": false,
        "AQL_Extended": false,
        "AQL_Runtime": true,
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
        "Wi-FiGeneration": "Wi-Fi 6",
        "MIMO": "2x2",
        "MaxChannelWidth": "40 MHz",
        "ChannelWidthCap": false,
        "Antennas": "2x2",
        "TheoreticalMaxMbps": "600",
        "TXQS": true,
        "AIRTIME_FAIRNESS": false,
        "AQL_Extended": false,
        "AQL_Runtime": true,
        "Driver": "ath11k"
      }
    }
  }
}
root@meshnode-2a52:/tmp# 

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
        "Wi-FiGeneration": "Wi-Fi 6",
        "MIMO": "4x4",
        "MaxChannelWidth": "40 MHz",
        "ChannelWidthCap": false,
        "Antennas": "4x4",
        "TheoreticalMaxMbps": "1200",
        "TXQS": true,
        "AIRTIME_FAIRNESS": true,
        "AQL_Extended": true,
        "AQL_Runtime": true,
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
        "Wi-FiGeneration": "Wi-Fi 6",
        "MIMO": "4x4",
        "MaxChannelWidth": "160 MHz",
        "ChannelWidthCap": false,
        "Antennas": "4x4",
        "TheoreticalMaxMbps": "4800",
        "TXQS": true,
        "AIRTIME_FAIRNESS": true,
        "AQL_Extended": true,
        "AQL_Runtime": true,
        "Driver": "mt798x-wmac"
      }
    }
  }
}
root@meshnode-8ecb:~# cat /tmp/wifi-chipset.debug
date_ver="Wed Dec 24 00:24:57 GMT 2025 - wifi-chipset-detect Version 1.0.0~be7a"
labelmac="94:83:c4:a2:8e:c9"
device="GL.iNet GL-MT6000"
phyname="phy0"
default_driver="mt798x-wmac"
per_band_detection_bands="2.4GHz"
per_band_detection_standards="n ac ax"
bands="2.4GHz"
standards="n ax"
wifigen="Wi-Fi 6"
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
wifigen="Wi-Fi 6"
mesh="yes"
default_chipset_detect=""
filogic_modalias="of:NwifiT%28null%29Cmediatek,mt7986-wmac"
filogic_chipset="MediaTek Filogic 830/810/880 %28MT798x integrated WiFi%29"
root@meshnode-8ecb:~# 

```


