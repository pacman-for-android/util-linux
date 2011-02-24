# Maintainer: judd <jvinet@zeroflux.org>
pkgname=util-linux
pkgver=2.19
pkgrel=4
pkgdesc="Miscellaneous system utilities for Linux"
url="http://userweb.kernel.org/~kzak/util-linux-ng/"
arch=('i686' 'x86_64')
groups=('base')
depends=('bash' 'ncurses>=5.7' 'zlib' 'filesystem')
replaces=('linux32' 'util-linux-ng')
conflicts=('linux32' 'util-linux-ng' 'e2fsprogs<1.41.8-2')
provides=('linux32' "util-linux-ng=${pkgver}")
license=('GPL2')
options=('!libtool')
source=(ftp://ftp.kernel.org/pub/linux/utils/${pkgname}/v2.19/${pkgname}-${pkgver}.tar.bz2)
optdepends=('perl: for chkdupexe support')
md5sums=('590ca71aad0b254e2631d84401f28255')

build() {
  cd "${srcdir}/${pkgname}-${pkgver}"
  # hardware clock
  sed -e 's%etc/adjtime%var/lib/hwclock/adjtime%' -i hwclock/hwclock.c
  ./configure --enable-arch --enable-write --enable-raw --disable-wall --enable-partx
  make
}

package() {
  cd "${srcdir}/${pkgname}-${pkgver}"
  mkdir -p "${pkgdir}/var/lib/hwclock"
  make DESTDIR="${pkgdir}" install
}
