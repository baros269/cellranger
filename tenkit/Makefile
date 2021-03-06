#
# Copyright (c) 2016 10X Genomics, Inc. All rights reserved.
#

LIBPY=lib/python
LIBSSW=$(LIBPY)/striped_smith_waterman
#LIBSSW_INIT_PY=$(LIBSSW)/__init__.py
LIBSSW_BUILD_DIR=$(LIBSSW)
LIBSSW_BUILD=$(LIBSSW_BUILD_DIR)/libssw.so
VERSION=$(shell git describe --tags --always --dirty)
GOBINS=barcodeqc godemux q30count

export GOPATH=$(shell pwd)/lib/go

GO_VERSION=$(strip $(shell go version | sed 's/.*go\([0-9]*\.[0-9]*\).*/\1/'))

# Older versions of Go use the "-X foo bar" syntax.  Newer versions either warn
# or error on that syntax, and use "-X foo=bar" instead.
LINK_SEPERATOR=$(if $(filter 1.5, $(word 1, $(sort 1.5 $(GO_VERSION)))),=, )

.PHONY: test

all: $(LIBSSW_BUILD) $(GOBINS)

clean: libssw-clean
	rm -rf $(GOPATH)/bin
	rm -rf $(GOPATH)/pkg
	rm -rf lib/bin

test:
	MROPATH=$(PWD)/mro \
		PATH=$(PATH):$(PWD)/bin \
		PYTHONPATH=$(PYTHONPATH):$(PWD)/$(LIBPY) \
		nosetests --with-xunit --with-coverage --cover-erase --cover-html --cover-xml --cover-package=stages,tenkit mro/stages/* $(LIBPY)

$(LIBSSW_BUILD):
	$(MAKE) -C $(LIBSSW_BUILD_DIR) libssw.so

libssw-clean:
	$(MAKE) -C $(LIBSSW_BUILD_DIR) clean

lib/bin:
	mkdir -p lib/bin

$(GOBINS): lib/bin
	go install -ldflags "-X $@.__VERSION__$(LINK_SEPERATOR)'$(VERSION)'" $@
	cp lib/go/bin/$@ lib/bin



