# Maintainer: Tom Gundersen <teg@jklm.no>
# Contributor: judd <jvinet@zeroflux.org>

pkgname=util-linux
pkgver=2.23
pkgrel=3
pkgdesc="Miscellaneous system utilities for Linux"
url="http://www.kernel.org/pub/linux/utils/util-linux/"
arch=('i686' 'x86_64')
groups=('base' 'base-devel')
depends=('pam' 'shadow' 'coreutils' 'glibc')
makedepends=('systemd')
# checkdepends=('bc')
conflicts=('util-linux-ng' 'eject')
provides=("util-linux-ng=$pkgver" 'eject')
license=('GPL2')
options=('!libtool')
source=("ftp://ftp.kernel.org/pub/linux/utils/$pkgname/v2.23/$pkgname-$pkgver.tar.xz"
        0001-lib-loopdev-fix-loopcxt_check_size-to-work-with-blkd.patch
        0001-losetup-use-warn_size-for-regular-files-only.patch
        0001-libfdisk-do-not-use-va_list-in-the-Ask-API.patch
        uuidd.tmpfiles
        pam-login
        pam-common
        pam-su)
backup=(etc/pam.d/chfn
        etc/pam.d/chsh
        etc/pam.d/login
        etc/pam.d/su
        etc/pam.d/su-l)
install=util-linux.install
md5sums=('cf5e9bb402371beaaffc3a5f276d5783'
         'fdb627fbb3d6a42e0b36978649b4c064'
         'de0ba450945a60f27c5df86e64523d57'
         'df949d15dbff01fe9fcda5d999a35b15'
         'a39554bfd65cccfd8254bb46922f4a67'
         '4368b3f98abd8a32662e094c54e7f9b1'
         'a31374fef2cba0ca34dfc7078e2969e4'
         'fa85e5cce5d723275b14365ba71a8aad')

prepare() {
  cd "$pkgname-$pkgver"

  patch -Np1 <"$srcdir"/0001-lib-loopdev-fix-loopcxt_check_size-to-work-with-blkd.patch
  patch -Np1 <"$srcdir"/0001-losetup-use-warn_size-for-regular-files-only.patch
  patch -Np1 <"$srcdir"/0001-libfdisk-do-not-use-va_list-in-the-Ask-API.patch
}

build() {
  cd "${pkgname}-${pkgver}"

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
#              --enable-line \ # not part of any package
#              --enable-last \ # not compat

  make
}

#check() {
# fails for some reason in chroot, works outside
#  make -C "$pkgname-$pkgver" check
#}

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
  install -m644 "$srcdir/pam-su" "${pkgdir}/etc/pam.d/su-l"

  # include tmpfiles fragment for uuidd
  # TODO(dreisner): offer this upstream?
  install -Dm644 "$srcdir/uuidd.tmpfiles" "$pkgdir/usr/lib/tmpfiles.d/uuidd.conf"
}
