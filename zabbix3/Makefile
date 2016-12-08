#
# Copyright (C) 2006-2014 OpenWrt.org
#
# This is free software, licensed under the GNU General Public License v2.
# See /LICENSE for more information.
#

include $(TOPDIR)/rules.mk

PKG_NAME:=zabbix3
PKG_VERSION:=3.2.1
PKG_RELEASE:=2

PKG_SOURCE:=zabbix-$(PKG_VERSION).tar.gz
PKG_SOURCE_URL:=@SF/zabbix
PKG_MD5SUM:=4f363b923ef2b5eefddee8dfc5f51e2b
PKG_BUILD_DIR:=$(BUILD_DIR)/zabbix3-$(PKG_VERSION)
PKG_BUILD_DEPENDS+=sqlite3 libcurl libxml2 net-snmp

PKG_LICENSE:=GPL-2.0
PKG_LICENSE_FILES:=COPYING

PKG_INSTALL:=1

PKG_FIXUP:=autoreconf

include $(INCLUDE_DIR)/package.mk
include $(INCLUDE_DIR)/nls.mk

CONFIGURE_ARGS+= \
	--disable-java \
	$(call autoconf_bool,CONFIG_IPV6,ipv6) 

ifdef ZABBIX3_OPENSSL
 CONFIGURE_ARGS += --with-openssl="$(STAGING_DIR)/usr" 
 ZDEPENDS += +libopenssl
endif

ifdef ZABBIX3_GNUTLS
  CONFIGURE_ARGS += --with-gnutls="$(STAGING_DIR)/usr"
  ZDEPENDS += +libgnutls
endif

ifdef CONFIG_PACKAGE_zabbix3-server
 CONFIGURE_ARGS += --enable-server --with-sqlite3="$(STAGING_DIR)/usr"
endif

ifdef CONFIG_PACKAGE_zabbix3-proxy
 CONFIGURE_ARGS += --enable-proxy --with-sqlite3="$(STAGING_DIR)/usr"
endif

ifdef CONFIG_PACKAGE_zabbix3-agentd
 CONFIGURE_ARGS += --enable-agent
endif

ifdef CONFIG_ZABBIX3_CURL
 CONFIGURE_ARGS += --with-libcurl="$(STAGING_DIR)/usr/bin/curl-config"
 SDEPENDS += +libcurl
endif

ifdef CONFIG_ZABBIX3_XML2
 CONFIGURE_ARGS += --with-libxml2="$(STAGING_DIR)/usr/" 
 ZDEPENDS += +libxml2
endif

ifdef CONFIG_ZABBIX3_SNMP
 CONFIGURE_ARGS += --with-net-snmp="$(STAGING_DIR)/usr" LDFLAGS="-lnetsnmp"
 ZDEPENDS += +libnetsnmp
endif

define Package/zabbix3/Default
  SECTION:=admin
  CATEGORY:=Administration
  TITLE:=Zabbix
  URL:=http://www.zabbix.com/
  SUBMENU:=zabbix3
  MAINTAINER:= Lukas Macura <lukas.macura@cesnet.cz>
  DEPENDS:=$(ICONV_DEPENDS) $(ZDEPENDS)
  USERID:=zabbix=53:zabbix=53
endef

define Package/zabbix3-core
  $(call Package/zabbix3/Default)
  TITLE+= core
endef

define Package/zabbix3-agentd
  $(call Package/zabbix3/Default)
  TITLE+= agentd
  DEPENDS += +zabbix3-core 
endef

define Package/zabbix3-sender
  $(call Package/zabbix3/Default)
  TITLE+= sender
  DEPENDS += +zabbix3-core 
endef

define Package/zabbix3-get
  $(call Package/zabbix3/Default)
  TITLE+= get
  DEPENDS += +zabbix3-core
endef

define Package/zabbix3-server
  $(call Package/zabbix3/Default)
  TITLE+= server
  DEPENDS += +zabbix3-core $(SDEPENDS) +libsqlite3
endef

define Package/zabbix3-proxy
  $(call Package/zabbix3/Default)
  TITLE+= proxy
  DEPENDS += +zabbix3-core $(SDEPENDS) +libsqlite3
endef

MAKE_FLAGS += ARCH="linux"

define Package/zabbix3-core/config
source "$(SOURCE)/Config.in"
endef

define Package/zabbix3/install/sbin
	$(INSTALL_DIR) \
		$(1)/usr/sbin

	$(INSTALL_BIN) \
		$(PKG_INSTALL_DIR)/usr/sbin/zabbix_$(2) \
		$(1)/usr/sbin/
endef

define Package/zabbix3/install/bin
	$(INSTALL_DIR) \
		$(1)/usr/bin

	$(INSTALL_BIN) \
		$(PKG_INSTALL_DIR)/usr/bin/zabbix_$(2) \
		$(1)/usr/bin/
endef

define Package/zabbix3/install/etc
	$(INSTALL_DIR) \
		$(1)/etc/config

	$(INSTALL_DIR) \
		$(1)/etc/zabbix_$(2).conf.d

	$(INSTALL_CONF) \
		$(PKG_INSTALL_DIR)/etc/zabbix_$(2).conf \
		$(1)/etc/

	@echo Include=/etc/zabbix_$(2).conf.d/ >$(1)/etc/zabbix_$(2).conf
	@echo Include=/var/run/zabbix_$(2).conf.d/ >>$(1)/etc/zabbix_$(2).conf
	@echo PidFile=/var/run/zabbix_$(2).pid >>$(1)/etc/zabbix_$(2).conf
	@if [ "$(2)" = "proxy" ] || [ "$(2)" = "server" ]; then echo FpingLocation=/usr/bin/fping; echo Fping6Location=/usr/bin/fping6; fi >>$(1)/etc/zabbix_$(2).conf

	$(INSTALL_CONF) \
		./files/zabbix_$(2).config \
		$(1)/etc/config/zabbix_$(2)

endef

define Package/zabbix3/install/init.d
	$(INSTALL_DIR) \
		$(1)/etc/init.d

	$(INSTALL_BIN) \
		./files/zabbix_$(2).init \
		$(1)/etc/init.d/zabbix_$(2)
endef

define Package/zabbix3/install/zabbix.conf.d
	$(INSTALL_DIR) \
		$(1)/etc/zabbix_agentd.conf.d

	$(INSTALL_BIN) \
		./files/$(2) \
		$(1)/etc/zabbix_agentd.conf.d/$(2)
endef

define Package/zabbix3-agentd/conffiles
/etc/zabbix_agentd.conf
/etc/config/zabbix_agentd
endef
define Package/zabbix3-server/conffiles
/etc/zabbix_server.conf
/etc/config/zabbix_server
endef
define Package/zabbix3-proxy/conffiles
/etc/zabbix_proxy.conf
/etc/config/zabbix_proxy
endef

define Build/Prepare
	rm -rf $(PKG_BUILD_DIR)/
	mkdir -p $(PKG_BUILD_DIR)/
	$(TAR) -xzf $(DL_DIR)/$(PKG_SOURCE) -C $(PKG_BUILD_DIR) --strip 1
	$(Build/Patch)
endef

define Build/Compile
	$(call Build/Compile/Default)
endef

define Package/zabbix3-agentd/install
	$(INSTALL_DIR) $(1)/usr/bin/
	$(call Package/zabbix3/install/sbin,$(1),agentd)
	$(call Package/zabbix3/install/etc,$(1),agentd)
	$(call Package/zabbix3/install/init.d,$(1),agentd)
endef

define Package/zabbix3-sender/install
	$(call Package/zabbix3/install/bin,$(1),sender)
endef

define Package/zabbix3-get/install
	$(call Package/zabbix3/install/bin,$(1),get)
endef

define Package/zabbix3-server/install
	$(call Package/zabbix3/install/sbin,$(1),server)
	$(call Package/zabbix3/install/etc,$(1),server)
	$(call Package/zabbix3/install/init.d,$(1),server)
endef

define Package/zabbix3-proxy/install
	$(call Package/zabbix3/install/sbin,$(1),proxy)
	$(call Package/zabbix3/install/etc,$(1),proxy)
	$(call Package/zabbix3/install/init.d,$(1),proxy)
endef

define Package/zabbix3-core/install
	$(INSTALL_DIR) $(1)/lib/zabbix/agentd
	$(INSTALL_BIN) ./files/zlock.sh $(1)/lib/zabbix/
endef

$(eval $(call BuildPackage,zabbix3-core))
$(eval $(call BuildPackage,zabbix3-agentd))
$(eval $(call BuildPackage,zabbix3-sender))
$(eval $(call BuildPackage,zabbix3-server))
$(eval $(call BuildPackage,zabbix3-proxy))
$(eval $(call BuildPackage,zabbix3-get))

