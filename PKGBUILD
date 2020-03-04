# Maintainer: Levente Polyak <anthraxx[at]archlinux[dot]org>
# Contributor: Timothy Redaelli <timothy.redaelli@gmail.com>
# Contributor: Guillaume ALAUX <guillaume@archlinux.org>
# Contributor: Florian Pritz <bluewind at jabber dot ccc dot de>
# Contributor: Peter Wu <peter@lekensteyn.nl>

pkgbase=wireshark
pkgname=('wireshark-cli' 'wireshark-qt')
pkgver=3.2.2
pkgrel=1
pkgdesc='Network traffic and protocol analyzer/sniffer'
url='https://www.wireshark.org/'
arch=('x86_64')
license=('GPL2')
makedepends=('cmake' 'ninja' 'c-ares' 'libmaxminddb' 'qt5-tools' 'qt5-svg'
             'qt5-multimedia' 'krb5' 'libpcap' 'libssh' 'libxml2' 'libnghttp2'
             'snappy' 'lz4' 'spandsp' 'gnutls' 'lua52' 'python' 'libcap'
             'glib2' 'libgcrypt' 'sbc' 'bcg729' 'desktop-file-utils'
             'hicolor-icon-theme')
options=('!emptydirs')
source=(https://www.wireshark.org/download/src/${pkgbase}-${pkgver}.tar.xz
        wireshark.sysusers)
sha512sums=('ab0a312369655f10926dc4245c2e11b988e0a1e49ffefe22d8fdad31e4f310b0b141bac080034511f4ffb1c6cace7e6e595c59a8f65d67909fdcb1c16e87b651'
            '3956c1226e64f0ce4df463f80b55b15eed06ecd9b8703b3e8309d4236a6e1ca84e43007336f3987bc862d8a5e7cfcaaf6653125d2a34999a0f1357c52e7c4990')

prepare() {
  cd ${pkgbase}-${pkgver}
  sed 's| Rev Unknown from unknown||' -i tools/make-version.pl
}

build() {
  cd ${pkgbase}-${pkgver}
  cmake . -GNinja \
    -DCMAKE_BUILD_TYPE=Release \
    -DCMAKE_INSTALL_PREFIX=/usr \
    -DCMAKE_INSTALL_LIBDIR=lib \
    -DCMAKE_INSTALL_RPATH= \
    -DCMAKE_SKIP_RPATH=ON
  ninja
}

package_wireshark-cli() {
  pkgdesc+=' - CLI tools and data files'
  depends=('c-ares' 'libmaxminddb' 'krb5' 'libgcrypt' 'libcap' 'libpcap'
           'gnutls' 'glib2' 'lua52' 'libssh' 'libxml2' 'libnghttp2' 'snappy'
           'lz4' 'spandsp' 'sbc' 'bcg729')
  install=wireshark.install
  conflicts=(wireshark)
  provides=(libwireshark.so libwiretap.so libwsutil.so)

  cd ${pkgbase}-${pkgver}
  DESTDIR="${pkgdir}" ninja install

  # wireshark uid group is 150
  install -Dm 644 "${srcdir}/wireshark.sysusers" "${pkgdir}/usr/lib/sysusers.d/wireshark.conf"
  chgrp 150 "${pkgdir}/usr/bin/dumpcap"
  chmod 754 "${pkgdir}/usr/bin/dumpcap"

  cd "${pkgdir}"
  rm -r usr/share/mime \
    usr/share/icons \
    usr/share/man/man1/wireshark.1 \
    usr/bin/wireshark \
    usr/share/applications/wireshark.desktop \
    usr/share/appdata/wireshark.appdata.xml
}

package_wireshark-qt() {
  pkgdesc+=' - Qt GUI'
  depends=('desktop-file-utils' 'qt5-multimedia' 'qt5-svg' 'wireshark-cli'
           'shared-mime-info' 'hicolor-icon-theme' 'xdg-utils')
  replaces=(wireshark wireshark-gtk wireshark-common)
  conflicts=(wireshark wireshark-gtk wireshark-common)

  cd ${pkgbase}-${pkgver}
  install -d "${srcdir}/staging"
  DESTDIR="${srcdir}/staging" ninja install

  install -Dm 755 run/wireshark -t "${pkgdir}/usr/bin"
  install -Dm 644 wireshark.desktop -t "${pkgdir}/usr/share/applications"
  install -Dm 644 doc/wireshark.1 -t "${pkgdir}/usr/share/man/man1"
  install -Dm 644 wireshark.appdata.xml -t "${pkgdir}/usr/share/appdata"
  install -Dm 644 wireshark-mime-package.xml "${pkgdir}/usr/share/mime/packages/wireshark.xml"
  mv "${srcdir}/staging/usr/share/icons" "${pkgdir}/usr/share/icons"
}

# vim: ts=2 sw=2 et:
