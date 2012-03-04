# Maintainer: Tom Gundersen <teg@jklm.no>
# Contributor: judd <jvinet@zeroflux.org>

pkgname=util-linux
pkgver=2.21
pkgrel=5
pkgdesc="Miscellaneous system utilities for Linux"
url="http://www.kernel.org/pub/linux/utils/util-linux/"
arch=('i686' 'x86_64')
groups=('base')
depends=('udev')
conflicts=('util-linux-ng')
provides=("util-linux-ng=${pkgver}")
license=('GPL2')
options=('!libtool')
source=(ftp://ftp.kernel.org/pub/linux/utils/${pkgname}/v${pkgver}/${pkgname}-${pkgver}.tar.xz
	libmount-canonicalize-all-paths-from-fs-tab.patch
	lib-canonicalize-always-remove-tailing-slash.patch
	libmount-canonicalize-targets-from-fstab-on-mount-a.patch
	mount-new-cleanup-mount-a-return-codes.patch
	libmount-use-mount.-type-s-for-NFS-only.patch
	libmount-allow-empty-source-for-mount-2-syscall.patch
	mount-new-add-internal-only-i-to-non-root-allowed-op.patch
	libmount-don-t-treat-none-differently.patch
	libmount-cosmetic-changes-around-none.patch)
optdepends=('perl: for chkdupexe support')

build() {
  cd "${srcdir}/${pkgname}-${pkgver}"

  # hardware clock
  sed -e 's%etc/adjtime%var/lib/hwclock/adjtime%' -i include/pathnames.h

  patch -p1 -i ../libmount-canonicalize-all-paths-from-fs-tab.patch
  patch -p1 -i ../lib-canonicalize-always-remove-tailing-slash.patch
  patch -p1 -i ../libmount-canonicalize-targets-from-fstab-on-mount-a.patch
  patch -p1 -i ../mount-new-cleanup-mount-a-return-codes.patch 
  patch -p1 -i ../libmount-use-mount.-type-s-for-NFS-only.patch
  patch -p1 -i ../libmount-allow-empty-source-for-mount-2-syscall.patch
  patch -p1 -i ../mount-new-add-internal-only-i-to-non-root-allowed-op.patch
  patch -p1 -i ../libmount-don-t-treat-none-differently.patch
  patch -p1 -i ../libmount-cosmetic-changes-around-none.patch

  ./configure --prefix=/usr \
              --libdir=/usr/lib \
              --enable-write \
              --enable-raw \
              --disable-wall \
              --enable-new-mount

  make
}

package() {
  cd "${srcdir}/${pkgname}-${pkgver}"

  make DESTDIR="${pkgdir}" install

  cd "${pkgdir}"

  install -dm755 var/lib/hwclock

  # delete stray empty dir, fixed upstream
  rm -r usr/share/man/ru
}
