mode ?= auto

.PHONY: predict
predict:
	sbot predict version

.PHONY: release
release:
	sbot update version
	sbot release version -m $(mode)
	sbot push version
