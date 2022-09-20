#
# Copyright (C) 2015-2022 Alex Kirhenshtein <alk@netxms.org>
#
# This is free software, licensed under the GNU General Public License v2.
# See /LICENSE for more information.
#

include $(TOPDIR)/rules.mk

NETXMS_RELEASE:=4.2
NETXMS_BUILD:=278

PKG_NAME:=netxms
PKG_LICENSE=GPL-2.0+
PKG_VERSION:=$(NETXMS_RELEASE).$(NETXMS_BUILD)
PKG_RELEASE:=1

PKG_SOURCE:=netxms-$(PKG_VERSION).tar.gz
PKG_SOURCE_URL:=https://www.netxms.org/download/releases/$(NETXMS_RELEASE)/
PKG_HASH:=526333512a96c31473ca218326a1c64068025ff9f16b3ec95b0faf5a0ac1395b

PKG_BUILD_DIR:=$(BUILD_DIR)/$(PKG_NAME)-$(BUILD_VARIANT)/$(PKG_NAME)-$(PKG_VERSION)
PKG_INSTALL:=1
PKG_BUILD_PARALLEL:=1

include $(INCLUDE_DIR)/package.mk

define Package/netxms/Default
	SUBMENU:=NetXMS
	SECTION:=admin
	CATEGORY:=Administration
	TITLE:=NetXMS
	URL:=http://www.netxms.org
	MAINTAINER:=Alex Kirhenshtein <alk@netxms.org>
endef

define Package/netxms/Default/description
	Meta-package for NetXMS agent
endef

define Package/netxms-base
$(call Package/netxms/Default)
	SUBMENU:=
	SECTION:=libs
	CATEGORY:=Libraries
	VARIANT:=$(1)
	TITLE+= common libraries$(2)
	DEPENDS:=+libatomic +libexpat +libpthread +libstdcpp +zlib +libpcre +libpcre32 $(3)
endef

define Package/netxms-base/description
$(call Package/netxms/Default/description)
	Common NetXMS libraries and tools
endef

Package/netxms-base-ssl=$(call Package/netxms-base,ssl,,+libopenssl)
Package/netxms-base-nossl=$(call Package/netxms-base,nossl, (no SSL))

define Package/netxms-agent
$(call Package/netxms/Default)
	VARIANT:=$(1)
	TITLE+= agent$(2)
	DEPENDS:=+netxms-base-$(1) +libsqlite3
endef

define Package/netxms-agent/description
$(call Package/netxms/Default/description)
	NetXMS agent
endef

Package/netxms-agent-ssl=$(call Package/netxms-agent,ssl)
Package/netxms-agent-nossl=$(call Package/netxms-agent,nossl, (no SSL))

ifeq ($(BUILD_VARIANT),nossl)
	CONFIG_NXAGENTD_NOSSL:=y
endif

define Build/Configure
	$(call Build/Configure/Default, \
		--with-openwrt \
		--with-agent \
		--disable-64bit \
		--disable-iconv \
		--without-curl \
		--disable-ssh \
		--disable-mqtt \
		--disable-null-free-check \
		--with-pcre="$(STAGING_DIR)/usr/" \
		$(if $(CONFIG_NXAGENTD_NOSSL),--disable-encryption,) \
	)
endef

define Package/netxms-base-$(BUILD_VARIANT)/install
	$(INSTALL_DIR) $(1)/usr/bin
	$(INSTALL_BIN) $(PKG_INSTALL_DIR)/usr/bin/nxencpasswd $(1)/usr/bin/
	$(INSTALL_DIR) $(1)/usr/lib
	$(CP) $(PKG_INSTALL_DIR)/usr/lib/libnetxms.so* $(1)/usr/lib/
	$(CP) $(PKG_INSTALL_DIR)/usr/lib/libnxjansson.so* $(1)/usr/lib/
endef

define Package/netxms-agent-$(BUILD_VARIANT)/install
	$(INSTALL_DIR) $(1)/usr/bin
	$(INSTALL_BIN) $(PKG_INSTALL_DIR)/usr/bin/nxagentd $(1)/usr/bin/
	$(INSTALL_BIN) $(PKG_INSTALL_DIR)/usr/bin/nxappget $(1)/usr/bin/
	$(INSTALL_BIN) $(PKG_INSTALL_DIR)/usr/bin/nxapush $(1)/usr/bin/
	$(INSTALL_BIN) $(PKG_INSTALL_DIR)/usr/bin/nxaevent $(1)/usr/bin/
	$(INSTALL_BIN) $(PKG_INSTALL_DIR)/usr/bin/nxtftp $(1)/usr/bin/
	$(INSTALL_DIR) $(1)/usr/lib
	$(CP) $(PKG_INSTALL_DIR)/usr/lib/libnxagent.so* $(1)/usr/lib/
	$(CP) $(PKG_INSTALL_DIR)/usr/lib/libappagent.so* $(1)/usr/lib/
	$(CP) $(PKG_INSTALL_DIR)/usr/lib/libnxdb.so* $(1)/usr/lib/
	$(CP) $(PKG_INSTALL_DIR)/usr/lib/libnxlp.so* $(1)/usr/lib/
	$(CP) $(PKG_INSTALL_DIR)/usr/lib/libnxsnmp.so* $(1)/usr/lib/
	$(INSTALL_DIR) $(1)/usr/lib/netxms
	$(CP) $(PKG_INSTALL_DIR)/usr/lib/netxms/*.nsm $(1)/usr/lib/netxms/
	$(INSTALL_DIR) $(1)/usr/lib/netxms/dbdrv
	$(CP) $(PKG_INSTALL_DIR)/usr/lib/netxms/dbdrv/sqlite.ddr $(1)/usr/lib/netxms/dbdrv/
	$(INSTALL_DIR) $(1)/etc
	$(INSTALL_CONF) ./files/nxagentd.conf $(1)/etc/
	$(INSTALL_DIR) $(1)/lib/upgrade/keep.d
	$(INSTALL_CONF) ./files/nxagentd.keep $(1)/lib/upgrade/keep.d/nxagentd
	$(INSTALL_DIR) $(1)/etc/init.d
	$(INSTALL_BIN) ./files/nxagentd.init $(1)/etc/init.d/nxagentd
endef

define Package/netxms-agent-$(BUILD_VARIANT)/conffiles
/etc/nxagentd.conf
endef

$(eval $(call BuildPackage,netxms-base-ssl))
$(eval $(call BuildPackage,netxms-base-nossl))
$(eval $(call BuildPackage,netxms-agent-ssl))
$(eval $(call BuildPackage,netxms-agent-nossl))
