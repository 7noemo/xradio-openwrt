#
# OpenWrt package: kmod-xradio
# Out-of-tree SDIO Wi-Fi driver for XRadio/Allwinner XR819
# (Orange Pi Zero onboard Wi-Fi, Allwinner H2+/H3)
#
# Driver source: https://github.com/fifteenhex/xradio
# (mainline port of the Allwinner XR819 driver, actively maintained,
#  adapted to kernel 6.17+)
#
# Firmware: xr819 boot/fw/sdd blobs -> /lib/firmware/xr819/
#

include $(TOPDIR)/rules.mk
include $(INCLUDE_DIR)/kernel.mk

PKG_NAME:=xradio
PKG_VERSION:=3ce657ae533e2213f6fda2297da1598fa11d7a03
PKG_RELEASE:=1

PKG_SOURCE_PROTO:=git
PKG_SOURCE_URL:=https://github.com/fifteenhex/xradio
PKG_SOURCE_VERSION:=$(PKG_VERSION)
PKG_MIRROR_HASH:=skip

PKG_BUILD_DIR:=$(KERNEL_BUILD_DIR)/$(PKG_NAME)-$(PKG_VERSION)
PKG_MAINTAINER:=7noemo
PKG_LICENSE:=GPL-2.0

# Ensure mac80211 backport headers are installed before configuring
STAMP_CONFIGURED_DEPENDS := $(STAGING_DIR)/usr/include/mac80211-backport/backport/autoconf.h

include $(INCLUDE_DIR)/package.mk

define Download/xr819fw
  FILE:=xr819.tar.bz2
  URL:=https://github.com/melsem/openwrt-lede_xradio-xr819_soc-audio/raw/master/firmware/
  HASH:=bc9bae512267e04b6d00ffe18fe68a4181c9a5af35f765dfbabdeedd28c8f4f7
endef
$(eval $(call Download,xr819fw))

define KernelPackage/xradio
  SUBMENU:=Wireless Drivers
  TITLE:=XRadio XR819 SDIO wireless driver (Orange Pi Zero)
  DEPENDS:=@TARGET_sunxi_cortexa7 +kmod-mac80211
  FILES:=$(PKG_BUILD_DIR)/xradio_wlan.ko
  AUTOLOAD:=$(call AutoProbe,xradio_wlan)
endef

define KernelPackage/xradio/description
  Out-of-tree mac80211 driver for the XRadio/Allwinner XR819 SDIO
  Wi-Fi chip found on the Orange Pi Zero (Allwinner H2+).
  Ported to mainline kernels by fifteenhex; STA, AP and dual mode
  supported. Includes the XR819 firmware blobs (boot/fw/sdd) in
  /lib/firmware/xr819/.

  WARNING: The XR819 chip firmware drops many frames (FCS errors);
  expect mediocre throughput and range. 802.11 b/g/n 2.4 GHz only.
endef

NOSTDINC_FLAGS:= \
	-I$(PKG_BUILD_DIR) \
	-I$(STAGING_DIR)/usr/include/mac80211-backport/uapi \
	-I$(STAGING_DIR)/usr/include/mac80211-backport \
	-I$(STAGING_DIR)/usr/include/mac80211/uapi \
	-I$(STAGING_DIR)/usr/include/mac80211 \
	-include $(STAGING_DIR)/usr/include/mac80211-backport/backport/autoconf.h \
	-include $(STAGING_DIR)/usr/include/mac80211-backport/backport/backport.h

define Build/Prepare
	$(call Build/Prepare/Default)
endef

define Build/Compile
	+$(MAKE) $(PKG_JOBS) -C "$(LINUX_DIR)" \
		$(KERNEL_MAKE_FLAGS) \
		M="$(PKG_BUILD_DIR)" \
		NOSTDINC_FLAGS="$(NOSTDINC_FLAGS)" \
		modules
endef

define KernelPackage/xradio/install
	$(INSTALL_DIR) $(1)/lib/firmware/xr819
	$(TAR) -C $(1)/lib/firmware -xjf $(DL_DIR)/xr819.tar.bz2
endef

$(eval $(call KernelPackage,xradio))
