# Maintainer: Tom Gundersen <teg@jklm.no>
# Maintainer: Dave Reisner <dreisner@archlinux.org>
# Contributor: judd <jvinet@zeroflux.org>

pkgbase=util-linux
pkgname=(util-linux libutil-linux)
_pkgmajor=2.33
pkgver=${_pkgmajor}
pkgrel=2
pkgdesc="Miscellaneous system utilities for Linux"
url="https://www.kernel.org/pub/linux/utils/util-linux/"
arch=('x86_64')
makedepends=('systemd' 'python' 'libcap-ng')
license=('GPL2')
options=('strip' 'debug')
validpgpkeys=('B0C64D14301CC6EFAEDF60E4E4B71D5EEC39C284')  # Karel Zak
source=("https://www.kernel.org/pub/linux/utils/util-linux/v$_pkgmajor/$pkgbase-$pkgver.tar."{xz,sign}
        '0001-agetty-fix-output-of-escaped-characters.patch'
        pam-{login,common,su}
        'util-linux.sysusers'
        '60-rfkill.rules'
        'rfkill-unblock_.service'
        'rfkill-block_.service')
sha256sums=('f261b9d73c35bfeeea04d26941ac47ee1df937bd3b0583e748217c1ea423658a'
            'SKIP'
            'a20ab3b78eed0e143300476d059e55ab87720bc9fc66a4dcbbd5ae8c48f39bf4'
            '993a3096c2b113e6800f2abbd5d4233ebf1a97eef423990d3187d665d3490b92'
            'fc6807842f92e9d3f792d6b64a0d5aad87995a279153ab228b1b2a64d9f32f20'
            '51eac9c2a2f51ad3982bba35de9aac5510f1eeff432d2d63c6362e45d620afc0'
            '10b0505351263a099163c0d928132706e501dd0a008dac2835b052167b14abe3'
            '7423aaaa09fee7f47baa83df9ea6fef525ff9aec395c8cbd9fe848ceb2643f37'
            '8ccec10a22523f6b9d55e0d6cbf91905a39881446710aa083e935e8073323376'
            'a22e0a037e702170c7d88460cc9c9c2ab1d3e5c54a6985cd4a164ea7beff1b36')

prepare() {
  cd "$pkgbase-$pkgver"

  # agetty: fix output of escaped characters
  patch -Np1 < ../0001-agetty-fix-output-of-escaped-characters.patch
}

build() {
  cd "$pkgbase-$pkgver"

  ./configure \
    --prefix=/usr \
    --libdir=/usr/lib \
    --bindir=/usr/bin \
    --localstatedir=/var \
    --enable-fs-paths-extra=/usr/bin \
    --enable-raw \
    --enable-vipw \
    --enable-newgrp \
    --enable-chfn-chsh \
    --enable-write \
    --enable-mesg \
    --with-python=3

  make
}

package_util-linux() {
  conflicts=('eject' 'zramctl' 'rfkill')
  provides=('eject' 'zramctl' 'rfkill')
  replaces=('zramctl' 'rfkill')
  depends=('pam' 'shadow' 'coreutils' 'libsystemd' 'libcap-ng' 'libutil-linux')
  optdepends=('python: python bindings to libmount'
              'words: default dictionary for look')
  groups=('base' 'base-devel')
  backup=(etc/pam.d/chfn
          etc/pam.d/chsh
          etc/pam.d/login
          etc/pam.d/su
          etc/pam.d/su-l)

  cd "$pkgbase-$pkgver"

  make DESTDIR="$pkgdir" install

  # setuid chfn and chsh
  chmod 4755 "$pkgdir"/usr/bin/{newgrp,ch{sh,fn}}

  # install PAM files for login-utils
  install -Dm644 "$srcdir/pam-common" "$pkgdir/etc/pam.d/chfn"
  install -m644 "$srcdir/pam-common" "$pkgdir/etc/pam.d/chsh"
  install -m644 "$srcdir/pam-login" "$pkgdir/etc/pam.d/login"
  install -m644 "$srcdir/pam-su" "$pkgdir/etc/pam.d/su"
  install -m644 "$srcdir/pam-su" "$pkgdir/etc/pam.d/su-l"

  # TODO(dreisner): offer this upstream?
  sed -i '/ListenStream/ aRuntimeDirectory=uuidd' "$pkgdir/usr/lib/systemd/system/uuidd.socket"

  # adjust for usrmove
  # TODO(dreisner): fix configure.ac upstream so that this isn't needed
  cd "$pkgdir"
  mv {,usr/}sbin/* usr/bin
  rmdir sbin usr/sbin

  ### runtime libs are shipped as part of libutil-linux
  rm "$pkgdir"/usr/lib/lib*.{a,so}*

  ### install systemd-sysusers
  install -Dm644 "$srcdir/util-linux.sysusers" \
    "$pkgdir/usr/lib/sysusers.d/util-linux.conf"

  install -Dm644 "$srcdir/60-rfkill.rules" \
    "$pkgdir/usr/lib/udev/rules.d/60-rfkill.rules"

  install -Dm644 "$srcdir/rfkill-unblock_.service" \
    "$pkgdir/usr/lib/systemd/system/rfkill-unblock@.service"
  install -Dm644 "$srcdir/rfkill-block_.service" \
    "$pkgdir/usr/lib/systemd/system/rfkill-block@.service"
}

package_libutil-linux() {
  pkgdesc="util-linux runtime libraries"
  provides=('libblkid.so' 'libfdisk.so' 'libmount.so' 'libsmartcols.so' 'libuuid.so')

  make -C "$pkgbase-$pkgver" DESTDIR="$pkgdir" install-usrlib_execLTLIBRARIES
}
