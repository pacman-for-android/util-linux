# Maintainer: Tom Gundersen <teg@jklm.no>
# Contributor: judd <jvinet@zeroflux.org>

pkgname=util-linux
pkgver=2.20rc1
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
source=(ftp://ftp.kernel.org/pub/linux/utils/${pkgname}/v2.20/${pkgname}-2.20-rc1.tar.bz2)
optdepends=('perl: for chkdupexe support')

build() {
  cd "${srcdir}/${pkgname}-2.20-rc1"

  # hardware clock
  sed -e 's%etc/adjtime%var/lib/hwclock/adjtime%' -i include/pathnames.h

  ./configure --enable-arch\
              --enable-write\
              --enable-raw\
              --disable-wall\
              --enable-partx\
              --enable-libmount-mount

  make
}

package() {
  cd "${srcdir}/${pkgname}-2.20-rc1"

  install -dm755 "${pkgdir}/var/lib/hwclock"

  make DESTDIR="${pkgdir}" install
}
sha256sums=('34edb87c1ae46a54921ea73dc9b07d010d0611cf79ff982f20bfc6841bae2fcc')
