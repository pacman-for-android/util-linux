# Maintainer: Tom Gundersen <teg@jklm.no>
# Contributor: judd <jvinet@zeroflux.org>

pkgname=util-linux
pkgver=2.21.2
pkgrel=2
pkgdesc="Miscellaneous system utilities for Linux"
url="http://www.kernel.org/pub/linux/utils/util-linux/"
arch=('i686' 'x86_64')
groups=('base')
depends=('udev' 'pam')
conflicts=('util-linux-ng')
provides=("util-linux-ng=${pkgver}")
license=('GPL2')
options=('!libtool')
source=(ftp://ftp.kernel.org/pub/linux/utils/${pkgname}/v2.21/${pkgname}-${pkgver}.tar.xz
        pam-login
        pam-common)
md5sums=('54ba880f1d66782c2287ee2c898520e9'
         '6aca1c10dad9c0feea2af6497de6ca82'
         'a31374fef2cba0ca34dfc7078e2969e4')

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
              --enable-new-mount \
              --enable-login-utils

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

  # setuid chfn and chsh
  chmod 4755 "$pkgdir"/usr/bin/ch{sh,fn}

  # install PAM files for login-utils
  install -Dm644 "$srcdir/pam-common" "$pkgdir/etc/pam.d/chfn"
  install -m644 "$srcdir/pam-common" "$pkgdir/etc/pam.d/chsh"
  install -m644 "$srcdir/pam-login" "$pkgdir/etc/pam.d/login"
}
