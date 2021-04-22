# Repository for pre-built OpenSSL binaries
We use this repository to have instant access to OpenSSL pre-built binaries for
`x86_64` and `arm64` architectures. It's important for us, because we work with
macOS, iOS/iPadOS, tvOS which use both `x86_64` and `arm64`.

## How to build OpenSSL for `x86_64` + `arm64`?
```shell
export OPENSSL_VERSION=1.1.1k
export MACOSX_DEPLOYMENT_TARGET=10.13

curl --output openssl.tar.gz "https://www.openssl.org/source/openssl-$OPENSSL_VERSION.tar.gz"
tar -xvzf openssl.tar.gz

mv openssl-$OPENSSL_VERSION openssl-$OPENSSL_VERSION-x86_64
cp -r openssl-$OPENSSL_VERSION-x86_64 openssl-$OPENSSL_VERSION-arm64

# Build x86_64

cd openssl-$OPENSSL_VERSION-x86_64
./Configure darwin64-x86_64-cc
make
cd ..

# Build arm64

cd openssl-$OPENSSL_VERSION-arm64

./Configure darwin64-arm64-cc
make
cd ..

# LIPO binaries
mkdir openssl-mac
lipo -create openssl-$OPENSSL_VERSION-arm64/libcrypto.a openssl-$OPENSSL_VERSION-x86_64/libcrypto.a -output openssl-mac/libcrypto.a
lipo -create openssl-$OPENSSL_VERSION-arm64/libssl.a openssl-$OPENSSL_VERSION-x86_64/libssl.a -output openssl-mac/libssl.a

# iPhoneOS SDK (seperate)
export CROSS_TOP=/Applications/Xcode.app/Contents/Developer/Platforms/iPhoneOS.platform/Developer
export CROSS_SDK=iPhoneOS.sdk
export BUILD_TOOLS=/Applications/Xcode.app/Contents/Developer
export CC="${BUILD_TOOLS}/usr/bin/gcc -arch arm64"
./Configure iphoneos-cross
make
# Run lipo for libssl.a libcrypto.a
```
