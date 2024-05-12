# Maintainer: DawfukFR <dawfukfr@gmail.com>
# Contributor: Antonio Rojas <arojas@archlinux.org>
# Contributor: Felix Yan <felixonmars@archlinux.org>
# Contributor: Andrea Scarpino <andrea@archlinux.org>
# Contributor: João Figueiredo <jf.mundox@gmail.com>

pkgbase=qt6-base
pkgname=qt6-base-git
pkgver=6.7.0_r67329.g95bb6bc916
pkgrel=1
arch=($CARCH)
url='https://www.qt.io'
license=(GPL3 LGPL3 FDL custom)
pkgdesc='A cross-platform application and UI framework'
depends=(libjpeg-turbo xcb-util-keysyms xcb-util-renderutil libgl fontconfig xdg-utils
         shared-mime-info xcb-util-wm libxrender libxi sqlite xcb-util-image mesa
         tslib libinput libxkbcommon-x11 libproxy libcups double-conversion md4c brotli)
makedepends=(cmake libfbclient mariadb-libs unixodbc postgresql-libs alsa-lib gst-plugins-base-libs
             gtk3 libpulse cups freetds vulkan-headers git)
optdepends=('postgresql-libs: PostgreSQL driver'
            'mariadb-libs: MariaDB driver'
            'unixodbc: ODBC driver'
            'libfbclient: Firebird/iBase driver'
            'freetds: MS SQL driver'
            'gtk3: GTK platform plugin'
            'perl: for fixqt4headers and syncqt')
conflicts=(${pkgname%-git})
provides=(${pkgname%-git})
groups=(qt6)
source=(git+https://github.com/qt/qtbase
        qt6-base-cflags.patch
        qt6-base-nostrip.patch
        fix-wrong-cpp-if.patch::https://code.qt.io/cgit/qt/qtbase.git/patch/?id=68102202)
sha256sums=('SKIP'
            '287533e421acc3be27302526f15f21588be48d37b68fff19804360e0934d6e75'
            '4b93f6a79039e676a56f9d6990a324a64a36f143916065973ded89adc621e094'
            '81c4821fb1c258603474771a267d450aa8b5d1d298443bc04620d70719c7eab7')

pkgver() {
  cd qtbase
  _ver="$(git describe | sed 's/^v//;s/-.*//')"
  echo "${_ver}_r$(git rev-list --count HEAD).g$(git rev-parse --short HEAD)"
}

prepare() {
  cd qtbase
  # patching
  for patch in "${source[@]}"; do
      if [[ $patch == *.patch ]]; then
      msg2 "Applying $patch"
      patch --no-backup-if-mismatch -Np1 -i "$srcdir/$patch"
      fi
  done
  git cherry-pick -n 7c4e1357e49baebdd2d20710fccb5604cbb36c0d # CVE-2024-33861
  git cherry-pick -n a8ef8ea55014546e0e835cd0eacf694919702a11 # https://bugreports.qt.io/browse/QTBUG-124386
  git cherry-pick -n 062f701a11d2c46660f5c5edd73f245477918a47 # Fix dependencies in pc files
  git cherry-pick -n 5ee9da89af7efe31ac45858bf1eb04e5155a3b50 # Fix dependencies in pc files
}

build() {
  cmake -B build -S qtbase -G Ninja \
    -DCMAKE_BUILD_TYPE=None \
    -DCMAKE_INSTALL_PREFIX=/usr \
    -DINSTALL_BINDIR=lib/qt6/bin \
    -DINSTALL_PUBLICBINDIR=usr/bin \
    -DINSTALL_LIBEXECDIR=lib/qt6 \
    -DINSTALL_DOCDIR=share/doc/qt6 \
    -DINSTALL_ARCHDATADIR=lib/qt6 \
    -DINSTALL_DATADIR=share/qt6 \
    -DINSTALL_INCLUDEDIR=include/qt6 \
    -DINSTALL_MKSPECSDIR=lib/qt6/mkspecs \
    -DINSTALL_EXAMPLESDIR=share/doc/qt6/examples \
    -DFEATURE_journald=ON \
    -DFEATURE_libproxy=ON \
    -DFEATURE_openssl_linked=ON \
    -DFEATURE_system_sqlite=ON \
    -DFEATURE_system_xcb_xinput=ON \
    -DFEATURE_no_direct_extern_access=ON \
    -DCMAKE_INTERPROCEDURAL_OPTIMIZATION=ON \
    -DCMAKE_MESSAGE_LOG_LEVEL=STATUS
  cmake --build build
}

package() {
  DESTDIR="$pkgdir" cmake --install build

  install -Dm644 qtbase/LICENSES/* -t "$pkgdir"/usr/share/licenses/$pkgbase

  # Symlinks for backwards compatibility
  mkdir -p "$pkgdir"/usr/bin
  for _b in uic rcc qmake; do
    ln -rs "$pkgdir"/usr/lib/qt6/bin/$_b "$pkgdir"/usr/bin/$_b-qt6
  done
}
