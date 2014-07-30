#
# Copyright (C) 2007-2012 OpenWrt.org
#
# This is free software, licensed under the GNU General Public License v2.
# See /LICENSE for more information.
#

include $(TOPDIR)/rules.mk

PKG_NAME:=avahi-no-dbus
PKG_VERSION:=0.6.31
PKG_RELEASE:=5


PKG_SOURCE:=avahi-0.6.31.tar.gz
PKG_SOURCE_URL:=http://avahi.org/download/
PKG_MD5SUM:=2f22745b8f7368ad5a0a3fddac343f2d

PKG_BUILD_DEPENDS:=libexpat libdaemon libgdbm intltool/host libpthread dbus

PKG_FIXUP:=autoreconf
PKG_REMOVE_FILES:=autogen.sh

PKG_INSTALL:=1
PKG_BUILD_PARALLEL:=1

PKG_BUILD_DIR=$(BUILD_DIR)/$(PKG_NAME)/avahi-$(PKG_VERSION)


include $(INCLUDE_DIR)/package.mk

define Package/avahi-no-dbus/Default
  SECTION:=net
  CATEGORY:=Network
  TITLE:=An mDNS/DNS-SD implementation - no-dbus variant
  URL:=http://www.avahi.org/
endef

define Package/avahi-no-dbus/Default/description
 Avahi is an mDNS/DNS-SD (aka RendezVous/Bonjour/ZeroConf)
 implementation (library). It facilitates
 service discovery on a local network -- this means that
 you can plug your laptop or computer into a network and
 instantly be able to view other people who you can chat with,
 find printers to print to or find files being shared.
 This kind of technology is already found in MacOS X
 (branded 'Rendezvous', 'Bonjour' and sometimes 'ZeroConf')
 and is very convenient.
endef

define Package/libavahi-no-dbus
  $(call Package/avahi-no-dbus/Default)
  SECTION:=libs
  CATEGORY:=Libraries
  DEPENDS:=+libpthread +SSP_SUPPORT:libssp
  TITLE+= (library)
endef

define Package/libavahi-no-dbus/description
$(call Package/avahi-no-dbus/Default/description)
 .
 The libavahi package contains the mDNS/DNS-SD shared libraries,
 used by other programs. Specifically, it provides
 libavahi-core and libavahi-common libraries.
 By default, it is built without D-Bus support,
 i.e. the --disable-dbus configuration flag is set.
 To enable D-Bus support, select the package
 libavahi-dbus-support.
endef

define Package/avahi-daemon-no-dbus
  $(call Package/avahi-no-dbus/Default)
  SUBMENU:=IP Addresses and Names 
  DEPENDS:=+libavahi-no-dbus +libexpat +librt +libdaemon
  TITLE+= (daemon)
endef

define Package/avahi-daemon-no-dbus/description
$(call Package/avahi-no-dbus/Default/description)
 .
 This package contains an mDNS/DNS-SD daemon.
endef

define Package/avahi-daemon/conffiles
/etc/avahi/avahi-daemon.conf
/etc/avahi/services/http.service
/etc/avahi/services/ssh.service
endef
TARGET_CFLAGS += $(FPIC) -DGETTEXT_PACKAGE

CONFIGURE_ARGS+= \
	--enable-shared \
	--enable-static \
	--disable-glib \
	--disable-gobject \
	--disable-qt3 \
	--disable-qt4 \
	--disable-gtk \
	--disable-gtk3 \
	--with-xml=expat \
	--disable-dbm \
	--enable-gdbm \
	--enable-libdaemon \
	--disable-python \
	--disable-pygtk \
	--disable-python-dbus \
	--disable-mono \
	--disable-monodoc \
	--disable-doxygen-doc \
	--disable-doxygen-dot \
	--disable-doxygen-man \
	--disable-doxygen-rtf \
	--disable-doxygen-xml \
	--disable-doxygen-chm \
	--disable-doxygen-chi \
	--disable-doxygen-html \
	--disable-doxygen-ps \
	--disable-doxygen-pdf \
	--disable-xmltoman \
	--with-distro=none \
	--with-avahi-user=nobody \
	--with-avahi-group=nogroup \
	--with-autoipd-user=nobody \
	--with-autoipd-group=nogroup \
	--disable-dbus

ifneq ($(CONFIG_SSP_SUPPORT),y)
CONFIGURE_ARGS+= \
	--disable-stack-protector
endif


CONFIGURE_VARS+= \
	CFLAGS="$$$$CFLAGS -DNDEBUG -DDISABLE_SYSTEMD" \
	ac_cv_header_sys_capability_h=no \

define Build/InstallDev
	$(INSTALL_DIR) $(1)/usr/include
	$(CP) $(PKG_INSTALL_DIR)/usr/include/* $(1)/usr/include/
	$(INSTALL_DIR) $(1)/usr/lib
	$(CP) $(PKG_INSTALL_DIR)/usr/lib/libavahi-* $(1)/usr/lib/
	$(INSTALL_DIR) $(1)/usr/lib/pkgconfig
	$(CP) $(PKG_INSTALL_DIR)/usr/lib/pkgconfig/* $(1)/usr/lib/pkgconfig/
endef

define Package/libavahi-no-dbus/install
	# The next line removes the ".build" file from the "other" build.
	# The effect is that, if the other build is re-selected in the future,
	# the build system will be forced to replace all the code in the
	# installer packages, removing anything from the current build.
	# "Other" means this: if the current build is "dbus", the other is "nodebus",
	# and if the current build is "nodbus", the other is "dbus".
	$(RM) -f $(PKG_ALT_DIR)/.built
	$(INSTALL_DIR) $(1)/usr/lib
	$(CP) $(PKG_INSTALL_DIR)/usr/lib/libavahi-{common,core}.so.* $(1)/usr/lib/
endef

define Package/avahi-daemon-no-dbus/install
	$(INSTALL_DIR) $(1)/usr/sbin
	$(INSTALL_BIN) $(PKG_INSTALL_DIR)/usr/sbin/avahi-daemon $(1)/usr/sbin/
	$(INSTALL_DIR) $(1)/etc/avahi
	$(INSTALL_DATA) ./files/avahi-daemon.conf $(1)/etc/avahi/
	$(INSTALL_DIR) $(1)/etc/avahi/services
	$(INSTALL_DATA) ./files/service-http $(1)/etc/avahi/services/http.service
	$(INSTALL_DATA) ./files/service-ssh $(1)/etc/avahi/services/ssh.service
	$(INSTALL_DIR) $(1)/etc/init.d
	$(INSTALL_BIN) ./files/avahi-daemon.init $(1)/etc/init.d/avahi-daemon
endef

$(eval $(call BuildPackage,libavahi-no-dbus))
$(eval $(call BuildPackage,avahi-daemon-no-dbus))
