#
# Copyright (C) 2006-2016 OpenWrt.org
# This is free software, licensed under the GNU General Public License v2.
#

include $(TOPDIR)/rules.mk

PKG_NAME:=openssl1.1
PKG_BASE:=1.1.1
PKG_BUGFIX:=w
PKG_VERSION:=$(PKG_BASE)$(PKG_BUGFIX)
PKG_RELEASE:=1
PKG_USE_MIPS16:=0

PKG_BUILD_PARALLEL:=1

PKG_SOURCE:=openssl-$(PKG_VERSION).tar.gz
PKG_SOURCE_URL:= \
	http://www.openssl.org/source/ \
	http://www.openssl.org/source/old/$(PKG_BASE)/ \
	https://openssl-library.org/source/old/$(PKG_BASE)/

PKG_HASH:=cf3098950ce4d71e6fe89f07d203d12b01be5a40c330aaf40b3bcc90e205c9b7

PKG_LICENSE:=OpenSSL
PKG_LICENSE_FILES:=LICENSE

include $(INCLUDE_DIR)/package.mk

define Package/libopenssl1.1
  SECTION:=libs
  CATEGORY:=Libraries
  SUBMENU:=SSL
  TITLE:=OpenSSL 1.1 libraries
  ABI_VERSION:=1.1
endef

define Package/libopenssl1.1/description
OpenSSL 1.1 shared libraries, installed to /usr/lib/openssl-1.1
endef

# 配置选项（保持原样，但无引擎依赖）
OPENSSL_OPTIONS:= shared no-engine

ifndef CONFIG_OPENSSL_WITH_ASM
  OPENSSL_OPTIONS += no-asm
endif

OPENSSL_TARGET:=linux-$(call qstrip,$(CONFIG_ARCH))-openwrt

define Build/Configure
	(cd $(PKG_BUILD_DIR); \
		./Configure $(OPENSSL_TARGET) \
			--prefix=/usr \
			--libdir=lib/openssl-1.1 \
			--openssldir=/etc/ssl-1.1 \
			--cross-compile-prefix="$(TARGET_CROSS)" \
			$(TARGET_CFLAGS) \
			$(TARGET_CPPFLAGS) \
			$(TARGET_LDFLAGS) \
			$(OPENSSL_OPTIONS) \
	)
endef

TARGET_CFLAGS += $(FPIC) -ffunction-sections -fdata-sections
TARGET_LDFLAGS += -Wl,--gc-sections

define Build/Compile
	+$(MAKE) $(PKG_JOBS) -C $(PKG_BUILD_DIR) \
		CC="$(TARGET_CC)" \
		all
	$(MAKE) -C $(PKG_BUILD_DIR) \
		CC="$(TARGET_CC)" \
		DESTDIR="$(PKG_INSTALL_DIR)" \
		install_sw install_ssldirs
endef

define Build/InstallDev
	$(INSTALL_DIR) $(1)/usr/include/openssl1.1
	$(CP) $(PKG_INSTALL_DIR)/usr/include/openssl $(1)/usr/include/openssl1.1/
	$(INSTALL_DIR) $(1)/usr/lib/openssl-1.1
	$(CP) $(PKG_INSTALL_DIR)/usr/lib/lib{crypto,ssl}.{a,so*} $(1)/usr/lib/openssl-1.1/
endef

define Package/libopenssl1.1/install
	$(INSTALL_DIR) $(1)/usr/lib/openssl-1.1
	$(INSTALL_DATA) $(PKG_INSTALL_DIR)/usr/lib/libcrypto.so.* $(1)/usr/lib/openssl-1.1/
	$(INSTALL_DATA) $(PKG_INSTALL_DIR)/usr/lib/libssl.so.* $(1)/usr/lib/openssl-1.1/
endef

$(eval $(call BuildPackage,libopenssl1.1))
