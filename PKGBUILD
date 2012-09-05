# Maintainer: Tom Gundersen <teg@jklm.no>
# Contributor: judd <jvinet@zeroflux.org>

pkgname=util-linux
pkgver=2.22
pkgrel=2
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
        su.1
        uuidd.tmpfiles
        pam-login
        pam-common
        pam-su)
backup=(etc/pam.d/chfn etc/pam.d/chsh etc/pam.d/login etc/pam.d/su)
install=util-linux.install
md5sums=('ba2d8cc12a937231c80a04f7f7149303'
         '7f524538dcf57284a86f03a98e624f04'
         'a39554bfd65cccfd8254bb46922f4a67'
         '4368b3f98abd8a32662e094c54e7f9b1'
         'a31374fef2cba0ca34dfc7078e2969e4'
         'fa85e5cce5d723275b14365ba71a8aad')

build() {
  cd "${srcdir}/${pkgname}-${pkgver}"

  # unbreak --localstatedir
  # TODO(dreisner): find out what sami hand in mind with these heuristics
  sed -i '71,75d' configure.ac
  ./autogen.sh

  ./configure --prefix=/usr \
              --libdir=/usr/lib \
              --localstatedir=/run \
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

  # broken buildsys doesn't include su(1), which means it
  # isn't even in the dist tarball
  # TODO(dreisner): patch for this already sent upstream
  install -m644 "$srcdir/su.1" "$pkgdir/usr/share/man/man1/su.1"

  # include tmpfiles fragment for uuidd
  # TODO(dreisner): offer this upstream
  install -Dm644 "$srcdir/uuidd.tmpfiles" "$pkgdir/usr/lib/tmpfiles.d/uuidd.conf"
}
