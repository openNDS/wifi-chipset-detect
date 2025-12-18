# SPDX-License-Identifier: GPL-2.0-only
#
# This is free software, licensed under the GNU General Public License v2.
# See /LICENSE for more information.
# Copyright (C) 2025 BlueWave Projects and Services  <licence@blue-wave.net>
#

include $(TOPDIR)/rules.mk

PKG_NAME:=wifi-chipset-detect
PKG_VERSION:=1.0.0
PKG_RELEASE:=1

PKG_MAINTAINER:=Rob White <rob@blue-wave.net>
PKG_LICENSE:=GPL-2.0-or-later
PKG_LICENSE_FILES:=LICENSE

PKG_SOURCE:=$(PKG_NAME)-$(PKG_VERSION).tar.gz
PKG_SOURCE_URL:=https://codeload.github.com/opennds/wifi-chipset-detect/tar.gz/v$(PKG_VERSION)?
PKG_HASH:=#shasum -a 256 of tar.gz of source files goes here
PKG_BUILD_DIR:=$(BUILD_DIR)/wifi-chipset-detect-$(PKG_VERSION)

include $(INCLUDE_DIR)/package.mk

define Package/wifi-chipset-detect
  SUBMENU:=Captive Portals
  SECTION:=net
  CATEGORY:=Network
  TITLE:=Wireless Chipset Detection Utility
  PKGARCH:=all
  URL:=https://github.com/opennds/wifi-chipset-detect
endef

define Package/wifi-chipset-detect/description
  WiFi chipset + driver detection script – works with radios enabled or disabled.
  It provides a stand alone script to detect details of the physical wireless hardware without requiring the radios to be enabled.
  There are no dependencies over and above the basic OpenWrt flash image.
  It is based on functionality originally built into the apmond daemon and incorporated into the OpenNDS and Mesh11sd packages.
  This package provides a json formatted output to the terminal screen and in the file /tmp/wifidetect
  This version does not require the Captive Portal or Mesh network to be running.
endef

define Package/wifi-chipset-detect/install
	$(INSTALL_DIR) $(1)/usr/sbin
	$(INSTALL_BIN) $(PKG_BUILD_DIR)/src/wifi-chipset-detect $(1)/usr/sbin
endef

define Build/Compile
endef

$(eval $(call BuildPackage,wifi-chipset-detect))
