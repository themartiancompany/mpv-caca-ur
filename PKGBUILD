# SPDX-License-Identifier: AGPL-3.0
#
# Maintainer: Truocolo <truocolo@aol.com>
# Maintainer: Pellegrino Prevete (tallero) <pellegrinoprevete@gmail.com>
# Maintainer: Christian Hesse <mail@eworm.de>
# Maintainer: Robin Candau <antiz@archlinux.org>
# Contributor: Bartłomiej Piotrowski <bpiotrowski@archlinux.org>
# Contributor: Eivind Uggedal <eivind@uggedal.com>

_os="$( \
  uname \
    -o)"
_egl="true"
if [[ "${_os}" == "Android" ]]; then
  _egl="false"
fi
_py="python"
_pkg=mpv
_variant="caca"
pkgname="${_pkg}-${_variant}"
epoch=1
pkgver=0.39.0
_ffmpeg_ver="2:7.1"
_android_ffmpeg_ver="6.1.2"
_libplacebo_ver=7.349.0
_android_libplacebo_ver="6.338.2"
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
  'glibc'
  'hicolor-icon-theme'
  'jack'
  'libarchive'
  'libass'
  'libbluray'
  "lib${_variant}"
  'libdrm'
  'libglvnd'
  'libjpeg-turbo'
  'libsixel'
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
  'rubberband'
  'uchardet'
  'vapoursynth'
  'wayland'
  'zlib'
)
if [[ "${_egl}" == "true" ]]; then
  depends+=(
    'libegl'
  )
fi
if [[ "${_os}" == "GNU/Linux" ]]; then
  depends+=(
    "ffmpeg=${_ffmpeg_ver}"
    'lcms2'
    "libplacebo=${_libplacebo_ver}"
    'libcdio'
    'libcdio-paranoia'
    'libdvdnav'
    'libdvdread'
    'libgl'
    'libpipewire'
    'libpulse'
    'libva'
    'libvdpau'
    'openal'
    'vulkan-icd-loader'
  )
elif [[ "${_os}" == "Android" ]]; then
  depends+=(
    "ffmpeg=${_android_ffmpeg_ver}"
    "libplacebo=${_android_libplacebo_ver}"
    'openal-soft'
    "pipewire"
    "pulseaudio"
  )
fi
makedepends=(
  'git'
  'meson'
  "${_py}-docutils"
  'vulkan-headers'
)
if [[ "${_os}" == "GNU/Linux" ]]; then
  makedepends+=(
    'ffnvcodec-headers'
    'ladspa'
    'wayland-protocols'
  )
elif [[ "${_os}" == "Android" ]]; then
  makedepends+=(
    'ladspa-sdk'
    'libwayland-protocols'
  )
fi
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

_include_get() {
  local \
    _usr \
    _bin \
    _cc
  _cc="$( \
    command \
      -v \
      "cc" \
      "gcc" \
      "clang" | \
      head \
       -n \
       1)"
  _bin="$( \
    dirname \
      "${_cc}")"
  _usr="$( \
    dirname \
      "${_bin}")"
  echo \
    "${_usr}/include"
}

build() {
  local \
    _cflags=() \
    _meson_options=() \
    _cdda \
    _dvdbin \
    _dvdnav \
    _include
  _include="$( \
    _include_get)"
  _cflags=(
    "${CFLAGS}"
  )
  if [[ "${_os}" == "Android" ]]; then
    _cdda="disabled"
    _dvdbin="disabled"
    _dvdnav="disabled"
    _gl="disabled"
    _cflags+=(
      -I"${_include}/vapoursynth"
    )
    _meson_options+=(
      -Dandroid-media-ndk=disabled
    )
  elif [[ "${_os}" == "GNU/Linux" ]]; then
    _cdda="enabled"
    _dvdbin="enabled"
    _dvdnav="enabled"
    _gl="enabled"
  fi
  _meson_options+=(
    --auto-features auto
    -D"lib${_pkg}"="true"
    -Dgl="${_gl}"
    -Dgl-x11="${_gl}"
    -D"${_variant}"="enabled"
    -Dcdda="${_cdda}"
    -Ddvbin="${_dvdbin}"
    -Ddvdnav="${_dvdnav}"
    -Dlibarchive=enabled
    -Dopenal=enabled
  )
  if [[ "${_egl}" == "false" ]]; then
    _meson_options+=(
      -Degl="disabled"
    )
  fi
  export \
    CFLAGS="${_cflags[*]}"
  CFLAGS="${_cflags[*]}" \
  CXXFLAGS="${_cflags[*]}" \
  arch-meson \
    "${_pkg}" \
      build \
      "${_meson_options[@]}"
  CFLAGS="${_cflags[*]}" \
  CXXFLAGS="${_cflags[*]}" \
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
  if [[ "${_os}" == "GNU/Linux" ]]; then
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
      'libarchive.so'
      'libass.so'
      'libbluray.so'
      'liblcms2.so'
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
  fi
  meson \
    install \
    -C \
      build \
    --destdir \
      "${terdir}"
  # delete private entries only required for static linking 
  sed \
    -i \
    -e \
      '/Requires.private/d' \
    -e \
      '/Libs.private/d' \
    "${terdir}/usr/lib/pkgconfig/${_pkg}.pc"
  install \
    -m0644 \
    "${_pkg}"/DOCS/{encoding.rst,tech-overview.txt} \
    "${terdir}/usr/share/doc/${_pkg}"
  install \
    -Dm0644 \
    "${_pkg}"/TOOLS/{"u${_pkg}","${_pkg}_identify.sh",stats-conv.py,idet.sh,lua/*} \
    -t \
    "${terdir}/usr/share/${_pkg}/scripts"
}
