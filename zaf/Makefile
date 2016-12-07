#
# Copyright (C) 2006-2014 OpenWrt.org
#
# This is free software, licensed under the GNU General Public License v2.
# See /LICENSE for more information.
#

include $(TOPDIR)/rules.mk

PKG_NAME:=zaf
PKG_VERSION:=1.3
PKG_RELEASE:=$(PKG_SOURCE_VERSION)

PKG_SOURCE_PROTO:=git
PKG_SOURCE_URL:=https://github.com/limosek/zaf.git
PKG_SOURCE_SUBDIR:=$(PKG_NAME)-$(PKG_VERSION)
PKG_SOURCE_VERSION:=1.3
PKG_SOURCE:=$(PKG_NAME)-$(PKG_VERSION)-$(PKG_SOURCE_VERSION).tar.gz

PKG_LICENSE:=GPL-2.0
PKG_LICENSE_FILES:=COPYING

include $(INCLUDE_DIR)/package.mk

define Package/zaf
  SECTION:=admin
  CATEGORY:=Administration
  TITLE:=Zabbix framework
  URL:=https://github.com/limosek/zaf/
  SUBMENU:=zabbix3
  MAINTAINER:=Lukas Macura <lukas.macura@gmail.com>
  DEPENDS += +zabbix3-agentd +curl
endef

define Package/zaf/config
source "$(SOURCE)/Config.in"
endef

define Package/zaf/conffiles
/etc/zaf.conf
endef

ifneq ($(CONFIG_ZAF_CURL_INSECURE),)
 ZAF_CURL_INSECURE=ZAF_CURL_INSECURE=1
endif
ifneq ($(CONFIG_ZAF_OPTIONS),"")
 ZAF_OPTIONS=$(CONFIG_ZAF_OPTIONS)
endif
ifneq ($(CONFIG_ZAF_REPO_URL),"")
 ZAF_REPO=ZAF_REPO_URL=$(CONFIG_ZAF_REPO_URL)
endif

define Package/zaf/compile
endef

define Package/zaf/install
	cd $(PKG_BUILD_DIR); \
	INSTALL_PREFIX=$(1) ./zaf cache-clean; \
	INSTALL_PREFIX=$(1) ZAF_DEBUG=2 sh ./install.sh auto ZAF_SUDOERSD= ZAF_CROND=/etc/crontabs $(ZAF_CURL_INSECURE) ZAF_PKG=opkg ZAF_OS=openwrt ZAF_OS_CODENAME=$(CONFIG_VERSION_NICK) \
	ZAF_FILES_UID=zabbix ZAF_FILES_GID=zabbix ZAF_FILES_UMASK=0770 \
	ZAF_PKG=opkg ZAF_GIT=0 "$(ZAF_REPO)" \
	ZAF_AGENT_CONFIG='/etc/zabbix_agentd.conf' ZAF_AGENT_CONFIGD='/var/run/zabbix_agentd.conf.d/' \
	ZAF_AGENT_RESTART='/etc/init.d/zabbix_agentd restart' ZAF_PREPACKAGED_DIR="/usr/lib/zaf/prepackaged/" ZAF_PLUGINS=$(CONFIG_ZAF_PLUGINS) $(ZAF_OPTIONS)
endef

$(eval $(call BuildPackage,zaf))

