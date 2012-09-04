# Maintainer: Tom Gundersen <teg@jklm.no>
# Contributor: judd <jvinet@zeroflux.org>

pkgname=util-linux
pkgver=2.22
pkgrel=1
pkgdesc="Miscellaneous system utilities for Linux"
url="http://www.kernel.org/pub/linux/utils/util-linux/"
arch=('i686' 'x86_64')
groups=('base')
depends=('pam')
makedepends=('bc') # for check() only
conflicts=('util-linux-ng' 'eject')
provides=("util-linux-ng=${pkgver}" 'eject')
license=('GPL2')
options=('!libtool')
source=(ftp://ftp.kernel.org/pub/linux/utils/${pkgname}/v2.22/${pkgname}-${pkgver}.tar.xz
        pam-login
        pam-common
	pam-su)
backup=(etc/pam.d/chfn etc/pam.d/chsh etc/pam.d/login)
install=util-linux.install

build() {
  cd "${srcdir}/${pkgname}-${pkgver}"

  ./configure --prefix=/usr \
              --libdir=/usr/lib \
              --enable-fs-paths-extra=/usr/bin:/usr/sbin \
              --enable-raw \
              --enable-vipw \
              --enable-newgrp \
              --enable-chfn-chsh \
              --enable-write \
              --enable-mesg \
              --enable-socket-activation

#              --enable-reset \ # part of ncurses
#              --enable-last \ # not part of any package
#              --enable-line \ # not compat

  make
}

check() {
  make -C "$pkgname-$pkgver" check
}

package() {
  cd "${srcdir}/${pkgname}-${pkgver}"

  make DESTDIR="${pkgdir}" install

  cd "${pkgdir}"

  # setuid chfn and chsh
  chmod 4755 "$pkgdir"/usr/bin/{newgrp,ch{sh,fn}}

  # install PAM files for login-utils
  install -Dm644 "$srcdir/pam-common" "$pkgdir/etc/pam.d/chfn"
  install -m644 "$srcdir/pam-common" "$pkgdir/etc/pam.d/chsh"
  install -m644 "$srcdir/pam-login" "$pkgdir/etc/pam.d/login"
  install -m644 "$srcdir/pam-su" "${pkgdir}/etc/pam.d/su"
}
md5sums=('ba2d8cc12a937231c80a04f7f7149303'
         '4368b3f98abd8a32662e094c54e7f9b1'
         'a31374fef2cba0ca34dfc7078e2969e4'
         'fa85e5cce5d723275b14365ba71a8aad')
