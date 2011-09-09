# Maintainer: Tom Gundersen <teg@jklm.no>
# Contributor: judd <jvinet@zeroflux.org>

pkgname=util-linux
pkgver=2.20
pkgrel=2
pkgdesc="Miscellaneous system utilities for Linux"
url="http://userweb.kernel.org/~kzak/util-linux-ng/"
arch=('i686' 'x86_64')
groups=('base')
depends=('filesystem')
replaces=('linux32' 'util-linux-ng')
conflicts=('linux32' 'util-linux-ng' 'e2fsprogs<1.41.8-2')
provides=('linux32' "util-linux-ng=${pkgver}")
license=('GPL2')
options=('!libtool')
source=(ftp://ftp.kernel.org/pub/linux/utils/${pkgname}/v${pkgver}/${pkgname}-${pkgver}.tar.bz2
	agetty-typo.patch
	write-freopen.patch
	dmesg-non-printk.patch
	dmesg-space.patch)
optdepends=('perl: for chkdupexe support')

build() {
  cd "${srcdir}/${pkgname}-${pkgver}"

  # patches from master
  for patch in agetty-typo.patch write-freopen.patch dmesg-non-printk.patch dmesg-space.patch; do
    patch -Np1 -i "${srcdir}/${patch}"
  done

  # hardware clock
  sed -e 's%etc/adjtime%var/lib/hwclock/adjtime%' -i include/pathnames.h

  ./configure --enable-arch\
              --enable-write\
              --enable-raw\
              --disable-wall\
              --enable-partx

  make
}

package() {
  cd "${srcdir}/${pkgname}-${pkgver}"

  install -dm755 "${pkgdir}/var/lib/hwclock"

  make DESTDIR="${pkgdir}" install
}
md5sums=('4dcacdbdafa116635e52b977d9d0e879'
         '13838c6dd8df686e0f01ad0f236d2690'
         '465817ff8f7c08411c8011ee91b50318'
         'f3ca75a1a22a2a739c5c22d92dc07ab0'
         'd9768f0b42d36d72c02ac7797b922ba1')
