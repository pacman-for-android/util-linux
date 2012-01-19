# Maintainer: Tom Gundersen <teg@jklm.no>
# Contributor: judd <jvinet@zeroflux.org>

pkgname=util-linux
pkgver=2.21
pkgrel=0.1
pkgdesc="Miscellaneous system utilities for Linux"
url="http://userweb.kernel.org/~kzak/util-linux-ng/"
arch=('i686' 'x86_64')
groups=('base')
depends=('udev')
conflicts=('util-linux-ng')
provides=("util-linux-ng=${pkgver}")
license=('GPL2')
options=('!libtool')
source=(ftp://ftp.kernel.org/pub/linux/utils/${pkgname}/v${pkgver}/${pkgname}-${pkgver}-rc1.tar.xz)
optdepends=('perl: for chkdupexe support')

build() {
  cd "${srcdir}/${pkgname}-${pkgver}-rc1"

  # hardware clock
  sed -e 's%etc/adjtime%var/lib/hwclock/adjtime%' -i include/pathnames.h

  ./configure --enable-write\
              --enable-raw\
              --disable-wall\
              --enable-new-mount

  make
}

package() {
  cd "${srcdir}/${pkgname}-${pkgver}-rc1"

  install -dm755 "${pkgdir}/var/lib/hwclock"

  make DESTDIR="${pkgdir}" install
}
md5sums=('bbfefcb74a47d517a37d0668c38de2d4')
