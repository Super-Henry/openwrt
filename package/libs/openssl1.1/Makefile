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
	https://openssl-library.org/source/old/$(PKG_BASE)/ \
	https://www.openssl.org/source/old/$(PKG_BASE)/

PKG_HASH:=cf3098950cb4d853ad95c0841f1f9c6d3dc102dccfcacd521d93925208b76ac8

PKG_BUILD_DIR:=$(BUILD_DIR)/openssl-$(PKG_VERSION)

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
# 强制禁用 asm，避免 MIPS16 汇编不兼容 以及 交叉编译 RANLIB 重复问题
OPENSSL_OPTIONS:= shared no-engine no-tests no-async no-deprecated no-asm

# 原来的条件保留但不再使用（直接 no-asm）
# ifndef CONFIG_OPENSSL_WITH_ASM
#   OPENSSL_OPTIONS += no-asm
# endif

# ===== 正确 target（鲁班 mipsel / 哪吒 aarch64 适配）=====
ifeq ($(ARCH),mips)
  OPENSSL_TARGET:=linux-mips32
else ifeq ($(ARCH),mipsel)
  OPENSSL_TARGET:=linux-mips32
else ifeq ($(ARCH),aarch64)
  OPENSSL_TARGET:=linux-aarch64
else ifeq ($(ARCH),arm)
  OPENSSL_TARGET:=linux-armv4
else ifeq ($(ARCH),x86_64)
  OPENSSL_TARGET:=linux-x86_64
else
  OPENSSL_TARGET:=linux-generic32
endif

# ===== flags（保留你原逻辑）=====
TARGET_CFLAGS += $(FPIC) -ffunction-sections -fdata-sections
TARGET_LDFLAGS += -Wl,--gc-sections

# ===== 关键修复：彻底隔离宿主机环境，不设置 cross-compile-prefix =====
define Build/Configure
	(cd $(PKG_BUILD_DIR); \
	unset CROSS_COMPILE; \
	unset CFLAGS; \
	unset CPPFLAGS; \
	unset LDFLAGS; \
	export CC="$(TARGET_CC)"; \
	export AR="$(TARGET_AR)"; \
	export RANLIB="$(TARGET_RANLIB)"; \
	export CFLAGS="$(TARGET_CFLAGS) -I$(STAGING_DIR)/usr/include -I$(TOOLCHAIN_DIR)/include"; \
	export CPPFLAGS="-I$(STAGING_DIR)/usr/include -I$(TOOLCHAIN_DIR)/include"; \
	export LDFLAGS="-L$(STAGING_DIR)/usr/lib -L$(TOOLCHAIN_DIR)/lib"; \
	./Configure $(OPENSSL_TARGET) \
		--prefix=/usr \
		--openssldir=/etc/ssl-1.1 \
		--libdir=lib \
		$(OPENSSL_OPTIONS) \
	)
endef

# ===== Compile =====
define Build/Compile
	$(MAKE) -C $(PKG_BUILD_DIR) \
		CC="$(TARGET_CC)" \
		AR="$(TARGET_AR)" \
		RANLIB="$(TARGET_RANLIB)" \
		CFLAGS="$(TARGET_CFLAGS) -I$(STAGING_DIR)/usr/include -I$(TOOLCHAIN_DIR)/include" \
		CPPFLAGS="-I$(STAGING_DIR)/usr/include -I$(TOOLCHAIN_DIR)/include" \
		LDFLAGS="-L$(STAGING_DIR)/usr/lib -L$(TOOLCHAIN_DIR)/lib" \
		$(PKG_JOBS)

	$(MAKE) -C $(PKG_BUILD_DIR) \
		DESTDIR="$(PKG_INSTALL_DIR)" \
		install_sw install_ssldirs
endef

# ===== 开发文件（避免污染 OpenSSL 3）=====
define Build/InstallDev
	$(INSTALL_DIR) $(1)/usr/include/openssl1.1
	$(CP) $(PKG_INSTALL_DIR)/usr/include/openssl $(1)/usr/include/openssl1.1/

	$(INSTALL_DIR) $(1)/usr/lib
	$(CP) $(PKG_INSTALL_DIR)/usr/lib/libcrypto.a $(1)/usr/lib/
	$(CP) $(PKG_INSTALL_DIR)/usr/lib/libssl.a $(1)/usr/lib/

	$(CP) $(PKG_INSTALL_DIR)/usr/lib/libcrypto.so.1.1 $(1)/usr/lib/
	$(CP) $(PKG_INSTALL_DIR)/usr/lib/libssl.so.1.1 $(1)/usr/lib/
endef

# ===== 运行时安装 =====
define Package/libopenssl1.1/install
	$(INSTALL_DIR) $(1)/usr/lib

	$(INSTALL_DATA) $(PKG_INSTALL_DIR)/usr/lib/libcrypto.so.1.1 $(1)/usr/lib/
	$(INSTALL_DATA) $(PKG_INSTALL_DIR)/usr/lib/libssl.so.1.1 $(1)/usr/lib/

	# 防止污染 OpenSSL 3
	rm -f $(1)/usr/lib/libcrypto.so
	rm -f $(1)/usr/lib/libssl.so
endef

$(eval $(call BuildPackage,libopenssl1.1))
