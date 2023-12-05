obj-m := hpsa.o

ifndef KERNELRELEASE
KRELEASE := $(shell uname -r)
else
KRELEASE := $(KERNELRELEASE)
endif

KRELEASE_ROOT := $(word 1,$(subst ., ,$(KRELEASE))).$(word 2,$(subst ., ,$(KRELEASE)))
KRELEASE_TAG := $(KRELEASE_ROOT).y

KDIR := /usr/lib/modules/$(KRELEASE)/build
PWD := $(shell pwd)


default:
	$(MAKE) -C $(KDIR) M=$(PWD) modules

clean:
	$(MAKE) -C $(KDIR) M=$(PWD) clean
	rm -f .source.stamp hpsa.c hpsa.h hpsa_cmd.h


ifneq (,$(M))
PATCHES := $(wildcard $(M)/$(KRELEASE_ROOT)*/*.patch)

$(M)/hpsa.h $(M)/hpsa.c $(M)/hpsa_cmd.h: $(M)/.source.stamp

$(M)/.source.stamp:
	for f in hpsa.c hpsa.h hpsa_cmd.h ; do \
		wget "https://git.kernel.org/pub/scm/linux/kernel/git/stable/linux.git/plain/drivers/scsi/$${f}?h=linux-$(KRELEASE_TAG)" -O $(M)/$${f} ; \
	done
	for PATCH in $(PATCHES) ; do \
		(cd $(M) && patch --no-backup-if-mismatch -Np3 -i "$${PATCH}") ; \
	done
	sed -re 's/^MODULE_VERSION\([^)]*/& "-1"/g' -i $(M)/hpsa.c
	touch $@
endif
