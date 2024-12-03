# SPDX-License-Identifier: AGPL-3.0
#
# Maintainer: Truocolo <truocolo@aol.com>
# Maintainer: Pellegrino Prevete (tallero) <pellegrinoprevete@gmail.com>
# Maintainer: Christian Hesse <mail@eworm.de>
# Maintainer: Robin Candau <antiz@archlinux.org>
# Contributor: Bartłomiej Piotrowski <bpiotrowski@archlinux.org>
# Contributor: Eivind Uggedal <eivind@uggedal.com>

_pkg=mpv
_variant="caca"
pkgname="${_pkg}-${_variant}"
epoch=1
pkgver=0.39.0
pkgrel=4
pkgdesc='a free, open source, and cross-platform media player'
arch=(
  'x86_64'
  'arm'
  'aarch64'
  'armv7l'
  'armv6l'
  'i686'
  'pentium4'
  'mips'
)
license=(
  'GPL-2.0-or-later AND LGPL-2.1-or-later'
)
url="https://${_pkg}.io/"
depends=(
  'alsa-lib'
  'desktop-file-utils'
  'ffmpeg'
  'glibc'
  'hicolor-icon-theme'
  'jack'
  'lcms2'
  'libarchive'
  'libass'
  'libbluray'
  "lib${_variant}"
  'libcdio'
  'libcdio-paranoia'
  'libdrm'
  'libdvdnav'
  'libdvdread'
  'libegl'
  'libgl'
  'libglvnd'
  'libjpeg-turbo'
  'libplacebo'
  'libpulse'
  'libsixel'
  'libva'
  'libvdpau'
  'libx11'
  'libxext'
  'libxkbcommon'
  'libxpresent'
  'libxrandr'
  'libxss'
  'libxv'
  'luajit'
  'mesa'
  'mujs'
  'libpipewire'
  'rubberband'
  'openal'
  'uchardet'
  'vapoursynth'
  'vulkan-icd-loader'
  'wayland'
  'zlib'
)
makedepends=(
  'git'
  'meson'
  'python-docutils'
  'ladspa'
  'wayland-protocols'
  'ffnvcodec-headers'
  'vulkan-headers'
)
optdepends=(
  'yt-dlp: for video-sharing websites playback'
)
provides=(
  "lib${_pkg}.so"
  "${_pkg}=${pkgver}"
)
conflicts=(
  "${_pkg}"
)
options=(
  '!emptydirs'
)
validpgpkeys=(
  # sfan5 <sfan5@live.de>
  '145077D82501AA20152CACCE8D769208D5E31419'
)
_http="https://github.com"
_ns="${_pkg}-player"
_url="${_http}/${_ns}/${_pkg}"
source=(
  "git+${_url}.git#tag=v${pkgver}?signed"
  "dynamically_generate_desktop_file_protocols.patch"
)
sha256sums=(
  '51e787dbff240d69227f306685fc962daae215c755689b9de4ef0432ddf4443b'
  '88acf97cbc8e0fe745f09bd0bd7f65e0437adcb549dadf3588fd0724d01298e9'
)

build() {
  local \
    _meson_options=()
  _meson_options=(
    --auto-features auto
    -D"lib${_pkg}"="true"
    -Dgl-x11=enabled
    -D"${_variant}"="enabled"
    -Dcdda=enabled
    -Ddvbin=enabled
    -Ddvdnav=enabled
    -Dlibarchive=enabled
    -Dopenal=enabled
  )
  arch-meson \
    "${_pkg}" \
      build \
      "${_meson_options[@]}"
  meson \
    compile \
    -C \
      build
}

check() {
  meson \
    test \
    -C \
      build
}

package() {
  depends+=(
    'libasound.so'
    'libavcodec.so'
    'libavdevice.so'
    'libavfilter.so'
    'libavformat.so'
    'libavutil.so'
    'libswresample.so'
    'libswscale.so'
    'libjack.so'
    'liblcms2.so'
    'libarchive.so'
    'libass.so'
    'libbluray.so'
    'libjpeg.so'
    'libplacebo.so'
    'libpulse.so'
    'libva.so'
    'libva-drm.so'
    'libva-wayland.so'
    'libva-x11.so'
    'libxkbcommon.so'
    'librubberband.so'
  )
  meson \
    install \
    -C \
      build \
    --destdir \
      "${pkgdir}"
  # delete private entries only required for static linking 
  sed \
    -i \
    -e \
      '/Requires.private/d' \
    -e \
      '/Libs.private/d' \
    "${pkgdir}/usr/lib/pkgconfig/${_pkg}.pc"
  install \
    -m0644 \
    "${_pkg}"/DOCS/{encoding.rst,tech-overview.txt} \
    "${pkgdir}/usr/share/doc/${_pkg}"
  install \
    -Dm0644 \
    "${_pkg}"/TOOLS/{"u${_pkg}","${_pkg}_identify.sh",stats-conv.py,idet.sh,lua/*} \
    -t \
    "${pkgdir}/usr/share/${_pkg}/scripts"
}
