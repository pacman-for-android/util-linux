# Maintainer: Tom Gundersen <teg@jklm.no>
# Contributor: judd <jvinet@zeroflux.org>

pkgname=util-linux
pkgver=2.21.2
pkgrel=1
pkgdesc="Miscellaneous system utilities for Linux"
url="http://www.kernel.org/pub/linux/utils/util-linux/"
arch=('i686' 'x86_64')
groups=('base')
depends=('udev' 'pam')
conflicts=('util-linux-ng')
provides=("util-linux-ng=${pkgver}")
license=('GPL2')
options=('!libtool')
source=(ftp://ftp.kernel.org/pub/linux/utils/${pkgname}/v2.21/${pkgname}-${pkgver}.tar.xz)

build() {
  cd "${srcdir}/${pkgname}-${pkgver}"

  # hardware clock
  sed -e 's%etc/adjtime%var/lib/hwclock/adjtime%' -i include/pathnames.h

  ./configure --prefix=/usr \
              --libdir=/usr/lib \
              --enable-fs-paths-extra=/usr/bin:/usr/sbin \
              --enable-write \
              --enable-raw \
              --disable-wall \
              --enable-new-mount

  make
}

check() {
	make -C "$pkgname-$pkgver" check
}

package() {
  cd "${srcdir}/${pkgname}-${pkgver}"

  make DESTDIR="${pkgdir}" install

  cd "${pkgdir}"

  install -dm755 var/lib/hwclock

  # broken tool, going away in next major release, so just remove it now
  rm "${pkgdir}"/usr/{bin/chkdupexe,share/man/man1/chkdupexe.1}

  # delete stray empty dir, fixed upstream
  rm -r usr/share/man/ru
}
md5sums=('54ba880f1d66782c2287ee2c898520e9')
