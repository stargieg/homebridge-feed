# This is free software, licensed under the GNU General Public License v2.
# See /LICENSE for more information.
#

include $(TOPDIR)/rules.mk

PKG_NPM_NAME:=homebridge
PKG_NAME:=node-$(PKG_NPM_NAME)
PKG_VERSION:=1.8.3
#PKG_VERSION>1.8.3
#required: { node: '20 || >=22' }
#current: { node: 'v18.20.4', npm: '10.7.0' }
#PKG_VERSION:=1.8.5
PKG_RELEASE:=2

PKG_SOURCE:=$(PKG_NPM_NAME)-$(PKG_VERSION).tgz
PKG_SOURCE_URL:=https://registry.npmjs.org/$(PKG_NPM_NAME)/-/
PKG_MIRROR_HASH:=skip
PKG_HASH:=skip

PKG_BUILD_DEPENDS:=node/host
PKG_BUILD_FLAGS:=no-mips16

PKG_MAINTAINER:=Hirokazu MORIKAWA <morikw2@gmail.com>
PKG_LICENSE:=ISC Apache-2.0
PKG_LICENSE_FILES:=LICENSE

include $(INCLUDE_DIR)/package.mk

define Package/node-homebridge
  SUBMENU:=Node.js
  SECTION:=lang
  CATEGORY:=Languages
  TITLE:=Node.js HomeKit Server
  URL:=https://www.npmjs.org/package/homebridge
  DEPENDS:=+node +node-npm
  USERID:=homebridge:homebridge
endef

define Package/node-homebridge/description
 Homebridge is a lightweight Node.js server you can run on your home network that emulates the iOS HomeKit API
 *** You need to install avahi-nodbus-daemon or avahi-dbus-daemon or umdns or mdnsd package. ***
endef

TAR_OPTIONS+= --strip-components 1
TAR_CMD=$(HOST_TAR) -C $(1) $(TAR_OPTIONS)

NODEJS_CPU:=$(subst powerpc,ppc,$(subst aarch64,arm64,$(subst x86_64,x64,$(subst i386,ia32,$(ARCH)))))
TMPNPM:=$(shell mktemp -u XXXXXXXXXX)

TARGET_CFLAGS+=$(FPIC)
TARGET_CPPFLAGS+=$(FPIC)

NPM_FLAGS=$(MAKE_VARS) \
	$(MAKE_FLAGS) \
	npm_config_arch=$(NODEJS_CPU) \
	npm_config_target_arch=$(NODEJS_CPU) \
	npm_config_build_from_source=true \
	npm_config_nodedir=$(STAGING_DIR)/usr/ \
	npm_config_prefix=$(PKG_INSTALL_DIR)/usr/ \
	npm_config_cache=$(TMP_DIR)/npm-cache-$(TMPNPM) \
	npm_config_tmp=$(TMP_DIR)/npm-tmp-$(TMPNPM)

#$(NPM_FLAGS) npm i -g --production $(PKG_BUILD_DIR)
#$(NPM_FLAGS) npm i --production --prefix=$(PKG_BUILD_DIR) --target_arch=$(NODEJS_CPU) --prefer-dedupe
define Build/Compile
	$(NPM_FLAGS) npm i -g --omit=dev $(PKG_BUILD_DIR)
	$(NPM_FLAGS) npm i --omit=dev --prefix=$(PKG_BUILD_DIR) --target_arch=$(NODEJS_CPU) --prefer-dedupe
	npm install -g $(PKG_BUILD_DIR)
	rm -rf $(TMP_DIR)/npm-tmp-$(TMPNPM)
	rm -rf $(TMP_DIR)/npm-cache-$(TMPNPM)
	rm -rf $(PKG_BUILD_DIR)/node_modules/@homebridge/put/test/c/itof
	rm -rf $(PKG_BUILD_DIR)/node_modules/@homebridge/put/test/c/ftoi
endef

define Package/node-homebridge/install
	$(INSTALL_DIR) $(1)/usr/lib/node/$(PKG_NPM_NAME)
	$(CP) $(PKG_INSTALL_DIR)/usr/lib/node_modules/$(PKG_NPM_NAME)/{*.json,*.md} \
		$(1)/usr/lib/node/$(PKG_NPM_NAME)/
	$(CP) $(PKG_INSTALL_DIR)/usr/lib/node_modules/$(PKG_NPM_NAME)/{node_modules,lib} \
		$(1)/usr/lib/node/$(PKG_NPM_NAME)/
	$(CP) $(PKG_INSTALL_DIR)/usr/lib/node_modules/$(PKG_NPM_NAME)/{LICENSE,bin} \
		$(1)/usr/lib/node/$(PKG_NPM_NAME)/
	$(INSTALL_DIR) $(1)/usr/bin
	$(LN) ../lib/node/$(PKG_NPM_NAME)/bin/homebridge $(1)/usr/bin/homebridge
	$(INSTALL_DIR) $(1)/etc/init.d
	$(INSTALL_BIN) ./files/homebridge.init $(1)/etc/init.d/homebridge
endef

$(eval $(call BuildPackage,node-homebridge))
