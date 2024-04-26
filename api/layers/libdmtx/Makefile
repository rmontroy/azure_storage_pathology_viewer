build-libdmtx:
	curl -L https://github.com/dmtx/libdmtx/archive/refs/tags/v0.7.7.tar.gz -o libdmtx.tar.gz
	tar xf libdmtx.tar.gz
	cd libdmtx-0.7.7 \
	&& ./autogen.sh \
	&& ./configure \
	&& make \
	&& make install
	mkdir -p "$(ARTIFACTS_DIR)/lib"
	cp /usr/local/lib/libdmtx.so.0 "$(ARTIFACTS_DIR)/lib"
	cp /usr/local/lib/libdmtx.so.0 "$(ARTIFACTS_DIR)/lib/libdmtx.so"
