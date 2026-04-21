#
# Copyright (C) 2006-2016 OpenWrt.org
#
# This is free software, licensed under the GNU General Public License v2.
# See /LICENSE for more information.
#

include $(TOPDIR)/rules.mk

PKG_NAME:=openssl1.1
PKG_BASE:=1.1.1
PKG_BUGFIX:=w
PKG_VERSION:=$(PKG_BASE)$(PKG_BUGFIX)
PKG_RELEASE:=1
PKG_USE_MIPS16:=0

PKG_BUILD_PARALLEL:=1

# 修正：源码包名应为 openssl-$(PKG_VERSION).tar.gz，而不是 openssl1.1-...
PKG_SOURCE:=openssl-$(PKG_VERSION).tar.gz
PKG_SOURCE_URL:= \
	http://www.openssl.org/source/ \
	http://www.openssl.org/source/old/$(PKG_BASE)/ \
	http://ftp.fi.muni.cz/pub/openssl/source/ \
	http://ftp.fi.muni.cz/pub/openssl/source/old/$(PKG_BASE)/ \
	ftp://ftp.pca.dfn.de/pub/tools/net/openssl/source/ \
	ftp://ftp.pca.dfn.de/pub/tools/net/openssl/source/old/$(PKG_BASE)/

PKG_HASH:=cf3098950cb4d853ad95c0841f1f9c6d3dc102dccfcacd521d93925208b76ac8

PKG_LICENSE:=OpenSSL
PKG_LICENSE_FILES:=LICENSE
PKG_MAINTAINER:=Eneas U de Queiroz <cotequeiroz@gmail.com>
PKG_CPE_ID:=cpe:/a:openssl:openssl
PKG_CONFIG_DEPENDS:= \
	CONFIG_OPENSSL_ENGINE \
	CONFIG_OPENSSL_ENGINE_BUILTIN \
	CONFIG_OPENSSL_ENGINE_BUILTIN_AFALG \
	CONFIG_OPENSSL_ENGINE_BUILTIN_DEVCRYPTO \
	CONFIG_OPENSSL_ENGINE_BUILTIN_PADLOCK \
	CONFIG_OPENSSL_NO_DEPRECATED \
	CONFIG_OPENSSL_OPTIMIZE_SPEED \
	CONFIG_OPENSSL_PREFER_CHACHA_OVER_GCM \
	CONFIG_OPENSSL_WITH_ARIA \
	CONFIG_OPENSSL_WITH_ASM \
	CONFIG_OPENSSL_WITH_ASYNC \
	CONFIG_OPENSSL_WITH_BLAKE2 \
	CONFIG_OPENSSL_WITH_CAMELLIA \
	CONFIG_OPENSSL_WITH_CHACHA_POLY1305 \
	CONFIG_OPENSSL_WITH_CMS \
	CONFIG_OPENSSL_WITH_COMPRESSION \
	CONFIG_OPENSSL_WITH_DTLS \
	CONFIG_OPENSSL_WITH_EC2M \
	CONFIG_OPENSSL_WITH_ERROR_MESSAGES \
	CONFIG_OPENSSL_WITH_IDEA \
	CONFIG_OPENSSL_WITH_MDC2 \
	CONFIG_OPENSSL_WITH_NPN \
	CONFIG_OPENSSL_WITH_PSK \
	CONFIG_OPENSSL_WITH_RFC3779 \
	CONFIG_OPENSSL_WITH_SEED \
	CONFIG_OPENSSL_WITH_SM234 \
	CONFIG_OPENSSL_WITH_SRP \
	CONFIG_OPENSSL_WITH_SSE2 \
	CONFIG_OPENSSL_WITH_TLS13 \
	CONFIG_OPENSSL_WITH_WHIRLPOOL

include $(INCLUDE_DIR)/package.mk
# 删除引擎支持（避免解析错误）
# include engine.mk

ifneq ($(CONFIG_CCACHE),)
HOSTCC=$(HOSTCC_NOCACHE)
HOSTCXX=$(HOSTCXX_NOCACHE)
endif

define Package/openssl/Default
  TITLE:=Open source SSL toolkit
  URL:=http://www.openssl.org/
  SECTION:=libs
  CATEGORY:=Libraries
endef

define Package/libopenssl1.1/config
source "$(SOURCE)/Config.in"
endef

define Package/openssl/Default/description
The OpenSSL Project is a collaborative effort to develop a robust,
commercial-grade, full-featured, and Open Source toolkit implementing the
Transport Layer Security (TLS) protocol as well as a full-strength
general-purpose cryptography library.
endef

define Package/libopenssl1.1
$(call Package/openssl/Default)
  SUBMENU:=SSL
  # 移除引擎依赖（这些包不存在，且已禁用引擎）
  DEPENDS:=+OPENSSL_WITH_COMPRESSION:zlib
  TITLE+= (libraries)
  ABI_VERSION:=1.1
  MENU:=1
endef

define Package/libopenssl1.1/description
$(call Package/openssl/Default/description)
This package contains the OpenSSL 1.1 shared libraries, installed to /usr/lib/openssl-1.1/.
endef

define Package/openssl1.1-util
  $(call Package/openssl/Default)
  SECTION:=utils
  CATEGORY:=Utilities
  DEPENDS:=+libopenssl1.1 +libopenssl1.1-conf
  TITLE+= (utility)
endef

define Package/openssl1.1-util/description
$(call Package/openssl/Default/description)
This package contains the OpenSSL 1.1 command-line utility (installed as /usr/bin/openssl1.1).
endef

define Package/libopenssl1.1-conf
  $(call Package/openssl/Default)
  SUBMENU:=SSL
  TITLE:=/etc/ssl-1.1/openssl.cnf config file
  DEPENDS:=libopenssl1.1
endef

define Package/libopenssl1.1-conf/conffiles
/etc/ssl-1.1/openssl.cnf
endef

define Package/libopenssl1.1-conf/description
$(call Package/openssl/Default/description)
This package installs the OpenSSL 1.1 configuration file /etc/ssl-1.1/openssl.cnf.
endef

OPENSSL_OPTIONS:= shared

ifndef CONFIG_OPENSSL_WITH_BLAKE2
  OPENSSL_OPTIONS += no-blake2
endif

ifndef CONFIG_OPENSSL_WITH_CHACHA_POLY1305
  OPENSSL_OPTIONS += no-chacha no-poly1305
else
  ifdef CONFIG_OPENSSL_PREFER_CHACHA_OVER_GCM
    OPENSSL_OPTIONS += -DOPENSSL_PREFER_CHACHA_OVER_GCM
  endif
endif

ifndef CONFIG_OPENSSL_WITH_ASYNC
  OPENSSL_OPTIONS += no-async
endif

ifndef CONFIG_OPENSSL_WITH_EC2M
  OPENSSL_OPTIONS += no-ec2m
endif

ifndef CONFIG_OPENSSL_WITH_ERROR_MESSAGES
  OPENSSL_OPTIONS += no-err
endif

ifndef CONFIG_OPENSSL_WITH_TLS13
  OPENSSL_OPTIONS += no-tls1_3
endif

ifndef CONFIG_OPENSSL_WITH_ARIA
  OPENSSL_OPTIONS += no-aria
endif

ifndef CONFIG_OPENSSL_WITH_SM234
  OPENSSL_OPTIONS += no-sm2 no-sm3 no-sm4
endif

ifndef CONFIG_OPENSSL_WITH_CAMELLIA
  OPENSSL_OPTIONS += no-camellia
endif

ifndef CONFIG_OPENSSL_WITH_IDEA
  OPENSSL_OPTIONS += no-idea
endif

ifndef CONFIG_OPENSSL_WITH_SEED
  OPENSSL_OPTIONS += no-seed
endif

ifndef CONFIG_OPENSSL_WITH_MDC2
  OPENSSL_OPTIONS += no-mdc2
endif

ifndef CONFIG_OPENSSL_WITH_WHIRLPOOL
  OPENSSL_OPTIONS += no-whirlpool
endif

ifndef CONFIG_OPENSSL_WITH_CMS
  OPENSSL_OPTIONS += no-cms
endif

ifndef CONFIG_OPENSSL_WITH_RFC3779
  OPENSSL_OPTIONS += no-rfc3779
endif

ifdef CONFIG_OPENSSL_NO_DEPRECATED
  OPENSSL_OPTIONS += no-deprecated
endif

ifeq ($(CONFIG_OPENSSL_OPTIMIZE_SPEED),y)
  TARGET_CFLAGS := $(filter-out -O%,$(TARGET_CFLAGS)) -O3
else
  OPENSSL_OPTIONS += -DOPENSSL_SMALL_FOOTPRINT
endif

# 删除引擎相关配置（因为已禁用引擎）
# ifdef CONFIG_OPENSSL_ENGINE ...
# 整个块移除

# 但保留与引擎无关的选项
ifndef CONFIG_OPENSSL_WITH_DTLS
  OPENSSL_OPTIONS += no-dtls
endif

ifdef CONFIG_OPENSSL_WITH_COMPRESSION
  OPENSSL_OPTIONS += zlib-dynamic
else
  OPENSSL_OPTIONS += no-comp
endif

ifndef CONFIG_OPENSSL_WITH_NPN
  OPENSSL_OPTIONS += no-nextprotoneg
endif

ifndef CONFIG_OPENSSL_WITH_PSK
  OPENSSL_OPTIONS += no-psk
endif

ifndef CONFIG_OPENSSL_WITH_SRP
  OPENSSL_OPTIONS += no-srp
endif

ifndef CONFIG_OPENSSL_WITH_ASM
  OPENSSL_OPTIONS += no-asm
endif

ifdef CONFIG_i386
  ifndef CONFIG_OPENSSL_WITH_SSE2
    OPENSSL_OPTIONS += no-sse2
  endif
endif

OPENSSL_TARGET:=linux-$(call qstrip,$(CONFIG_ARCH))-openwrt

STAMP_CONFIGURED := $(STAMP_CONFIGURED)_$(shell echo $(OPENSSL_OPTIONS) | $(MKHASH) md5)

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
			$(OPENSSL_OPTIONS) && \
		{ [ -f $(STAMP_CONFIGURED) ] || make clean; } \
	)
endef

TARGET_CFLAGS += $(FPIC) -ffunction-sections -fdata-sections
TARGET_LDFLAGS += -Wl,--gc-sections

define Build/Compile
	+$(MAKE) $(PKG_JOBS) -C $(PKG_BUILD_DIR) \
		CC="$(TARGET_CC)" \
		SOURCE_DATE_EPOCH=$(SOURCE_DATE_EPOCH) \
		OPENWRT_OPTIMIZATION_FLAGS="$(TARGET_CFLAGS)" \
		$(OPENSSL_MAKEFLAGS) \
		all
	$(MAKE) -C $(PKG_BUILD_DIR) \
		CC="$(TARGET_CC)" \
		DESTDIR="$(PKG_INSTALL_DIR)" \
		$(OPENSSL_MAKEFLAGS) \
		install_sw install_ssldirs
endef

define Build/InstallDev
	$(INSTALL_DIR) $(1)/usr/include/openssl1.1
	$(CP) $(PKG_INSTALL_DIR)/usr/include/openssl $(1)/usr/include/openssl1.1/
	$(INSTALL_DIR) $(1)/usr/lib/openssl-1.1
	$(CP) $(PKG_INSTALL_DIR)/usr/lib/lib{crypto,ssl}.{a,so*} $(1)/usr/lib/openssl-1.1/
	# 不安装 pkgconfig 文件，避免与 OpenSSL 3 冲突
endef

define Package/libopenssl1.1/install
	$(INSTALL_DIR) $(1)/etc/ssl-1.1/certs
	$(INSTALL_DIR) $(1)/etc/ssl-1.1/private
	chmod 0700 $(1)/etc/ssl-1.1/private
	$(INSTALL_DIR) $(1)/usr/lib/openssl-1.1
	$(INSTALL_DATA) $(PKG_INSTALL_DIR)/usr/lib/libcrypto.so.* $(1)/usr/lib/openssl-1.1/
	$(INSTALL_DATA) $(PKG_INSTALL_DIR)/usr/lib/libssl.so.* $(1)/usr/lib/openssl-1.1/
endef

define Package/libopenssl1.1-conf/install
	$(INSTALL_DIR) $(1)/etc/ssl-1.1
	$(CP) $(PKG_INSTALL_DIR)/etc/ssl/openssl.cnf $(1)/etc/ssl-1.1/
endef

define Package/openssl1.1-util/install
	$(INSTALL_DIR) $(1)/usr/bin
	$(INSTALL_BIN) $(PKG_INSTALL_DIR)/usr/bin/openssl $(1)/usr/bin/openssl1.1
endef

$(eval $(call BuildPackage,libopenssl1.1))
$(eval $(call BuildPackage,libopenssl1.1-conf))
$(eval $(call BuildPackage,openssl1.1-util))
