#
# This is free software, licensed under the GNU General Public License v2.
# See /LICENSE for more information.
#

include $(TOPDIR)/rules.mk

PKG_NAME:=adguardhome
PKG_VERSION:=0.107.64
PKG_RELEASE:=1

PKG_SOURCE:=$(PKG_NAME)-$(PKG_VERSION).tar.gz
PKG_SOURCE_URL:=https://codeload.github.com/AdguardTeam/AdGuardHome/tar.gz/v$(PKG_VERSION)?
PKG_HASH:=a6b61d3c102fa47072a7230382d438f3d408f74ffae3aff7d330adad90ed169c
PKG_BUILD_DIR:=$(BUILD_DIR)/AdGuardHome-$(PKG_VERSION)

FRONTEND_DEST:=$(PKG_NAME)-frontend-$(PKG_VERSION).tar.gz
FRONTEND_URL:=https://github.com/AdguardTeam/AdGuardHome/releases/download/v$(PKG_VERSION)/
FRONTEND_HASH:=f0c1e4d6e673d4d26d52947cdb220682aa554158d331fa044576d1794b82e325

PKG_LICENSE:=GPL-3.0-only
PKG_LICENSE_FILES:=LICENSE.txt
PKG_CPE_ID:=cpe:/a:adguard:adguardhome
PKG_MAINTAINER:=Dobroslaw Kijowski <dobo90@gmail.com>, George Sapkin <george@sapk.in>

PKG_BUILD_DEPENDS:=golang/host
PKG_BUILD_PARALLEL:=1
PKG_BUILD_FLAGS:=no-mips16

GO_PKG:=github.com/AdguardTeam/AdGuardHome
GO_PKG_BUILD_PKG:=$(GO_PKG)

GO_PKG_LDFLAGS_X:= \
	$(GO_PKG)/internal/version.channel=release \
	$(GO_PKG)/internal/version.version=v$(PKG_VERSION) \
	$(GO_PKG)/internal/version.committime=$(SOURCE_DATE_EPOCH) \
	$(GO_PKG)/internal/version.goarm=$(GO_ARM) \
	$(GO_PKG)/internal/version.gomips=$(GO_MIPS)

include $(INCLUDE_DIR)/package.mk
include ../../lang/golang/golang-package.mk

define Package/adguardhome
  SECTION:=net
  CATEGORY:=Network
  TITLE:=Network-wide ads and trackers blocking DNS server
  URL:=https://github.com/AdguardTeam/AdGuardHome
  DEPENDS:=$(GO_ARCH_DEPENDS) +ca-bundle
endef

define Package/adguardhome/conffiles
/etc/adguardhome.yaml
/etc/config/adguardhome
endef

define Package/adguardhome/description
  Free and open source, powerful network-wide ads and trackers blocking DNS server.
endef

define Download/adguardhome-frontend
	URL:=$(FRONTEND_URL)
	URL_FILE:=AdGuardHome_frontend.tar.gz
	FILE:=$(FRONTEND_DEST)
	HASH:=$(FRONTEND_HASH)
endef

define Build/Prepare
	$(call Build/Prepare/Default)

	gzip -dc $(DL_DIR)/$(FRONTEND_DEST) | $(HOST_TAR) -C $(PKG_BUILD_DIR)/ $(TAR_OPTIONS)
endef

define Package/adguardhome/install
	$(call GoPackage/Package/Install/Bin,$(1))
	$(INSTALL_DIR) $(1)/etc/init.d
	$(INSTALL_BIN) ./files/adguardhome.init $(1)/etc/init.d/adguardhome

	$(INSTALL_DIR) $(1)/etc/config
	$(INSTALL_DATA) ./files/adguardhome.config $(1)/etc/config/adguardhome

	$(INSTALL_DIR) $(1)/etc/sysctl.d
	$(INSTALL_CONF) ./files/adguardhome.sysctl $(1)/etc/sysctl.d/50-adguardhome.conf
endef

$(eval $(call Download,adguardhome-frontend))
$(eval $(call GoBinPackage,adguardhome))
$(eval $(call BuildPackage,adguardhome))
