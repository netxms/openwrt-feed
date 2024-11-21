#
# Copyright (C) 2015-2022 Alex Kirhenshtein <alk@netxms.org>
#
# This is free software, licensed under the GNU General Public License v2.
# See /LICENSE for more information.
#

include $(TOPDIR)/rules.mk

NETXMS_RELEASE:=5.1
NETXMS_BUILD:=1.8

PKG_NAME:=netxms
PKG_LICENSE=GPL-2.0+
PKG_VERSION:=$(NETXMS_RELEASE).$(NETXMS_BUILD)
PKG_RELEASE:=1

PKG_SOURCE:=netxms-$(PKG_VERSION).tar.gz
PKG_SOURCE_URL:=https://www.netxms.org/download/releases/$(NETXMS_RELEASE)/
PKG_HASH:=9e428f25dad4519fec7b96b16915494867e3eae6f320fb3f69e892404993f068

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
	TITLE+= common libraries
	DEPENDS:=+libatomic +libexpat +libpthread +libstdcpp +zlib +libpcre +libpcre32 +libopenssl
endef

define Package/netxms-base/description
$(call Package/netxms/Default/description)
	Common NetXMS libraries and tools
endef

define Package/netxms-agent
$(call Package/netxms/Default)
	TITLE+= agent
	DEPENDS:=+netxms-base +libsqlite3 +libmosquitto +libssh
endef

define Package/netxms-agent/description
$(call Package/netxms/Default/description)
	NetXMS agent
endef

define Build/Configure
	$(call Build/Configure/Default, \
		--with-openwrt \
		--with-agent \
		--disable-iconv \
		--without-curl \
		--disable-null-free-check \
		--with-pcre="$(STAGING_DIR)/usr/" \
	)
endef

define Package/netxms-base/install
	$(INSTALL_DIR) $(1)/usr/bin
	$(INSTALL_BIN) $(PKG_INSTALL_DIR)/usr/bin/nxencpasswd $(1)/usr/bin/
	$(INSTALL_DIR) $(1)/usr/lib
	$(CP) $(PKG_INSTALL_DIR)/usr/lib/libnetxms.so* $(1)/usr/lib/
	$(CP) $(PKG_INSTALL_DIR)/usr/lib/libnxjansson.so* $(1)/usr/lib/
endef

define Package/netxms-agent/install
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

define Package/netxms-agent/conffiles
/etc/nxagentd.conf
endef

$(eval $(call BuildPackage,netxms-base))
$(eval $(call BuildPackage,netxms-agent))
