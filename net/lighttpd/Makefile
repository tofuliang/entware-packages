#
# Copyright (C) 2006-2015 OpenWrt.org
#
# This is free software, licensed under the GNU General Public License v2.
# See /LICENSE for more information.
#

include $(TOPDIR)/rules.mk

PKG_NAME:=lighttpd
PKG_VERSION:=1.4.48
PKG_RELEASE:=2

PKG_SOURCE:=$(PKG_NAME)-$(PKG_VERSION).tar.xz
PKG_SOURCE_URL:=https://download.lighttpd.net/lighttpd/releases-1.4.x
PKG_HASH:=0f8ad5aac7529d7b948b9d7e8cd0b4a9e177309d85d6bf6516e28e6e40d74f36

PKG_LICENSE:=BSD-3c
PKG_LICENSE_FILES:=COPYING

PKG_FIXUP:=autoreconf
PKG_INSTALL:=1

REBUILD_MODULES=authn_gssapi authn_ldap authn_mysql cml magnet mysql_vhost trigger_b4_dl webdav

PKG_CONFIG_DEPENDS:=CONFIG_LIGHTTPD_SSL $(patsubst %,CONFIG_PACKAGE_lighttpd-mod-%,$(REBUILD_MODULES))

include $(INCLUDE_DIR)/package.mk

define Package/lighttpd/Default
  SUBMENU:=Web Servers/Proxies
  SECTION:=net
  CATEGORY:=Network
  URL:=http://www.lighttpd.net/
  MAINTAINER:=W. Michael Petullo <mike@flyn.org>
endef

define Package/lighttpd
  $(call Package/lighttpd/Default)
  MENU:=1
  DEPENDS:=+LIGHTTPD_SSL:libopenssl +libpcre +libpthread
  TITLE:=A flexible and lightweight web server
endef

define Package/lighttpd/config
config LIGHTTPD_SSL
	bool "SSL support"
	depends on PACKAGE_lighttpd
	default y
	help
	  Implements SSL support in lighttpd (using libopenssl). This
	  option is required if you enable the SSL engine in your
	  lighttpd confguration file.
endef

BASE_MODULES:=dirlisting indexfile staticfile

CONFIGURE_ARGS+= \
	--libdir=/opt/lib/lighttpd \
	--sysconfdir=/opt/etc/lighttpd \
	--enable-shared \
	--enable-static \
	--disable-rpath \
	--without-attr \
	--without-bzip2 \
	--without-fam \
	--with-pcre \
	--without-valgrind \
	 $(call autoconf_bool,CONFIG_IPV6,ipv6)

CONFIGURE_VARS+= \
	PCRE_LIB="-lpcre" \

ifneq ($(strip $(CONFIG_LIGHTTPD_SSL)),)
  CONFIGURE_ARGS+= \
	--with-openssl="$(STAGING_DIR)/opt"
  BASE_MODULES+= openssl
else
  CONFIGURE_ARGS+= \
	--without-openssl
endif

#ifneq ($(SDK)$(CONFIG_PACKAGE_lighttpd-mod-authn_gssapi),)
#  CONFIGURE_ARGS+= --with-krb5
#else
#  CONFIGURE_ARGS+= --without-krb5
#endif

ifneq ($(SDK)$(CONFIG_PACKAGE_lighttpd-mod-authn_ldap),)
  CONFIGURE_ARGS+= --with-ldap
else
  CONFIGURE_ARGS+= --without-ldap
endif

ifneq ($(SDK)$(CONFIG_PACKAGE_lighttpd-mod-authn_mysql),)
  CONFIGURE_ARGS+= --with-mysql
else
  CONFIGURE_ARGS+= --without-mysql
endif

#ifneq ($(SDK)$(CONFIG_PACKAGE_lighttpd-mod-geoip),)
#  CONFIGURE_ARGS+= --with-geoip
#else
#  CONFIGURE_ARGS+= --without-geoip
#endif

ifneq ($(SDK)$(CONFIG_PACKAGE_lighttpd-mod-cml)$(CONFIG_PACKAGE_lighttpd-mod-magnet),)
  CONFIGURE_ARGS+= --with-lua
else
  CONFIGURE_ARGS+= --without-lua
endif

ifneq ($(SDK)$(CONFIG_PACKAGE_lighttpd-mod-mysql_vhost),)
  CONFIGURE_ARGS+= --with-mysql
else
  CONFIGURE_ARGS+= --without-mysql
endif

#ifneq ($(SDK)$(CONFIG_PACKAGE_lighttpd-mod-cml)$(CONFIG_PACKAGE_lighttpd-mod-trigger_b4_dl),)
#  CONFIGURE_ARGS+= --with-memcached
#else
#  CONFIGURE_ARGS+= --without-memcached
#endif

ifneq ($(SDK)$(CONFIG_PACKAGE_lighttpd-mod-trigger_b4_dl),)
  CONFIGURE_ARGS+= --with-gdbm
else
  CONFIGURE_ARGS+= --without-gdbm
endif

ifneq ($(SDK)$(CONFIG_PACKAGE_lighttpd-mod-webdav),)
  CONFIGURE_ARGS+= \
	--with-webdav-locks \
	--with-webdav-props
  # XXX: needed by sqlite3 to prevent segfaults in mod_webdav.so
  CONFIGURE_VARS+= \
	LIBS="-lpthread"
else
  CONFIGURE_ARGS+= \
	--without-webdav-locks \
	--without-webdav-props
endif

define Build/Configure
$(call Build/Configure/Default)
	# XXX: override pcre (mis)detection by ./configure when cross-compiling
	echo "#define HAVE_LIBPCRE 1" >>$(PKG_BUILD_DIR)/config.h
	echo "#define HAVE_PCRE_H 1" >>$(PKG_BUILD_DIR)/config.h
endef

define Package/lighttpd/conffiles
/opt/etc/lighttpd/lighttpd.conf
/opt/etc/lighttpd/conf.d/30-accesslog.conf
/opt/etc/lighttpd/conf.d/30-access.conf
/opt/etc/lighttpd/conf.d/30-alias.conf
/opt/etc/lighttpd/conf.d/20-auth.conf
/opt/etc/lighttpd/conf.d/30-cgi.conf
/opt/etc/lighttpd/conf.d/30-cml.conf
/opt/etc/lighttpd/conf.d/30-compress.conf
/opt/etc/lighttpd/conf.d/30-evasive.conf
/opt/etc/lighttpd/conf.d/30-evhost.conf
/opt/etc/lighttpd/conf.d/30-expire.conf
/opt/etc/lighttpd/conf.d/30-extforward.conf
/opt/etc/lighttpd/conf.d/30-fastcgi.conf
/opt/etc/lighttpd/conf.d/30-flv_streaming.conf
/opt/etc/lighttpd/conf.d/30-magnet.conf
/opt/etc/lighttpd/conf.d/30-mysql_vhost.conf
/opt/etc/lighttpd/conf.d/30-proxy.conf
/opt/etc/lighttpd/conf.d/10-redirect.conf
/opt/etc/lighttpd/conf.d/30-rewrite.conf
/opt/etc/lighttpd/conf.d/30-rrdtool.conf
/opt/etc/lighttpd/conf.d/30-scgi.conf
/opt/etc/lighttpd/conf.d/30-secdownload.conf
/opt/etc/lighttpd/conf.d/30-setenv.conf
/opt/etc/lighttpd/conf.d/30-simple_vhost.conf
/opt/etc/lighttpd/conf.d/30-ssi.conf
/opt/etc/lighttpd/conf.d/30-status.conf
/opt/etc/lighttpd/conf.d/30-trigger_b4_dl.conf
/opt/etc/lighttpd/conf.d/30-userdir.conf
/opt/etc/lighttpd/conf.d/30-usertrack.conf
/opt/etc/lighttpd/conf.d/30-webdav.conf
endef

define Package/lighttpd/install
	$(INSTALL_DIR) $(1)/opt/etc/lighttpd
	$(INSTALL_DATA) ./files/lighttpd.conf $(1)/opt/etc/lighttpd/
	$(INSTALL_DATA) $(PKG_BUILD_DIR)/doc/config/conf.d/mime.conf $(1)/opt/etc/lighttpd/
	$(INSTALL_DIR) $(1)/opt/etc/lighttpd/conf.d
	$(INSTALL_DIR) $(1)/opt/etc/init.d
	$(INSTALL_BIN) ./files/S80lighttpd $(1)/opt/etc/init.d/
	$(INSTALL_DIR) $(1)/opt/lib/lighttpd
	for m in $(BASE_MODULES); do \
		$(CP) $(PKG_INSTALL_DIR)/opt/lib/lighttpd/mod_$$$${m}.so $(1)/opt/lib/lighttpd/ ; \
	done
	$(INSTALL_DIR) $(1)/opt/sbin
	$(INSTALL_BIN) $(PKG_INSTALL_DIR)/opt/sbin/lighttpd $(1)/opt/sbin/
	$(INSTALL_DIR) $(1)/opt/share/www $(1)/opt/var/log/lighttpd
endef

define BuildPlugin
  define Package/lighttpd-mod-$(1)
    $(call Package/lighttpd/Default)
    DEPENDS:=lighttpd
    ifneq ($(3),)
      DEPENDS+= $(3)
    endif
    TITLE:=$(2) module
  endef

 ifneq ($(SDK)$(CONFIG_PACKAGE_lighttpd-mod-$(1)),)
  define Package/lighttpd-mod-$(1)/install
	$(INSTALL_DIR) $$(1)/opt/lib/lighttpd
	$(CP) $(PKG_INSTALL_DIR)/opt/lib/lighttpd/mod_$(1).so $$(1)/opt/lib/lighttpd
	$(INSTALL_DIR) $$(1)/opt/etc/lighttpd/conf.d
	if [ -f $(PKG_BUILD_DIR)/doc/config/conf.d/$(1).conf ]; then \
		$(CP) $(PKG_BUILD_DIR)/doc/config/conf.d/$(1).conf $$(1)/opt/etc/lighttpd/conf.d/$(4)-$(1).conf ; \
	else \
		echo 'server.modules += ( "mod_$(1)" )' > $$(1)/opt/etc/lighttpd/conf.d/$(4)-$(1).conf ; \
	fi
  endef
 endif

  $$(eval $$(call BuildPackage,lighttpd-mod-$(1)))
endef

$(eval $(call BuildPackage,lighttpd))

# First, permit redirect from HTTP to HTTPS.
$(eval $(call BuildPlugin,redirect,URL redirection,+PACKAGE_lighttpd-mod-redirect:libpcre,10))

# Next, permit authentication.
$(eval $(call BuildPlugin,auth,Authentication,,20))
$(eval $(call BuildPlugin,authn_file,File-based authentication,,20))
#$(eval $(call BuildPlugin,authn_gssapi,Kerberos-based authentication,+PACKAGE_lighttpd-mod-authn_gssapi:krb5-libs,20))
$(eval $(call BuildPlugin,authn_ldap,LDAP-based authentication,+PACKAGE_lighttpd-mod-authn_ldap:libopenldap,20))
$(eval $(call BuildPlugin,authn_mysql,Mysql-based authentication,+PACKAGE_lighttpd-mod-authn_mysql:libmysqlclient,20))

# Finally, everything else.
$(eval $(call BuildPlugin,access,Access restrictions,,30))
$(eval $(call BuildPlugin,accesslog,Access logging,,30))
$(eval $(call BuildPlugin,alias,Directory alias,,30))
$(eval $(call BuildPlugin,cgi,CGI,,30))
#$(eval $(call BuildPlugin,cml,Cache Meta Language,+PACKAGE_lighttpd-mod-cml:liblua +PACKAGE_lighttpd-mod-cml:libmemcached,30))
$(eval $(call BuildPlugin,cml,Cache Meta Language,+PACKAGE_lighttpd-mod-cml:liblua,30))
$(eval $(call BuildPlugin,compress,Compress output,+PACKAGE_lighttpd-mod-compress:zlib,30))
$(eval $(call BuildPlugin,deflate,Compress dynamic output,+PACKAGE_lighttpd-mod-deflate:zlib,30))
$(eval $(call BuildPlugin,evasive,Evasive,,30))
$(eval $(call BuildPlugin,evhost,Enhanced Virtual-Hosting,,30))
$(eval $(call BuildPlugin,expire,Expire,,30))
$(eval $(call BuildPlugin,extforward,Extract client,,30))
$(eval $(call BuildPlugin,fastcgi,FastCGI,,30))
$(eval $(call BuildPlugin,flv_streaming,FLV streaming,,30))
#$(eval $(call BuildPlugin,geoip,Geolocation,+PACKAGE_lighttpd-mod-geoip:libgeoip,30))
$(eval $(call BuildPlugin,magnet,Magnet,+PACKAGE_lighttpd-mod-magnet:liblua,30))
$(eval $(call BuildPlugin,mysql_vhost,Mysql virtual hosting,+PACKAGE_lighttpd-mod-mysql_vhost:libmysqlclient,30))
$(eval $(call BuildPlugin,proxy,Proxy,,30))
$(eval $(call BuildPlugin,rewrite,URL rewriting,+PACKAGE_lighttpd-mod-rewrite:libpcre,30))
$(eval $(call BuildPlugin,rrdtool,RRDtool,,30))
$(eval $(call BuildPlugin,scgi,SCGI,,30))
$(eval $(call BuildPlugin,secdownload,Secure and fast download,,30))
$(eval $(call BuildPlugin,setenv,Environment variable setting,,30))
$(eval $(call BuildPlugin,simple_vhost,Simple virtual hosting,,30))
$(eval $(call BuildPlugin,ssi,SSI,+PACKAGE_lighttpd-mod-ssi:libpcre,30))
$(eval $(call BuildPlugin,status,Server status display,,30))
#$(eval $(call BuildPlugin,trigger_b4_dl,Trigger before download,+PACKAGE_lighttpd-mod-trigger_b4_dl:libpcre +PACKAGE_lighttpd-mod-trigger_b4_dl:libgdbm +PACKAGE_lighttpd-mod-trigger_b4_dl:libmemcached,30))
$(eval $(call BuildPlugin,trigger_b4_dl,Trigger before download,+PACKAGE_lighttpd-mod-trigger_b4_dl:libpcre +PACKAGE_lighttpd-mod-trigger_b4_dl:libgdbm,30))
$(eval $(call BuildPlugin,userdir,User directory,,30))
$(eval $(call BuildPlugin,usertrack,User tracking,,30))
$(eval $(call BuildPlugin,webdav,WebDAV,+PACKAGE_lighttpd-mod-webdav:libsqlite3 +PACKAGE_lighttpd-mod-webdav:libuuid +PACKAGE_lighttpd-mod-webdav:libxml2,30))

