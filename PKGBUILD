# SPDX-License-Identifier: AGPL-3.0
#
# Maintainer: Truocolo <truocolo@aol.com>
# Maintainer: Pellegrino Prevete (tallero) <pellegrinoprevete@gmail.com>
# Maintainer : Daniel Bermond <dbermond@archlinux.org>
# Contributor: Ashok Arora <ashok.maillist@gmail.com>
# Contributor: Johannes Janosovits <johannes@walnutempire.de>
# Contributor: Joshua Schüler <joshua.schueler at gmail dotcom>
# Contributor: Ray Rashif <schiv@archlinux.org>
# Contributor: Tobias Powalowski <tpowa@archlinux.org>

_python="false"
_pymajver="2"
_pyminver="7"
_pyver="${_pymajver}.${_pyminver}"
_py="python${_pymajver}"
_proj="opencv"
_pkg="${_proj}"
pkgname="${_pkg}2"
pkgver=2.4.13.7
pkgrel=5
pkgdesc='Open Source Computer Vision Library (version 2.x)'
arch=(
  'x86_64'
  'i686'
  'arm'
  'aarch64'
  'armv7l'
  'armv6l'
  'mips'
  'pentium4'
)
license=(
  'BSD'
)
url="https://${_pkg}.org"
depends=(
  'glib2'
  'gst-plugins-base'
  'gst-plugins-base-libs'
  'gstreamer'
  'gtk2'
  'gtkglext'
  'libdc1394'
  'libgl'
  'libjpeg'
  'libpng'
  'libtiff'
  'zlib'
)
makedepends=(
  'cmake'
  'eigen'
  'mesa'
)
optdepends=(
  'opencl-icd-loader: for coding with OpenCL'
)
if [[ "${_python}" == "true" ]]; then
  makedepends+=(
    "${_py}-numpy"
  )
elif [[ "${_python}" == "false" ]]; then
  optdepends+=(
    "${_py}-numpy: Python 2.x bindings"
  )
fi
_http="https://github.com"
_ns="${_pkg}"
_url="${_http}/${_ns}/${_pkg}"
source=(
  "${_url}/archive/${pkgver}/${pkgname}-${pkgver}.tar.gz"
  "010-${pkgname}-remove-prototypes-warnings.patch"
)
sha256sums=(
  '192d903588ae2cdceab3d7dc5a5636b023132c8369f184ca89ccec0312ae33d0'
  '0ac6a30a0708339486941b0c784a3a94f4ab2a5154cd74ae82edd405b4f61823'
)

prepare() {
  patch \
    -d \
      "${_pkg}-${pkgver}" \
    -Np1 \
    -i \
    "${srcdir}/010-${pkgname}-remove-prototypes-warnings.patch"
}

_bin_get() {
  local \
    _bin \
    _cmake
  _cmake="$( \
    command \
      -v \
      "cmake")"
  _bin="$( \
    dirname \
      "${_cmake}")"
  echo \
    "${_bin}"
}

_usr_get() {
  local \
    _bin
  _bin="$( \
    _bin_get)"
  echo \
    "$(dirname \
      "${_bin}")"
}

_lib_get() {
  local \
    _usr
  _usr="$( \
    _usr_get)"  
  echo \
    "${_usr}/lib"
}

_include_get() {
  local \
    _usr
  _usr="$( \
    _usr_get)"  
  echo \
    "${_usr}/include"
}

build() {
  local \
    _cmake_opts=() \
    _opencv_python \
    _bin \
    _include \
    _lib \
    _python_include \
    _numpy_include \
    _cxxflags=()
  _cxxflags=(
    "${CXXFLAGS}"
    -std=c++14
    -ffat-lto-objects
  )
  export \
    CXXFLAGS="${_cxxflags[*]}"
  _opencv_python="OFF"
  if [[ "${_python}" == "true" ]]; then
    _bin="$( \
      _bin_get)"
    _lib="$( \
      _lib_get)"
    _include="$( \
      _include_get)"
    _numpy_include="${_lib}/python${_pyver}/site-packages/numpy/core/include"
    _python_include="${_include}/python${_pyver}"
    _opencv_python="ON"
  fi
  _cmake_opts+=(
    -DCMAKE_BUILD_TYPE:STRING='None'
    -DCMAKE_INSTALL_PREFIX:PATH="/opt/${pkgname}"
    -DCMAKE_SKIP_INSTALL_RPATH:BOOL='YES'
    -DBUILD_JASPER:BOOL='ON'
    -DBUILD_opencv_java:BOOL='OFF'
    -DBUILD_opencv_python:BOOL="${_opencv_python}"
    -DBUILD_EXAMPLES:BOOL='OFF'
    -DBUILD_PERF_TESTS:BOOL='OFF'
    -DBUILD_TESTS:BOOL='OFF'
    -DBUILD_WITH_DEBUG_INFO:BOOL='OFF'
    -DINSTALL_C_EXAMPLES:BOOL='OFF'
    -DINSTALL_PYTHON_EXAMPLES:BOOL='OFF'
    -DWITH_CUDA:BOOL='OFF'
    -DWITH_FFMPEG:BOOL:BOOL='OFF'
    -DWITH_GSTREAMER:BOOL='ON'
    -DWITH_LIBV4L:BOOL='OFF'
    -DWITH_OPENCL:BOOL='ON'
    -DWITH_OPENGL:BOOL='ON'
    -DWITH_TBB:BOOL='OFF'
    -DWITH_V4L:BOOL='ON'
    -DWITH_XINE:BOOL='OFF'
    -DENABLE_SSE3:BOOL='OFF'
    -DENABLE_SSSE4:BOOL='OFF'
    -DENABLE_SSE41:BOOL='OFF'
    -DENABLE_SSE42:BOOL='OFF'
    -DENABLE_AVX:BOOL='OFF'
    -DENABLE_AVX2:BOOL='OFF'
    -Wno-dev
  )
  if [[ "${_python}" == "true" ]]; then
    _cmake_opts=(
      -DPYTHON_DEFAULT_EXECUTABLE:STRING="${_bin}/${_py}"
      -DPYTHON_EXECUTABLE:STRING="${_bin}/${_py}"
      -DPYTHON_INCLUDE_PATH:STRING="${_python_include}"
      -DPYTHON_LIBRARIES:STRING="${_lib}/python${_pyver}.so"
      -DPYTHON_LIBRARY:STRING="${_lib}/python${pyver}.so" \
      -DPYTHON_NUMPY_INCLUDE_DIR:STRING="${_numpy_include}"
    )
    export \
      PYTHON_INCLUDE_PATH="${_include}/python${_pyver}" \
      PYTHON_LIBRARIES="${_lib}" \
      PYTHON_LIBRARY="${_lib}/python${_pyver}.so" \
      PYTHON_NUMPY_INCLUDE_DIR="${_numpy_include}" \
      PYTHON_EXECUTABLE="${_bin}/${_py}"
  fi
  cmake \
    -S \
      "${_pkg}-${pkgver}" \
    -B build \
    -G 'Unix Makefiles' \
    "${_cmake_opts[@]}"
  cmake \
    --build \
      build
}

package() {
  local \
    _bin \
    _lib
  DESTDIR="$pkgdir" \
  cmake \
    --install \
      build
  install \
    -dm755 \
    "${pkgdir}/usr"/{"bin","include","lib/pkgconfig"}
  install \
    -Dm644 \
    "${_pkg}-${pkgver}/LICENSE" \
    -t \
    "${pkgdir}/usr/share/licenses/${pkgname}"
  ln \
    -s "../../../opt/${pkgname}/lib/pkgconfig/${_pkg}.pc" \
    "${pkgdir}/usr/lib/pkgconfig/${pkgname}.pc"
  ln \
    -s \
    "../../../opt/${pkgname}/lib/pkgconfig/${_pkg}.pc" \
    "${pkgdir}/usr/lib/pkgconfig/${_pkg}.pc"
  ln \
    -s \
    "../../opt/${pkgname}/include/${pkgname}" \
    "${pkgdir}/usr/include/${pkgname}"
  ln \
    -s \
    "../../opt/${pkgname}/include/${_pkg}" \
    "${pkgdir}/usr/include/${_pkg}"
  for _bin in "${pkgdir}/opt/${pkgname}/bin/"*; do
    ln \
      -s \
      "../../opt/${pkgname}/bin/${_bin##*/}" \
      "${pkgdir}/usr/bin/${_bin##*/}2"
  done
  for _lib in "${pkgdir}/opt/${pkgname}/lib/libopencv"*.so."${pkgver%.*}"; do
    ln \
      -s \
      "../../opt/${pkgname}/lib/${_lib##*/}" \
      "${pkgdir}/usr/lib/${_lib##*/}"
  done
  while read -r -d '' _lib
  do
    ln \
      -s \
      "${_lib}" \
      "${pkgdir}/usr/lib/${_lib%.*}"
  done < \
    <(find \
        -L \
	"${pkgdir}/usr/lib" \
	-type \
	  f \
	-name \
	  "lib${_pkg}*.so.${pkgver%.*}" \
	-print0 | \
	  sed \
	    -z \
	    's|.*/||')
}
