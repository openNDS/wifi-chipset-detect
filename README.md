## Wi-fi Chipset Detect

***WiFi chipset + driver detection script – works with radios enabled or disabled.***

This project provides a stand alone script for OpenWrt to detect details of the physical wireless hardware without requiring the radios to be enabled. There are no dependencies over and above the basic OpenWrt flash image.

It is based on functionality originally built into the `apmond` daemon which was later incorporated into the Mesh11sd package where it reports radio capabilities and client connections on remote mesh nodes, collected in a central database residing on the mesh portal.

This package provides a json formatted output to the terminal screen and in the file `/tmp/wifidetect`

Most of the wireless hardware types are detected, due mostly to the co-operation of the members of the OpenWrt Forum testing their routers.

The thread can be seen here:

https://forum.openwrt.org/t/wifi-chipset-driver-detection-script-works-with-radios-enabled-or-disabled-please-show-your-test-outputs-nov-2025


