# Maintainer: judd <jvinet@zeroflux.org>
pkgname=util-linux
pkgver=2.19
pkgrel=2
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
install=util-linux.install
md5sums=('590ca71aad0b254e2631d84401f28255')

build() {
  cd "${srcdir}/${pkgname}-${pkgver}"
  # hardware clock
  sed -e 's%etc/adjtime%var/lib/hwclock/adjtime%' -i hwclock/hwclock.c
  autoreconf
  automake
  ./configure --enable-arch --enable-write --enable-raw --disable-wall --enable-rdev --enable-partx --enable-libmount-mount
  make HAVE_SLN=yes ADD_RAW=yes
}

package() {
  cd "${srcdir}/${pkgname}-${pkgver}"
  mkdir -p "${pkgdir}/var/lib/hwclock"
  make HAVE_SLN=yes ADD_RAW=yes DESTDIR="${pkgdir}" install
  # remove files
  rm -f "${pkgdir}/bin/kill"
  rm -f "${pkgdir}/usr/share/man/man1/kill.1"
  rm -f "${pkgdir}/usr/share/man/man5/nfs.5"
  rm -f "${pkgdir}/usr/share/info/dir"
}
