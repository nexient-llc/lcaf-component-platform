include $(COMPONENTS_DIR)/**/tasks/**/Makefile

.PHONY: check
check:
	$(MAKE) lint
	$(MAKE) test

.PHONY: lint
lint::
# Commands that have no potential to execute untrusted code may be run as part of a `lint` target
# in your component. When adding a `lint` target to your Makefile, ensure that you follow it with 
# a double-colon like above, so that your target adds to the collection of `lint` commands rather
# than overriding it for other components.

# This target serves only to ensure that at least one `lint` target exists and should noop.
	@true

.PHONY: test
test::
# Commands that have a potential to execute untrusted code may be run as part of the `test` target.
# When adding a `test` target to your Makefile, ensure that you follow it with a double-colon like 
# above, so that your target adds to the collection of `test` commands rather than overriding it 
# for other components.

# This target serves only to ensure that at least one `test` target exists and should noop.
	@true
