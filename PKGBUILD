# Maintainer: Tom Gundersen <teg@jklm.no>
# Contributor: judd <jvinet@zeroflux.org>

pkgname=util-linux
pkgver=2.21.2
pkgrel=4
pkgdesc="Miscellaneous system utilities for Linux"
url="http://www.kernel.org/pub/linux/utils/util-linux/"
arch=('i686' 'x86_64')
groups=('base')
depends=('pam')
conflicts=('util-linux-ng')
provides=("util-linux-ng=${pkgver}")
license=('GPL2')
options=('!libtool')
source=(ftp://ftp.kernel.org/pub/linux/utils/${pkgname}/v2.21/${pkgname}-${pkgver}.tar.xz
        pam-login
        pam-common)
backup=(etc/pam.d/chfn etc/pam.d/chsh etc/pam.d/login)

build() {
  cd "${srcdir}/${pkgname}-${pkgver}"

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
md5sums=('54ba880f1d66782c2287ee2c898520e9'
         '4368b3f98abd8a32662e094c54e7f9b1'
         'a31374fef2cba0ca34dfc7078e2969e4')
