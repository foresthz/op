# 
# Copyright (C) 2006 OpenWrt.org
#
# This is free software, licensed under the GNU General Public License v2.
# See /LICENSE for more information.
#
include $(TOPDIR)/rules.mk

ARCH:=armeb
BOARD:=ixp4xx
BOARDNAME:=Intel XScale IXP4xx
FEATURES:=squashfs

define Target/Description
endef

include $(INCLUDE_DIR)/kernel-build.mk

# include the profiles
-include profiles/*.mk

$(eval $(call BuildKernel))
