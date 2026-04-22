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
PKG_SOURCE_URL:= https://openssl-library.org/source/old/$(PKG_BASE)/ 

PKG_HASH:=cf3098950cb4d853ad95c0841f1f9c6d3dc102dccfcacd521d93925208b76ac8

PKG_LICENSE:=OpenSSL
PKG_LICENSE_FILES:=LICENSE

include $(INCLUDE_DIR)/package.mk

define Package/libopenssl1.1
  SECTION:=libs
  CATEGORY:=Libraries
  SUBMENU:=SSL
  TITLE:=OpenSSL 1.1 libraries (parallel install)
  ABI_VERSION:=1.1
  PROVIDES:=libcrypto1.1 libssl1.1
endef

define Package/libopenssl1.1/description
OpenSSL 1.1 shared libraries for legacy apps (coexists with OpenSSL 3)
endef

# ===== 构建选项 =====
OPENSSL_OPTIONS:= shared no-engine no-tests

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

# ===== 开发文件（避免污染 OpenSSL 3）=====
define Build/InstallDev
	# 头文件隔离
	$(INSTALL_DIR) $(1)/usr/include/openssl1.1
	$(CP) $(PKG_INSTALL_DIR)/usr/include/openssl $(1)/usr/include/openssl1.1/

	# 静态库（可选）
	$(INSTALL_DIR) $(1)/usr/lib
	$(CP) $(PKG_INSTALL_DIR)/usr/lib/libcrypto.a $(1)/usr/lib/
	$(CP) $(PKG_INSTALL_DIR)/usr/lib/libssl.a $(1)/usr/lib/

	# 只放带版本号的动态库
	$(CP) $(PKG_INSTALL_DIR)/usr/lib/libcrypto.so.1.1 $(1)/usr/lib/
	$(CP) $(PKG_INSTALL_DIR)/usr/lib/libssl.so.1.1 $(1)/usr/lib/
endef

# ===== 运行时安装（核心）=====
define Package/libopenssl1.1/install
	$(INSTALL_DIR) $(1)/usr/lib

	# ✅ 只安装 1.1 版本（不会覆盖 OpenSSL 3）
	$(INSTALL_DATA) $(PKG_INSTALL_DIR)/usr/lib/libcrypto.so.1.1 $(1)/usr/lib/
	$(INSTALL_DATA) $(PKG_INSTALL_DIR)/usr/lib/libssl.so.1.1 $(1)/usr/lib/

	# 🔥 防止覆盖 OpenSSL 3（极其关键）
	rm -f $(1)/usr/lib/libcrypto.so
	rm -f $(1)/usr/lib/libssl.so
endef

$(eval $(call BuildPackage,libopenssl1.1))
