#
# Copyright (C) 2006-2016 OpenWrt.org
# This is free software, licensed under the GNU General Public License v2.
#

include $(TOPDIR)/rules.mk

PKG_NAME:=openssl1.1
PKG_BASE:=1.1.1
PKG_BUGFIX:=v
PKG_VERSION:=$(PKG_BASE)$(PKG_BUGFIX)
PKG_RELEASE:=1
PKG_USE_MIPS16:=0

PKG_BUILD_PARALLEL:=1

PKG_SOURCE:=openssl-$(PKG_VERSION).tar.gz
PKG_SOURCE_URL:= \
	https://www.openssl.org/source/ \
	https://www.openssl.org/source/old/$(PKG_BASE)/ \
	https://github.com/openssl/openssl/releases/download/OpenSSL_1_1_1w/ \
	https://mirrors.tuna.tsinghua.edu.cn/openssl/source/old/$(PKG_BASE)/ \
	https://ftp.openssl.org/source/old/$(PKG_BASE)/

PKG_HASH:=d6697e2871e77238460402e9362d47d18382b15ef9f246aba6c7bd780d38a6b0

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
OpenSSL 1.1 shared libraries, installed to /usr/lib
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
			--libdir=lib \
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
	# 头文件放在单独目录，避免覆盖 OpenSSL 3
	$(INSTALL_DIR) $(1)/usr/include/openssl1.1
	$(CP) $(PKG_INSTALL_DIR)/usr/include/openssl $(1)/usr/include/openssl1.1/
	# 静态库和动态库放 /usr/lib
	$(INSTALL_DIR) $(1)/usr/lib
	$(CP) $(PKG_INSTALL_DIR)/usr/lib/libcrypto.a $(1)/usr/lib/
	$(CP) $(PKG_INSTALL_DIR)/usr/lib/libssl.a $(1)/usr/lib/
	# 动态库只安装 1.1 版本
	$(CP) $(PKG_INSTALL_DIR)/usr/lib/libcrypto.so.1.1 $(1)/usr/lib/
	$(CP) $(PKG_INSTALL_DIR)/usr/lib/libssl.so.1.1 $(1)/usr/lib/
endef

define Package/libopenssl1.1/install
	$(INSTALL_DIR) $(1)/usr/lib
	# 只安装带版本号的动态库，避免覆盖 OpenSSL 3
	$(INSTALL_DATA) $(PKG_INSTALL_DIR)/usr/lib/libcrypto.so.1.1 $(1)/usr/lib/
	$(INSTALL_DATA) $(PKG_INSTALL_DIR)/usr/lib/libssl.so.1.1 $(1)/usr/lib/
endef

$(eval $(call BuildPackage,libopenssl1.1))
