# Maintainer: Tom Gundersen <teg@jklm.no>
# Contributor: judd <jvinet@zeroflux.org>

pkgname=util-linux
pkgver=2.19.1
pkgrel=3
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
source=(ftp://ftp.kernel.org/pub/linux/utils/${pkgname}/v2.19/${pkgname}-${pkgver}.tar.bz2
       mount-segfault-2.19.1.patch
       two-component-linux.patch)
optdepends=('perl: for chkdupexe support')

build() {
  cd "${srcdir}/${pkgname}-${pkgver}"
  # add support for linux 3.0, which is needed mkswap
  patch -Np1 -i ../two-component-linux.patch
  # fix https://bugs.archlinux.org/task/24261
  patch -Np1 -i ../mount-segfault-2.19.1.patch
  # hardware clock
  sed -e 's%etc/adjtime%var/lib/hwclock/adjtime%' -i hwclock/hwclock.c
  ./configure --enable-arch --enable-write --enable-raw --disable-wall --enable-partx
  make
}

package() {
  cd "${srcdir}/${pkgname}-${pkgver}"
  install -dm755 "${pkgdir}/var/lib/hwclock"
  make DESTDIR="${pkgdir}" install
}
md5sums=('3eab06f05163dfa65479c44e5231932c'
         '3247b52f0e4b8044f23f2f7218e2fdea'
         '6eb23edb484adf7192e107d1c6d94bd3')
