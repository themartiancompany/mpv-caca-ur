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
_git="true"
if [[ "${_os}" == "Android" ]]; then
  _git="false"
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
  'libxext'
  'libxkbcommon'
  'libxpresent'
  'libxrandr'
  'libxss'
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
    'libx11'
    'libxv'
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
_tag="${_commit}"
_tag_name="pkgver"
_tarname="${_pkg}-${_tag}"
[[ "${_offline}" == "true" ]] && \
  _url="file://${HOME}/${_pkg}"
if [[ "${_git}" == true ]]; then
  makedepends+=(
    "git"
  )
  _src="${_tarname}::git+${_url}#${_tag_name}=${_tag}?signed"
  _sum="SKIP"
elif [[ "${_git}" == false ]]; then
  if [[ "${_tag_name}" == 'pkgver' ]]; then
    _src="${_tarname}.tar.gz::${_url}/archive/refs/tags/${_tag}.tar.gz"
    _sum="d4f4179c6e4ce1702c5fe6af132669e8ec4d0378428f69518f2926b969663a91"
  elif [[ "${_tag_name}" == "commit" ]]; then
    _src="${_tarname}.zip::${_url}/archive/${_commit}.zip"
    _sum='31ac0b8014076f8c7cafa1c92c311359dc7f6f931e17c0a718c406cae77272f7'
  fi
fi
source=(
  "${_src}"
  "dynamically_generate_desktop_file_protocols.patch"
  "${_pkg}.android.conf"
)
sha256sums=(
  "${_sum}"
  '88acf97cbc8e0fe745f09bd0bd7f65e0437adcb549dadf3588fd0724d01298e9'
  'd356992ddb798c886f684ce5776bb9c1c145ba33584e108734d6c8372c9b27a5'
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
    _gl \
    _include \
    _x11 \
    _xv
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
    _x11="disabled"
    _xv="disabled"
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
    _x11="auto"
    _xv="auto"
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
    -Dx11="${_x11}"
    -Dxv="${_xv}"
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
    "${_tarname}" \
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
    "${_tarname}"/DOCS/{encoding.rst,tech-overview.txt} \
    "${terdir}/usr/share/doc/${_pkg}"
  install \
    -Dm0644 \
    "${_tarname}"/TOOLS/{"u${_pkg}","${_pkg}_identify.sh",stats-conv.py,idet.sh,lua/*} \
    -t \
    "${terdir}/usr/share/${_pkg}/scripts"
  if [[ "${_os}" == "Android" ]]; then
    install \
      -Dm0644 \
      "${srcdir}/${_pkg}.android.conf"
      "${pkgdir}/etc/${_pkg}/${_pkg}.conf"
  fi
}
