#
# Copyright (C) 2015 Alex Kirhenshtein <alk@netxms.org>
#
# This is free software, licensed under the GNU General Public License v2.
# See /LICENSE for more information.
#

include $(TOPDIR)/rules.mk

PKG_NAME:=netxms-agent
PKG_LICENSE=GPL-2.0+
PKG_VERSION:=2.0.2
PKG_RELEASE:=1

PKG_SOURCE_URL:=http://www.netxms.org/download/
PKG_SOURCE:=netxms-$(PKG_VERSION).tar.gz
PKG_MD5SUM:=a89376cbe282800503e602350bd9f4ef

PKG_BUILD_DIR:=$(BUILD_DIR)/$(PKG_NAME)-$(BUILD_VARIANT)/netxms-$(PKG_VERSION)
PKG_INSTALL:=1
PKG_BUILD_PARALLEL:=1

PKG_FIXUP:=autoreconf

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
	DEPENDS:=+libexpat +libpthread +libstdcpp +zlib $(3)
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
		--with-agent \
		--with-client \
		--disable-64bit \
		--disable-iconv \
		--with-internal-libtre \
		$(if $(CONFIG_NXAGENTD_NOSSL),--disable-encryption,) \
	)
endef

define Package/netxms-base-$(BUILD_VARIANT)/install
	$(INSTALL_DIR) $(1)/usr/bin
	$(INSTALL_BIN) $(PKG_INSTALL_DIR)/usr/bin/nxencpasswd $(1)/usr/bin/
	$(INSTALL_DIR) $(1)/usr/lib
	$(CP) $(PKG_INSTALL_DIR)/usr/lib/libnetxms.so* $(1)/usr/lib/
	$(CP) $(PKG_INSTALL_DIR)/usr/lib/libnxclient.so* $(1)/usr/lib/
	$(CP) $(PKG_INSTALL_DIR)/usr/lib/libnxdb.so* $(1)/usr/lib/
	$(CP) $(PKG_INSTALL_DIR)/usr/lib/libnxsnmp.so* $(1)/usr/lib/
	$(CP) $(PKG_INSTALL_DIR)/usr/lib/libnxtre.so* $(1)/usr/lib/
endef

define Package/netxms-agent-$(BUILD_VARIANT)/install
	$(INSTALL_DIR) $(1)/usr/bin
	$(INSTALL_BIN) $(PKG_INSTALL_DIR)/usr/bin/nxagentd $(1)/usr/bin/
	$(INSTALL_BIN) $(PKG_INSTALL_DIR)/usr/bin/nxappget $(1)/usr/bin/
	$(INSTALL_BIN) $(PKG_INSTALL_DIR)/usr/bin/nxapush $(1)/usr/bin/
	$(INSTALL_BIN) $(PKG_INSTALL_DIR)/usr/bin/nxevent $(1)/usr/bin/
	$(INSTALL_BIN) $(PKG_INSTALL_DIR)/usr/bin/nxpush $(1)/usr/bin/
	$(INSTALL_DIR) $(1)/usr/lib
	$(CP) $(PKG_INSTALL_DIR)/usr/lib/libnxagent.so* $(1)/usr/lib/
	$(CP) $(PKG_INSTALL_DIR)/usr/lib/libappagent.so* $(1)/usr/lib/
	$(CP) $(PKG_INSTALL_DIR)/usr/lib/libnxlp.so* $(1)/usr/lib/
	$(INSTALL_DIR) $(1)/usr/lib/netxms
	$(CP) $(PKG_INSTALL_DIR)/usr/lib/netxms/*.nsm $(1)/usr/lib/netxms/
	$(INSTALL_DIR) $(1)/usr/lib/netxms/dbdrv
	$(CP) $(PKG_INSTALL_DIR)/usr/lib/netxms/dbdrv/sqlite.ddr $(1)/usr/lib/netxms/dbdrv/
endef

$(eval $(call BuildPackage,netxms-base-ssl))
$(eval $(call BuildPackage,netxms-base-nossl))
$(eval $(call BuildPackage,netxms-agent-ssl))
$(eval $(call BuildPackage,netxms-agent-nossl))
