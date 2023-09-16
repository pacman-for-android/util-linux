# Maintainer: Tom Gundersen <teg@jklm.no>
# Maintainer: Dave Reisner <dreisner@archlinux.org>
# Contributor: judd <jvinet@zeroflux.org>

pkgbase=util-linux
pkgname=(util-linux util-linux-libs)
_tag='d32d74bf433a419f2a8976530fb03669bde722cd' # git rev-parse v${_tag_name}
_tag_name=2.39.2
pkgver=${_tag_name/-/}
pkgrel=1.4
pkgdesc='Miscellaneous system utilities for Linux'
url='https://github.com/util-linux/util-linux'
arch=('x86_64' 'aarch64')
makedepends=('git' 'meson' 'bash-completion' 'libcap-ng'
             'libutempter' 'libxcrypt' 'python')
license=('GPL2')
options=('strip')
validpgpkeys=('B0C64D14301CC6EFAEDF60E4E4B71D5EEC39C284')  # Karel Zak
source=("git+https://github.com/util-linux/util-linux#tag=${_tag}?signed"
        pam-{login,common,runuser,su}
        'util-linux.sysusers'
        '60-rfkill.rules'
        'rfkill-unblock_.service'
        'rfkill-block_.service'
        'change-pathnames.patch')
sha256sums=('SKIP'
            '99cd77f21ee44a0c5e57b0f3670f711a00496f198fc5704d7e44f5d817c81a0f'
            '57e057758944f4557762c6def939410c04ca5803cbdd2bfa2153ce47ffe7a4af'
            '48d6fba767631e3dd3620cf02a71a74c5d65a525d4c4ce4b5a0b7d9f41ebfea1'
            '3f54249ac2db44945d6d12ec728dcd0d69af0735787a8b078eacd2c67e38155b'
            '10b0505351263a099163c0d928132706e501dd0a008dac2835b052167b14abe3'
            '7423aaaa09fee7f47baa83df9ea6fef525ff9aec395c8cbd9fe848ceb2643f37'
            '7cd2490466d752bcf33fd9cc37e55fd6f05aedc957394164c7abe2ec58de56be'
            '670f68979cadde209606ab7878b83d60b141596d7679c8a68f5500381c7037c2'
            'aa72f8584f5962d3fae944a82853571f94670d3aa0abbbf57bbe2a58e6e62b93')

_backports=(
)

_reverts=(
)

prepare() {
  cd "${pkgbase}"

  local _c _l
  for _c in "${_backports[@]}"; do
    if [[ "${_c}" == *..* ]]; then _l='--reverse'; else _l='--max-count=1'; fi
    git log --oneline "${_l}" "${_c}"
    git cherry-pick --mainline 1 --no-commit "${_c}"
  done
  for _c in "${_reverts[@]}"; do
    if [[ "${_c}" == *..* ]]; then _l='--reverse'; else _l='--max-count=1'; fi
    git log --oneline "${_l}" "${_c}"
    git revert --mainline 1 --no-commit "${_c}"
  done

  patch -Np1 -i ../change-pathnames.patch

  # do not mark dirty
  sed -i '/dirty=/c dirty=' tools/git-version-gen
  sed -i "s@'/run'@'/data/run'@" meson.build

  sed -i '1s|.*|#!/data/usr/bin/sh|' tools/{ko-release-{gen,push},*.sh,config-gen}
}

build() {
  local _meson_options=(
    -Dfs-search-path=/data/usr/bin:/data/usr/local/bin
    # -Drunstatedir=/data/run

    -Dlibuser=disabled
    -Dncurses=disabled
    -Dncursesw=enabled
    -Deconf=disabled
    -Dsystemd=disabled
    -Dcryptsetup=disabled
    -Dcryptsetup-dlopen=disabled

    -Dbuild-chfn-chsh=enabled
    -Dbuild-line=disabled
    -Dbuild-mesg=enabled
    -Dbuild-newgrp=enabled
    -Dbuild-vipw=enabled
    -Dbuild-write=enabled
  )

  arch-meson "${pkgbase}" build "${_meson_options[@]}"

  meson compile -C build
}

package_util-linux() {
  conflicts=('rfkill' 'hardlink')
  provides=('rfkill' 'hardlink')
  replaces=('rfkill' 'hardlink')
  depends=('pam' 'shadow' 'coreutils'
           'libcap-ng' 'libutempter' 'libxcrypt' 'libcrypt.so' 'util-linux-libs'
           'libmagic.so' 'libncursesw.so')
  optdepends=('words: default dictionary for look')
  backup=(data/etc/pam.d/chfn
          data/etc/pam.d/chsh
          data/etc/pam.d/login
          data/etc/pam.d/runuser
          data/etc/pam.d/runuser-l
          data/etc/pam.d/su
          data/etc/pam.d/su-l)

  _python_stdlib="$(python -c 'import sysconfig; print(sysconfig.get_paths()["stdlib"])')"

  DESTDIR="${pkgdir}" meson install -C build

  # remove static libraries
  rm "${pkgdir}"/data/usr/lib/lib*.a*

  # setuid chfn and chsh
  chmod 4755 "${pkgdir}"/data/usr/bin/{newgrp,ch{sh,fn}}

  # Rename su binary
  mv "${pkgdir}"/data/usr/bin/su{,-linux}

  # install PAM files for login-utils
  install -Dm0644 pam-common "${pkgdir}/data/etc/pam.d/chfn"
  install -m0644 pam-common "${pkgdir}/data/etc/pam.d/chsh"
  install -m0644 pam-login "${pkgdir}/data/etc/pam.d/login"
  install -m0644 pam-runuser "${pkgdir}/data/etc/pam.d/runuser"
  install -m0644 pam-runuser "${pkgdir}/data/etc/pam.d/runuser-l"
  install -m0644 pam-su "${pkgdir}/data/etc/pam.d/su"
  install -m0644 pam-su "${pkgdir}/data/etc/pam.d/su-l"

  # TODO(dreisner): offer this upstream?
  # sed -i '/ListenStream/ aRuntimeDirectory=uuidd' "${pkgdir}/data/usr/lib/systemd/system/uuidd.socket"

  # runtime libs are shipped as part of util-linux-libs
  install -d -m0755 util-linux-libs/lib/
  mv "$pkgdir"/data/usr/lib/lib*.so* util-linux-libs/lib/
  mv "$pkgdir"/data/usr/lib/pkgconfig util-linux-libs/lib/pkgconfig
  mv "$pkgdir"/data/usr/include util-linux-libs/include
  mv "$pkgdir"/"${_python_stdlib}"/site-packages util-linux-libs/site-packages
  rmdir "$pkgdir"/"${_python_stdlib}"
  # mv "$pkgdir"/data/usr/share/man/man3 util-linux-libs/man3

  # install systemd-sysusers
  # install -Dm0644 util-linux.sysusers \
  #   "${pkgdir}/usr/lib/sysusers.d/util-linux.conf"

  # install -Dm0644 60-rfkill.rules \
  #   "${pkgdir}/usr/lib/udev/rules.d/60-rfkill.rules"

  # install -Dm0644 rfkill-unblock_.service \
  #   "${pkgdir}/usr/lib/systemd/system/rfkill-unblock@.service"
  # install -Dm0644 rfkill-block_.service \
  #   "${pkgdir}/usr/lib/systemd/system/rfkill-block@.service"
}

package_util-linux-libs() {
  pkgdesc="util-linux runtime libraries"
  depends=('glibc')
  provides=('libutil-linux' 'libblkid.so' 'libfdisk.so' 'libmount.so' 'libsmartcols.so' 'libuuid.so')
  conflicts=('libutil-linux')
  replaces=('libutil-linux')
  optdepends=('python: python bindings to libmount')

  install -d -m0755 "$pkgdir"/{"${_python_stdlib}",data/usr}
  mv util-linux-libs/lib/* "$pkgdir"/data/usr/lib/
  mv util-linux-libs/include "$pkgdir"/data/usr/include
  mv util-linux-libs/site-packages "$pkgdir"/"${_python_stdlib}"/site-packages
  # mv util-linux-libs/man3 "$pkgdir"/data/usr/share/man/man3
}
