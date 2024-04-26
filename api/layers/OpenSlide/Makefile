build-OpenSlide:
	# get Openslide deps
	dnf install -y glib2-devel libjpeg-turbo-devel openjpeg2-devel libtiff-devel libcurl-devel-8.3.0-1.amzn2023.0.2
	mkdir -p "$(ARTIFACTS_DIR)/lib"
	cp /usr/lib64/libopenjp2.so.7 "$(ARTIFACTS_DIR)/lib"
	cp /usr/lib64/libtiff.so.5 "$(ARTIFACTS_DIR)/lib"
	cp /usr/lib64/libwebp.so.7 "$(ARTIFACTS_DIR)/lib"
	cp /usr/lib64/libpng16.so.16 "$(ARTIFACTS_DIR)/lib"
	cp /usr/lib64/libjpeg.so.62 "$(ARTIFACTS_DIR)/lib"
	cp /usr/lib64/libglib-2.0.so.0 "$(ARTIFACTS_DIR)/lib"
	cp /usr/lib64/libjbig.so.2.1 "$(ARTIFACTS_DIR)/lib"

	dnf install -y meson ninja-build cmake
	# Build cairo graphics as a link-time optimized static library
	curl https://cairographics.org/releases/cairo-1.18.0.tar.xz -o cairo.tar.xz
	mkdir -p cairo/build && tar xf cairo.tar.xz -C cairo --strip-components=1
	meson setup cairo/build cairo --buildtype=release --default-library=static \
		--auto-features=disabled -Db_lto=true -Dc_args="-Wno-inline -w"
	# These next two text replacements are to help LTO eliminate unneeded code
	sed -i.bak 's/__attribute__((__visibility__("default")))/__attribute__((__visibility__("hidden")))/' cairo/src/cairo.h
	sed -i.bak 's/__attribute__ ((visibility("default")))/__attribute__((visibility("hidden")))/' cairo/subprojects/pixman/pixman/pixman-compiler.h
	ninja -C cairo/build
	ninja -C cairo/build install

	# Build AWS SDK for C++ ourselves, until someone makes it smaller. Might as well link it
	# statically if we're building it anyway (link-time optimized and fewer symbol relocations).
	git clone --recurse-submodules --depth=1 --branch 1.11.314 https://github.com/aws/aws-sdk-cpp
	cd aws-sdk-cpp && git apply ../aws-sdk-cpp.patch
	dnf install -y openssl-devel
	cmake -S aws-sdk-cpp -B aws-sdk-cpp/build -DCMAKE_BUILD_TYPE=Release -DENABLE_TESTING=OFF  \
		-DBUILD_ONLY="s3" -DBUILD_SHARED_LIBS=OFF -DENABLE_UNITY_BUILD=OFF -DENABLE_VIRTUAL_OPERATIONS=OFF \
		-DCMAKE_INTERPROCEDURAL_OPTIMIZATION=ON -DAWS_ENABLE_LTO=ON -DS2N_LTO=ON \
		-DCMAKE_C_FLAGS="-fvisibility=hidden" -DCMAKE_CXX_FLAGS="-fvisibility=hidden -fvisibility-inlines-hidden"
	cmake --build aws-sdk-cpp/build --config=Release
	cmake --install aws-sdk-cpp/build --config=Release

	# Build TIFFClientOpen-to-S3 adapter as a link-time optimized static library
	cmake -S tiffS3 -B tiffS3/build -DCMAKE_BUILD_TYPE=Release -DENABLE_TESTING=OFF  \
		-DCMAKE_INTERPROCEDURAL_OPTIMIZATION=ON
	cmake --build tiffS3/build --config=Release
	cmake --install tiffS3/build --config=Release

	# Build Openslide as a link-time optimized shared library
	git clone --depth=1 https://github.com/rmontroy/openslide.git
	meson setup openslide/build openslide --buildtype=release --auto-features=disabled \
		-Db_lto=true
	ninja -C openslide/build
	ninja -C openslide/build install
	cp /usr/local/lib64/libopenslide.so.1 "$(ARTIFACTS_DIR)/lib"

	pip install openslide-python --no-deps -t "$(ARTIFACTS_DIR)/python"