# Maintainer: Tom Gundersen <teg@jklm.no>
# Contributor: judd <jvinet@zeroflux.org>

pkgname=util-linux
pkgver=2.21
pkgrel=1
pkgdesc="Miscellaneous system utilities for Linux"
url="http://www.kernel.org/pub/linux/utils/util-linux/"
arch=('i686' 'x86_64')
groups=('base')
depends=('udev')
conflicts=('util-linux-ng')
provides=("util-linux-ng=${pkgver}")
license=('GPL2')
options=('!libtool')
source=(ftp://ftp.kernel.org/pub/linux/utils/${pkgname}/v${pkgver}/${pkgname}-${pkgver}.tar.xz)
optdepends=('perl: for chkdupexe support')

build() {
  cd "${srcdir}/${pkgname}-${pkgver}"

  # hardware clock
  sed -e 's%etc/adjtime%var/lib/hwclock/adjtime%' -i include/pathnames.h

  ./configure --prefix=/usr \
              --libdir=/usr/lib \
              --enable-write \
              --enable-raw \
              --disable-wall \
              --enable-new-mount

  make
}

package() {
  cd "${srcdir}/${pkgname}-${pkgver}"

  make DESTDIR="${pkgdir}" install

  cd "${pkgdir}"

  install -dm755 var/lib/hwclock

  # an empty stray dir
  rm -r usr/share/man/ru
}
md5sums=('208aa058f4117759d2939d1be7d662fc')
