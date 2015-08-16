# Maintainer: xantares <xantares09 at hotmail dot com>

pkgname=mingw-w64-qsint
pkgver=0.2.1
pkgrel=3
pkgdesc="Opensource collection of Qt widgets (mingw-w64)"
license=('GPL')
arch=(any)
url="http://sintegrial.com/qsint/pages/en/"
depends=('mingw-w64-crt' 'mingw-w64-qt4')
makedepends=('mingw-w64-gcc' 'mingw-w64-qt4')
options=('!buildflags' '!strip' 'staticlibs')
source=("http://sintegrial.com/qsint/files/qsint-${pkgver}-src.7z")
md5sums=('8be8b6f882f4ebf2f556586874415287')

_architectures="i686-w64-mingw32 x86_64-w64-mingw32"      

build() {
  unset CFLAGS CXXFLAGS LDFLAGS
  cd "$srcdir/qsint-${pkgver}-src"
  sed -i "s|staticlib|shared|g" src/src.pri
  echo "INSTALLS += headers" >> qsint.pro
  sed -i "s|src \\\\|src|g" qsint.pro
  sed -i "s|designer|#designer|g" qsint.pro
  sed -i "s|examples|#examples|g" qsint.pro
  echo "headers.path = /usr/include/qsint" >> qsint.pro
  for _arch in ${_architectures}; do
    export QTDIR=/usr/${_arch}
    mkdir -p "build-${_arch}" && pushd "build-${_arch}"
    ${QTDIR}/bin/qmake ../qsint.pro
    make src/Makefile
    make -C src Core/Makefile
    make -C src Charts/Makefile
    sed -i "s|QSCore.a|QSCore.dll.a|g" src/Core/Makefile.Release
    sed -i "s|QSCharts.a|QSCharts.dll.a|g" src/Charts/Makefile.Release
    make
    popd
  done
}


package() {
  for _arch in ${_architectures}; do
    cd "$srcdir/qsint-${pkgver}-src/build-${_arch}"
    make INSTALL_ROOT="$pkgdir" install
    install -d "$pkgdir"/usr/${_arch}/lib
    install -d "$pkgdir"/usr/${_arch}/bin
    install -d "$pkgdir"/usr/${_arch}/include/qsint/{Core,Charts}
    install -m644 lib/*.dll "$pkgdir"/usr/${_arch}/bin
    install -m644 lib/*.a "$pkgdir"/usr/${_arch}/lib
    install -m644 $srcdir/qsint-${pkgver}-src/src/Core/*.h "$pkgdir"/usr/${_arch}/include/qsint/Core
    install -m644 $srcdir/qsint-${pkgver}-src/src/Charts/*.h "$pkgdir"/usr/${_arch}/include/qsint/Charts
    ${_arch}-strip --strip-unneeded "$pkgdir"/usr/${_arch}/bin/*.dll
    ${_arch}-strip -g "$pkgdir"/usr/${_arch}/lib/*.a
  done
}
