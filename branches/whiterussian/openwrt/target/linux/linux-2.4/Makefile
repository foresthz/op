#############################################################
# $Id$
#
# Linux 2.4 kernel target for the OpenWRT project
#
# patches are sorted by numbers
# 0xx	needed patches for board, cpu, filesystem
# 1xx	addon patches for new features (netfilter, ebtables, cifs,..)
# 2xx	compile fixes, driver fixes
#
#############################################################

include $(TOPDIR)/rules.mk
include ../rules.mk

LINUX_VERSION:=2.4.30
MODULES_SUBDIR:=lib/modules/$(LINUX_VERSION)
LINUX_KCONFIG:=./config/$(BOARD)
LINUX_BUILD_DIR:=$(BUILD_DIR)/linux-2.4-$(BOARD)
LINUX_TARGET_DIR:=$(LINUX_BUILD_DIR)/root
LINUX_KERNEL:=$(LINUX_BUILD_DIR)/vmlinux
LINUX_KARCH:=$(shell echo $(ARCH) | sed -e 's/i[3-9]86/i386/' \
	-e 's/mipsel/mips/' \
	-e 's/powerpc/ppc/' \
	-e 's/sh[234]/sh/' \
	)

LINUX_BINARY_DRIVER_SITE := http://openwrt.org/downloads/sources/
LINUX_SOURCE := linux-$(LINUX_VERSION).tar.bz2
LINUX_SOURCE_DIR := $(LINUX_BUILD_DIR)/linux-$(LINUX_VERSION)
MODULES_DIR := $(LINUX_BUILD_DIR)/modules/$(MODULES_SUBDIR)
TARGET_MODULES_DIR := $(TARGET_DIR)/lib/modules/$(LINUX_VERSION)
IPKG_KERNEL:=IPKG_TMP=$(BUILD_DIR)/tmp IPKG_INSTROOT=$(LINUX_TARGET_DIR) IPKG_CONF_DIR=$(LINUX_BUILD_DIR) $(SCRIPT_DIR)/ipkg -force-defaults -force-depends

PKG_BUILD_DIR := $(LINUX_BUILD_DIR)/linux-modules
PKG_RELEASE := 5

KERNEL_IPKG:=$(LINUX_BUILD_DIR)/kernel_$(LINUX_VERSION)-$(BOARD)-$(PKG_RELEASE)_$(ARCH).ipk
KERNEL_IDIR:=$(LINUX_BUILD_DIR)/kernel-ipkg

KPKG_MAKEOPTS:=	IPKG="$(IPKG_KERNEL)" \
		BOARD="$(BOARD)" \
		TARGET_DIR="$(LINUX_TARGET_DIR)" \
		BUILD_DIR="$(LINUX_BUILD_DIR)" \
		KERNEL_DIR="$(LINUX_SOURCE_DIR)" \
		LINUX_VERSION="$(LINUX_VERSION)" \
		KERNEL_RELEASE="$(PKG_RELEASE)"

include $(LINUX_KCONFIG)

INSTALL_TARGETS := $(KERNEL_IPKG)
TARGETS := 

ifeq ($(BOARD),brcm)
include ./broadcom.mk
endif

include ../netfilter.mk

# metapackage for compatibility ...
$(eval $(call KMOD_template,IPTABLES_EXTRA,iptables-extra,\
,,kmod-ipt-conntrack kmod-ipt-extra kmod-ipt-filter kmod-ipt-ipopt kmod-ipt-ipsec kmod-ipt-nat kmod-ipt-nat-extra kmod-ipt-queue kmod-ipt-ulog))

$(eval $(call KMOD_template,IPT_CONNTRACK,ipt-conntrack,\
	$(foreach mod,$(IPT_CONNTRACK-m),$(MODULES_DIR)/kernel/net/ipv4/netfilter/$(mod).o) \
))
$(eval $(call KMOD_template,IPT_EXTRA,ipt-extra,\
	$(foreach mod,$(IPT_EXTRA-m),$(MODULES_DIR)/kernel/net/ipv4/netfilter/$(mod).o) \
))
$(eval $(call KMOD_template,IPT_FILTER,ipt-filter,\
	$(foreach mod,$(IPT_FILTER-m),$(MODULES_DIR)/kernel/net/ipv4/netfilter/$(mod).o) \
))
$(eval $(call KMOD_template,IPT_IPOPT,ipt-ipopt,\
	$(foreach mod,$(IPT_IPOPT-m),$(MODULES_DIR)/kernel/net/ipv4/netfilter/$(mod).o) \
))
$(eval $(call KMOD_template,IPT_IPSEC,ipt-ipsec,\
	$(foreach mod,$(IPT_IPSEC-m),$(MODULES_DIR)/kernel/net/ipv4/netfilter/$(mod).o) \
))
$(eval $(call KMOD_template,IPT_NAT_DEFAULT,ipt-nat-default,\
	$(foreach mod,$(IPT_NAT_DEFAULT-m),$(MODULES_DIR)/kernel/net/ipv4/netfilter/$(mod).o) \
,,,10,$(IPT_NAT_DEFAULT-m)))
$(eval $(call KMOD_template,IPT_NAT,ipt-nat,\
	$(foreach mod,$(IPT_NAT-m),$(MODULES_DIR)/kernel/net/ipv4/netfilter/$(mod).o) \
,,,20,$(IPT_NAT-m)))
$(eval $(call KMOD_template,IPT_NAT_H323,ipt-nat-h323,\
	$(foreach mod,$(IPT_NAT_H323-m),$(MODULES_DIR)/kernel/net/ipv4/netfilter/$(mod).o) \
,,,40,$(IPT_NAT_H323-m)))
$(eval $(call KMOD_template,IPT_NAT_PPTP,ipt-nat-pptp,\
	$(foreach mod,$(IPT_NAT_PPTP-m),$(MODULES_DIR)/kernel/net/ipv4/netfilter/$(mod).o) \
,,,40,$(IPT_NAT_PPTP-m)))
$(eval $(call KMOD_template,IPT_NAT_RTSP,ipt-nat-rtsp,\
	$(foreach mod,$(IPT_NAT_RTSP-m),$(MODULES_DIR)/kernel/net/ipv4/netfilter/$(mod).o) \
,,,40,$(IPT_NAT_RTSP-m)))
$(eval $(call KMOD_template,IPT_NAT_EXTRA,ipt-nat-extra,\
	$(foreach mod,$(IPT_NAT_EXTRA-m),$(MODULES_DIR)/kernel/net/ipv4/netfilter/$(mod).o) \
,,,40,$(IPT_NAT_EXTRA-m)))
$(eval $(call KMOD_template,IPT_QUEUE,ipt-queue,\
	$(foreach mod,$(IPT_QUEUE-m),$(MODULES_DIR)/kernel/net/ipv4/netfilter/$(mod).o) \
))
$(eval $(call KMOD_template,IPT_ULOG,ipt-ulog,\
	$(foreach mod,$(IPT_ULOG-m),$(MODULES_DIR)/kernel/net/ipv4/netfilter/$(mod).o) \
))

$(eval $(call KMOD_template,IMQ,imq,\
	$(MODULES_DIR)/kernel/net/ipv4/netfilter/*IMQ* \
	$(MODULES_DIR)/kernel/drivers/net/imq.o \
))
$(eval $(call KMOD_template,IPIP,ipip,\
	$(MODULES_DIR)/kernel/net/ipv4/ipip.o \
,CONFIG_NET_IPIP,kmod-ipip))

$(eval $(call KMOD_template,IP6TABLES,ip6tables,\
	$(MODULES_DIR)/kernel/net/ipv6/netfilter/ip*.o \
,CONFIG_IP6_NF_IPTABLES,kmod-ipv6))

$(eval $(call KMOD_template,PCMCIA,pcmcia,\
	$(MODULES_DIR)/kernel/drivers/pcmcia/*.o \
,CONFIG_PCMCIA,,40,pcmcia_core yenta_socket ds))
$(eval $(call KMOD_template,PCMCIA_SERIAL,pcmcia-serial, \
	$(MODULES_DIR)/kernel/drivers/char/pcmcia/serial_cs.o \
,CONFIG_PCMCIA_SERIAL_CS))
$(eval $(call KMOD_template,B44,b44,\
	$(MODULES_DIR)/kernel/drivers/net/b44.o \
,CONFIG_B44))
$(eval $(call KMOD_template,ARPT,arptables,\
	$(MODULES_DIR)/kernel/net/ipv4/netfilter/arp*.o \
,CONFIG_IP_NF_ARPTABLES))
$(eval $(call KMOD_template,EBT,ebtables,\
	$(MODULES_DIR)/kernel/net/bridge/netfilter/*.o \
,CONFIG_BRIDGE_NF_EBTABLES))
$(eval $(call KMOD_template,IPV6,ipv6,\
	$(MODULES_DIR)/kernel/net/ipv6/ipv6.o \
,CONFIG_IPV6,,20,ipv6))
$(eval $(call KMOD_template,CIFS,cifs,\
	$(MODULES_DIR)/kernel/fs/cifs/cifs.o \
,CONFIG_CIFS,,30,cifs))
$(eval $(call KMOD_template,NFS,nfs,\
	$(MODULES_DIR)/kernel/fs/lockd/*.o \
	$(MODULES_DIR)/kernel/fs/nfs/*.o \
	$(MODULES_DIR)/kernel/net/sunrpc/*.o \
,CONFIG_NFS_FS,,30,sunrpc lockd nfs))
$(eval $(call KMOD_template,USB,usb-core,\
	$(MODULES_DIR)/kernel/drivers/usb/usbcore.o \
,CONFIG_USB,,50,usbcore))
$(eval $(call KMOD_template,USB_UHCI,usb-uhci,\
	$(MODULES_DIR)/kernel/drivers/usb/host/uhci.o \
,CONFIG_USB_UHCI_ALT,kmod-usb-core,60,uhci))
$(eval $(call KMOD_template,USB_UHCI,usb-uhci-iv,\
	$(MODULES_DIR)/kernel/drivers/usb/host/usb-uhci.o \
,CONFIG_USB_UHCI,kmod-usb-core,60,usb-uhci-iv)) 
$(eval $(call KMOD_template,USB_OHCI,usb-ohci,\
	$(MODULES_DIR)/kernel/drivers/usb/host/usb-ohci.o \
,CONFIG_USB_OHCI,kmod-usb-core,60,usb-ohci))
$(eval $(call KMOD_template,USB2,usb2,\
	$(MODULES_DIR)/kernel/drivers/usb/host/ehci-hcd.o \
,CONFIG_USB_EHCI_HCD,kmod-usb-core,60,ehci-hcd))
$(eval $(call KMOD_template,USB_STORAGE,usb-storage,\
	$(MODULES_DIR)/kernel/drivers/scsi/*.o \
	$(MODULES_DIR)/kernel/drivers/usb/storage/*.o \
,CONFIG_USB_STORAGE,kmod-usb-core,60,scsi_mod sd_mod usb-storage))
$(eval $(call KMOD_template,USB_SERIAL,usb-serial,\
	$(MODULES_DIR)/kernel/drivers/usb/serial/usbserial.o \
,CONFIG_USB_SERIAL,kmod-usb-core,60,usbserial))
$(eval $(call KMOD_template,USB_SERIAL_BELKIN,usb-serial-belkin,\
	$(MODULES_DIR)/kernel/drivers/usb/serial/belkin_sa.o \
,CONFIG_USB_SERIAL_BELKIN,kmod-usb-serial,61,belkin_sa))
$(eval $(call KMOD_template,USB_SERIAL_FTDI,usb-serial-ftdi,\
	$(MODULES_DIR)/kernel/drivers/usb/serial/ftdi_sio.o \
,CONFIG_USB_SERIAL_FTDI_SIO,kmod-usb-serial,61,ftdi_sio))
$(eval $(call KMOD_template,USB_SERIAL_MCT_U232,usb-serial-mct-u232,\
	$(MODULES_DIR)/kernel/drivers/usb/serial/mct_u232.o \
,CONFIG_USB_SERIAL_MCT_U232,kmod-usb-serial,61,mct_u232))
$(eval $(call KMOD_template,USB_SERIAL_PL2303,usb-serial-pl2303,\
	$(MODULES_DIR)/kernel/drivers/usb/serial/pl2303.o \
,CONFIG_USB_SERIAL_PL2303,kmod-usb-serial,61,pl2303))
$(eval $(call KMOD_template,USB_SERIAL_VISOR,usb-serial-visor,\
	$(MODULES_DIR)/kernel/drivers/usb/serial/visor.o \
,CONFIG_USB_SERIAL_VISOR,kmod-usb-serial,61,visor))
$(eval $(call KMOD_template,USB_PRINTER,usb-printer,\
	$(MODULES_DIR)/kernel/drivers/usb/printer.o \
,CONFIG_USB_PRINTER,kmod-usb-core,60,printer))
$(eval $(call KMOD_template,USB_SCANNER,usb-scanner,\
	$(MODULES_DIR)/kernel/drivers/usb/scanner.o \
,CONFIG_USB_SCANNER,kmod-usb-core,60,scanner))
$(eval $(call KMOD_template,USB_ACM,usb-acm,\
	$(MODULES_DIR)/kernel/drivers/usb/acm.o \
,CONFIG_USB_ACM,kmod-usb-core,60,acm))
$(eval $(call KMOD_template,IDE,ide,\
	$(MODULES_DIR)/kernel/drivers/ide/*.o \
	$(MODULES_DIR)/kernel/drivers/ide/*/*.o \
,CONFIG_IDE,,20,ide-core pdc202xx_old ide-detect ide-disk))
$(eval $(call KMOD_template,LOOP,loop,\
    $(MODULES_DIR)/kernel/drivers/block/loop.o \
,CONFIG_BLK_DEV_LOOP,,20,loop))
$(eval $(call KMOD_template,MINIX,minix,\
	$(MODULES_DIR)/kernel/fs/minix/*.o \
,CONFIG_MINIX_FS,,30,minix))
$(eval $(call KMOD_template,EXT2,ext2,\
	$(MODULES_DIR)/kernel/fs/ext2/*.o \
,CONFIG_EXT2_FS,,30,ext2))
$(eval $(call KMOD_template,EXT3,ext3,\
	$(MODULES_DIR)/kernel/fs/ext3/*.o \
	$(MODULES_DIR)/kernel/fs/jbd/*.o \
,CONFIG_EXT3_FS,,30,jbd ext3))
$(eval $(call KMOD_template,XFS,xfs,\
	$(MODULES_DIR)/kernel/fs/xfs/*.o \
,CONFIG_XFS_FS,,30,xfs))
$(eval $(call KMOD_template,HFSPLUS,hfsplus,\
	$(MODULES_DIR)/kernel/fs/hfsplus/*.o \
,CONFIG_HFSPLUS_FS,,30,hfsplus))
$(eval $(call KMOD_template,VFAT,vfat,\
	$(MODULES_DIR)/kernel/fs/vfat/vfat.o \
	$(MODULES_DIR)/kernel/fs/fat/fat.o \
,CONFIG_VFAT_FS,,30,fat vfat))
$(eval $(call KMOD_template,MSDOS,msdos,\
	$(MODULES_DIR)/kernel/fs/msdos/msdos.o \
,CONFIG_MSDOS_FS,,40,msdos))
$(eval $(call KMOD_template,NLS_CP437,nls-cp437,\
	$(MODULES_DIR)/kernel/fs/nls/nls_cp437.o \
,CONFIG_NLS_CODEPAGE_437,,40,nls_cp437.o))
$(eval $(call KMOD_template,NLS_CP850,nls-cp850,\
	$(MODULES_DIR)/kernel/fs/nls/nls_cp850.o \
,CONFIG_NLS_CODEPAGE_850,,40,nls_cp850.o))
$(eval $(call KMOD_template,NLS_ISO8859_1,nls-iso8859-1,\
	$(MODULES_DIR)/kernel/fs/nls/nls_iso8859-1.o \
,CONFIG_NLS_ISO8859_1,,40,nls_iso8859-1.o))
$(eval $(call KMOD_template,NLS_ISO8859_15,nls-iso8859-15,\
	$(MODULES_DIR)/kernel/fs/nls/nls_iso8859-15.o \
,CONFIG_NLS_ISO8859_15,,40,nls_iso8859-15.o))
$(eval $(call KMOD_template,NLS_KOI8_R,nls-koi8-r,\
	$(MODULES_DIR)/kernel/fs/nls/nls_koi8-r.o \
,CONFIG_NLS_KOI8_R,,40,nls_koi8-r.o))
$(eval $(call KMOD_template,NLS_UTF8,nls-utf8,\
	$(MODULES_DIR)/kernel/fs/nls/nls_utf8.o \
,CONFIG_NLS_UTF8,,40,nls_utf8.o))
$(eval $(call KMOD_template,CRYPTO,crypto,\
	$(MODULES_DIR)/kernel/crypto/*.o \
,,,20,arc4 \
	sha1))
$(eval $(call KMOD_template,PPP,ppp,\
	$(MODULES_DIR)/kernel/drivers/net/ppp_async.o \
	$(MODULES_DIR)/kernel/drivers/net/ppp_generic.o \
	$(MODULES_DIR)/kernel/drivers/net/slhc.o \
,CONFIG_PPP,,10,slhc ppp_generic ppp_async))
$(eval $(call KMOD_template,MPPE,mppe,\
	$(MODULES_DIR)/kernel/drivers/net/ppp_mppe_mppc.o \
,CONFIG_PPP_MPPE_MPPC,,30,ppp_mppe_mppc))
$(eval $(call KMOD_template,PPPOE,pppoe,\
	$(MODULES_DIR)/kernel/drivers/net/pppoe.o \
	$(MODULES_DIR)/kernel/drivers/net/pppox.o \
,CONFIG_PPPOE))
$(eval $(call KMOD_template,TUN,tun,\
	$(MODULES_DIR)/kernel/drivers/net/tun.o \
,CONFIG_TUN,,20,tun))
$(eval $(call KMOD_template,GRE,gre,\
	$(MODULES_DIR)/kernel/net/ipv4/ip_gre.o \
,CONFIG_NET_IPGRE))
$(eval $(call KMOD_template,SCHED,sched,\
	$(MODULES_DIR)/kernel/net/sched/*.o \
))
$(eval $(call KMOD_template,VIDEODEV,videodev,\
	$(MODULES_DIR)/kernel/drivers/media/video/videodev.o \
))
$(eval $(call KMOD_template,PWC,pwc,\
	$(MODULES_DIR)/kernel/drivers/usb/pwc.o \
))

$(eval $(call KMOD_template,SOFTDOG,softdog,\
	$(MODULES_DIR)/kernel/drivers/char/softdog.o \
))

$(eval $(call KMOD_template,BONDING,bonding,\
	$(MODULES_DIR)/kernel/drivers/net/bonding/bonding.o \
))

$(eval $(call KMOD_template,BLUETOOTH,bluetooth,\
	$(MODULES_DIR)/kernel/net/bluetooth/bluez.o \
	$(MODULES_DIR)/kernel/net/bluetooth/bnep/bnep.o \
	$(MODULES_DIR)/kernel/net/bluetooth/l2cap.o \
	$(MODULES_DIR)/kernel/net/bluetooth/rfcomm/rfcomm.o \
	$(MODULES_DIR)/kernel/net/bluetooth/sco.o \
,CONFIG_BLUEZ,,30,bluez bnep l2cap rfcomm sco))

$(eval $(call KMOD_template,BLUETOOTH_BFUSB,bluetooth-bfusb,\
	$(MODULES_DIR)/kernel/drivers/bluetooth/bfusb.o \
))

$(eval $(call KMOD_template,BLUETOOTH_BLUECARD,bluetooth-bluecard,\
	$(MODULES_DIR)/kernel/drivers/bluetooth/bluecard_cs.o \
))

$(eval $(call KMOD_template,BLUETOOTH_BT3C,bluetooth-bt3c,\
        $(MODULES_DIR)/kernel/drivers/bluetooth/bt3c_cs.o \
))

$(eval $(call KMOD_template,BLUETOOTH_BTUART,bluetooth-btuart,\
        $(MODULES_DIR)/kernel/drivers/bluetooth/btuart_cs.o \
))

$(eval $(call KMOD_template,BLUETOOTH_DTL1,bluetooth-dtl1,\
        $(MODULES_DIR)/kernel/drivers/bluetooth/dtl1_cs.o \
))

$(eval $(call KMOD_template,BLUETOOTH_HCIUART,bluetooth-hciuart,\
        $(MODULES_DIR)/kernel/drivers/bluetooth/hci_uart.o \
))

$(eval $(call KMOD_template,BLUETOOTH_HCIUSB,bluetooth-hciusb,\
        $(MODULES_DIR)/kernel/drivers/bluetooth/hci_usb.o \
))

$(eval $(call KMOD_template,BLUETOOTH_HCIVHCI,bluetooth-hcivhci,\
        $(MODULES_DIR)/kernel/drivers/bluetooth/hci_vhci.o \
))

$(TARGETS): $(PACKAGE_DIR)

$(PACKAGE_DIR):
	mkdir -p $(PACKAGE_DIR)

$(LINUX_DIR)/.unpacked: $(DL_DIR)/$(LINUX_SOURCE)
	-mkdir -p $(LINUX_BUILD_DIR)
	bzcat $(DL_DIR)/$(LINUX_SOURCE) | tar -C $(LINUX_BUILD_DIR) $(TAR_OPTIONS)
	rm -f $(LINUX_DIR)
	ln -s $(LINUX_BUILD_DIR)/linux-$(LINUX_VERSION) $(LINUX_DIR)
	touch $(LINUX_DIR)/.unpacked

$(LINUX_DIR)/.patched: $(LINUX_DIR)/.unpacked
	$(PATCH) $(LINUX_DIR) ./patches/generic
	[ -d ./patches/$(BOARD) ] && $(PATCH) $(LINUX_DIR) ./patches/$(BOARD)
	touch $(LINUX_DIR)/.patched

$(LINUX_DIR)/.configured: $(LINUX_DIR)/.patched
	-cp $(LINUX_KCONFIG) $(LINUX_DIR)/.config
	$(SED) "s,^CROSS_COMPILE.*,CROSS_COMPILE=$(KERNEL_CROSS),g;" \
	  $(LINUX_DIR)/Makefile  \
	  $(LINUX_DIR)/arch/*/Makefile
	$(SED) "s,\-mcpu=,\-mtune=,g;" $(LINUX_DIR)/arch/mips/Makefile
	$(MAKE) -C $(LINUX_DIR) ARCH=$(LINUX_KARCH) oldconfig include/linux/version.h
	touch $(LINUX_DIR)/.configured

$(LINUX_DIR)/.depend_done:  $(LINUX_DIR)/.configured
	$(MAKE) -C $(LINUX_DIR) ARCH=$(LINUX_KARCH) dep
	touch $(LINUX_DIR)/.depend_done

$(LINUX_DIR)/vmlinux: $(LINUX_DIR)/.depend_done
	$(MAKE) -C $(LINUX_DIR) ARCH=$(LINUX_KARCH) PATH=$(TARGET_PATH)

$(LINUX_KERNEL): $(LINUX_DIR)/vmlinux
	$(TARGET_CROSS)objcopy -O binary -R .reginfo -R .note -R .comment -R .mdebug -S $< $@ 
	touch -c $(LINUX_KERNEL)
	
$(LINUX_DIR)/.modules_done: $(LINUX_KERNEL) $(LINUX_IMAGE)
	rm -rf $(LINUX_BUILD_DIR)/modules
	$(MAKE) -C $(LINUX_DIR) ARCH=$(LINUX_KARCH) PATH=$(TARGET_PATH) modules
	$(MAKE) -C $(LINUX_DIR) DEPMOD=true INSTALL_MOD_PATH=$(LINUX_BUILD_DIR)/modules modules_install
	touch $(LINUX_DIR)/.modules_done

$(STAGING_DIR)/include/linux/version.h: $(LINUX_DIR)/.configured
	mkdir -p $(STAGING_DIR)/include
	tar -ch -C $(LINUX_DIR)/include -f - linux | tar -xf - -C $(STAGING_DIR)/include/
	tar -ch -C $(LINUX_DIR)/include -f - asm | tar -xf - -C $(STAGING_DIR)/include/

$(STAMP_DIR)/.linux-compile: $(LINUX_DIR)/.modules_done
	@mkdir -p $(STAMP_DIR)
	@$(MAKE) $(TARGETS)
	ln -sf $(LINUX_BUILD_DIR)/linux-$(LINUX_VERSION) $(LINUX_DIR)
	$(MAKE) -C $(TOPDIR)/target/linux/package \
		$(KPKG_MAKEOPTS) \
		compile
	touch $@

$(TARGET_MODULES_DIR): 
	-mkdir -p $(TARGET_MODULES_DIR)

$(KERNEL_IPKG):
	rm -rf $(KERNEL_IDIR)
	mkdir -p $(KERNEL_IDIR)/etc
	$(SCRIPT_DIR)/make-ipkg-dir.sh $(KERNEL_IDIR) ../control/kernel.control $(LINUX_VERSION)-$(BOARD)-$(PKG_RELEASE) $(ARCH)
	if [ -f ./config/$(BOARD).modules ]; then \
		cp ./config/$(BOARD).modules $(KERNEL_IDIR)/etc/modules; \
	fi
	$(IPKG_BUILD) $(KERNEL_IDIR) $(LINUX_BUILD_DIR)

source: $(DL_DIR)/$(LINUX_SOURCE)
prepare: $(LINUX_DIR)/.configured
compile: $(STAMP_DIR)/.linux-compile

install: compile $(TARGET_MODULES_DIR) $(KERNEL_IPKG)
	rm -rf $(LINUX_BUILD_DIR)/root*
	echo -e 'dest root /\noption offline_root $(LINUX_BUILD_DIR)/root' > $(LINUX_BUILD_DIR)/ipkg.conf
	$(MAKE) -C $(TOPDIR)/package \
		TARGET_DIR="$(LINUX_TARGET_DIR)" \
		IPKG_CONF="$(LINUX_BUILD_DIR)" \
		BOARD="$(BOARD)" \
		install
	$(MAKE) -C $(TOPDIR)/target/linux/package \
		$(KPKG_MAKEOPTS) \
		install
	[ "$(INSTALL_TARGETS)" != "" ] && $(IPKG_KERNEL) install $(INSTALL_TARGETS) || true

mostlyclean:
	rm -f $(STAMP_DIR)/.linux-compile
	rm -f $(LINUX_BUILD_DIR)/linux-$(LINUX_VERSION)/.modules_done
	rm -f $(LINUX_BUILD_DIR)/linux-$(LINUX_VERSION)/.drivers-unpacked
	$(MAKE) -C $(LINUX_BUILD_DIR)/linux-$(LINUX_VERSION) clean
	rm -f $(LINUX_KERNEL) $(LINUX_IMAGE)

rebuild:
	-$(MAKE) mostlyclean
	if [ -f $(LINUX_KERNEL) ]; then \
		$(MAKE) clean; \
	fi
	$(MAKE) compile

clean:
	rm -f $(STAMP_DIR)/.linux-compile
	rm -rf $(LINUX_BUILD_DIR)
	rm -f $(TARGETS)

package/%:
	$(MAKE) -C $(TOPDIR)/target/linux/package \
		$(KPKG_MAKEOPTS) \
		$(patsubst package/%,%,$@)
