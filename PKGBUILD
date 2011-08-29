# Maintainer: Tom Gundersen <teg@jklm.no>
# Contributor: judd <jvinet@zeroflux.org>

pkgname=util-linux
pkgver=2.20
pkgrel=1
pkgdesc="Miscellaneous system utilities for Linux"
url="http://userweb.kernel.org/~kzak/util-linux-ng/"
arch=('i686' 'x86_64')
groups=('base')
depends=('filesystem')
replaces=('linux32' 'util-linux-ng')
conflicts=('linux32' 'util-linux-ng' 'e2fsprogs<1.41.8-2')
provides=('linux32' "util-linux-ng=${pkgver}")
license=('GPL2')
options=('!libtool')
source=(ftp://ftp.kernel.org/pub/linux/utils/${pkgname}/v${pkgver}/${pkgname}-${pkgver}.tar.bz2)
optdepends=('perl: for chkdupexe support')

build() {
  cd "${srcdir}/${pkgname}-${pkgver}"

  # hardware clock
  sed -e 's%etc/adjtime%var/lib/hwclock/adjtime%' -i include/pathnames.h

  ./configure --enable-arch\
              --enable-write\
              --enable-raw\
              --disable-wall\
              --enable-partx

  make
}

package() {
  cd "${srcdir}/${pkgname}-${pkgver}"

  install -dm755 "${pkgdir}/var/lib/hwclock"

  make DESTDIR="${pkgdir}" install
}
md5sums=('4dcacdbdafa116635e52b977d9d0e879')
