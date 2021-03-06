# Created by: Tk-Glitch <ti3nou at gmail dot com>

plain '       .---.`               `.---.'
plain '    `/syhhhyso-           -osyhhhys/`'
plain '   .syNMdhNNhss/``.---.``/sshNNhdMNys.'
plain '   +sdMh.`+MNsssssssssssssssNM+`.hMds+'
plain '   :syNNdhNNhssssssssssssssshNNhdNNys:'
plain '    /ssyhhhysssssssssssssssssyhhhyss/'
plain '    .ossssssssssssssssssssssssssssso.'
plain '   :sssssssssssssssssssssssssssssssss:'
plain '  /sssssssssssssssssssssssssssssssssss/'
plain ' :sssssssssssssoosssssssoosssssssssssss:'
plain ' osssssssssssssoosssssssoossssssssssssso'
plain ' osssssssssssyyyyhhhhhhhyyyyssssssssssso'
plain ' /yyyyyyhhdmmmmNNNNNNNNNNNmmmmdhhyyyyyy/'
plain '  smmmNNNNNNNNNNNNNNNNNNNNNNNNNNNNNmmms'
plain '   /dNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNd/'
plain '    `:sdNNNNNNNNNNNNNNNNNNNNNNNNNds:`'
plain '       `-+shdNNNNNNNNNNNNNNNdhs+-`'
plain '             `.-:///////:-.`'
_where="$PWD" # track basedir as different Arch based distros are moving srcdir around
source "$_where"/customization.cfg

# Load external configuration file if present. Available variable values will overwrite customization.cfg ones.
if [ -e "$_EXT_CONFIG_PATH" ]; then
  source "$_EXT_CONFIG_PATH" && msg2 "External configuration file $_EXT_CONFIG_PATH will be used to override customization.cfg values.\n"
fi

if [ -n "$_vkd3d_source" ] && [ "$_vkd3d_source" != 'git://source.winehq.org/git/vkd3d' ]; then
  _vkd3dsrcdir=$( sed 's|/|-|g' <<< $(sed 's|.*://.[^/]*/||g' <<< $_vkd3d_source))
else
  _vkd3dsrcdir='vkd3d'
  _vkd3d_source='git://source.winehq.org/git/vkd3d'
fi

if [ "$_vkd3d_source" = "https://github.com/HansKristian-Work/vkd3d-proton" ]; then
  _pkgnameprefix="vkd3d-proton-tkg-git"
else
  _pkgnameprefix="vkd3d-tkg-git"
fi

pkgname=("$_pkgnameprefix" "lib32-$_pkgnameprefix")
pkgver=1.2.r48.g5fb9bcd
pkgrel=1

# custom vkd3d commit to pass to git
if [ -n "$_vkd3d_commit" ]; then
  _vkd3d_commit="#commit=${_vkd3d_commit}"
fi

pkgdesc='Wine d3d12 to vulkan translation lib, git version'
url='https://source.winehq.org/git/vkd3d.git'
arch=('x86_64')
license=('LGPL')
makedepends=('git' 'autoconf' 'ncurses' 'bison' 'perl' 'fontforge' 'flex'
    'gcc>=4.5.0-2' 'spirv-headers'
    'vulkan-headers' 'vulkan-icd-loader'
    'lib32-vulkan-icd-loader' 'wine'
    'glslang'
)

if [ "$_vkd3d_source" = "https://github.com/HansKristian-Work/vkd3d-proton" ] && [ "$_fork_disable_meson" = "false" ]; then
  makedepends+=('meson' 'ninja' 'xcb-util-keysyms' 'lib32-xcb-util-keysyms')
fi

optdepends=('schedtool')

source=("$_vkd3dsrcdir"::"git+${_vkd3d_source}${_vkd3d_commit}"
)

makedepends=(${makedepends[@]} ${depends[@]})

function exit_cleanup {
  # Remove temporarily copied patches
  rm -rf "$_where"/src/*

  # Community patches removal
  for _p in ${_community_patches[@]}; do
    rm -f "$_where"/$_p
  done

  remove_deps

  msg2 "Cleanup done"
}

user_patcher() {
	# To patch the user because all your base are belong to us
	local _patches=("$_where"/*."${_userpatch_ext}revert")
	if [ ${#_patches[@]} -ge 2 ] || [ -e "${_patches}" ]; then
	  if [ "$_user_patches_no_confirm" != "true" ]; then
	    msg2 "Found ${#_patches[@]} 'to revert' userpatches for ${_userpatch_target}:"
	    printf '%s\n' "${_patches[@]}"
	    read -rp "Do you want to install it/them? - Be careful with that ;)"$'\n> N/y : ' _CONDITION;
	  fi
	  if [ "$_CONDITION" == "y" ] || [ "$_user_patches_no_confirm" == "true" ]; then
	    for _f in "${_patches[@]}"; do
	      if [ -e "${_f}" ]; then
	        msg2 "######################################################"
	        msg2 ""
	        msg2 "Reverting your own ${_userpatch_target} patch ${_f}"
	        msg2 ""
	        msg2 "######################################################"
	        patch -Np1 -R < "${_f}"
	        echo "Reverted your own patch ${_f}" >> "$_where"/last_build_config.log
	      fi
	    done
	  fi
	fi

	_patches=("$_where"/*."${_userpatch_ext}patch")
	if [ ${#_patches[@]} -ge 2 ] || [ -e "${_patches}" ]; then
	  if [ "$_user_patches_no_confirm" != "true" ]; then
	    msg2 "Found ${#_patches[@]} userpatches for ${_userpatch_target}:"
	    printf '%s\n' "${_patches[@]}"
	    read -rp "Do you want to install it/them? - Be careful with that ;)"$'\n> N/y : ' _CONDITION;
	  fi
	  if [ "$_CONDITION" == "y" ] || [ "$_user_patches_no_confirm" == "true" ]; then
	    for _f in "${_patches[@]}"; do
	      if [ -e "${_f}" ]; then
	        msg2 "######################################################"
	        msg2 ""
	        msg2 "Applying your own ${_userpatch_target} patch ${_f}"
	        msg2 ""
	        msg2 "######################################################"
	        patch -Np1 < "${_f}"
	        echo "Applied your own patch ${_f}" >> "$_where"/last_build_config.log
	      fi
	    done
	  fi
	fi
}

pkgver() {
	# retrieve current version
	cd "${srcdir}/${_vkd3dsrcdir}"

	# version string
	echo $(git describe --long 2>/dev/null || printf "r%s.%s" "$(git rev-list --count HEAD)" "$(git rev-parse --short HEAD)") | sed 's/\([^-]*-g\)/r\1/;s/-/./g;s/vkd3d.//g'
}

prepare() {
	# Cleanups
	rm -fv $_where/last_build_config.log
	cd "${srcdir}/${_vkd3dsrcdir}"

	# Community patches
	if [ -n "$_community_patches" ]; then
      if [ ! -d "$_where/../community-patches" ]; then
        cd "$_where/.." && git clone https://github.com/Frogging-Family/community-patches.git && cd "${srcdir}/${_vkd3dsrcdir}"
      fi
	  _community_patches=($_community_patches)
	  for _p in ${_community_patches[@]}; do
	    ln -s "$_where"/../community-patches/vkd3d-git/$_p "$_where"/
	  done
	fi

	# vkd3d user patches
	_userpatch_target="vkd3d"
	_userpatch_ext="myvkd3d"
	user_patcher

	# Community patches removal
	for _p in ${_community_patches[@]}; do
	  rm -f "$_where"/$_p
	done

	# create new build dirs
	mkdir -p "${srcdir}"/"${_pkgnameprefix}"
	mkdir -p "${srcdir}"/lib32-"${_pkgnameprefix}"
	cd "$_where"
}

build() {
  # Use custom compiler paths if defined
  if [ -n "${CUSTOM_MINGW_PATH}" ]; then
    PATH=${CUSTOM_MINGW_PATH}/bin:${CUSTOM_MINGW_PATH}/lib:${CUSTOM_MINGW_PATH}/include:${PATH}
  fi
  if [ -n "${CUSTOM_GCC_PATH}" ]; then
    PATH=${CUSTOM_GCC_PATH}/bin:${CUSTOM_GCC_PATH}/lib:${CUSTOM_GCC_PATH}/include:${PATH}
  fi

  if [ "$_vkd3d_source" != "https://github.com/HansKristian-Work/vkd3d-proton" ] || [ "$_fork_disable_meson" = "true" ]; then
    cd "${srcdir}/${_vkd3dsrcdir}"
    ./autogen.sh

    export CC='gcc -m32 -mstackrealign'
    export CXX='g++ -m32 -mstackrealign'
    export PKG_CONFIG_PATH='/usr/lib32/pkgconfig'

    cd  "${srcdir}"/lib32-"${_pkgnameprefix}"
    ../${_vkd3dsrcdir}/configure \
      --prefix=/usr \
      --libdir=/usr/lib32

    # make using all available threads
    schedtool -B -n 1 -e ionice -n 1 make -j$(nproc) -C "${srcdir}"/lib32-"${_pkgnameprefix}" || make -j$(nproc) -C "${srcdir}"/lib32-"${_pkgnameprefix}"

    export CC='gcc -m64'
    export CXX='g++ -m64'
    export PKG_CONFIG_PATH='/usr/lib/pkgconfig'

    cd  "${srcdir}"/"${_pkgnameprefix}"
    ../${_vkd3dsrcdir}/configure \
      --prefix=/usr \
      --libdir=/usr/lib

    # make using all available threads
    schedtool -B -n 1 -e ionice -n 1 make -j$(nproc) -C "${srcdir}"/"${_pkgnameprefix}" || make -j$(nproc) -C "${srcdir}"/"${_pkgnameprefix}"
  else
    cd "${srcdir}/${_vkd3dsrcdir}"
    git submodule update --init --recursive

    cd "${srcdir}"

    # 64
    export CC="gcc -m64"
    export CXX="g++ -m64"

    arch-meson "${srcdir}/${_vkd3dsrcdir}" "${srcdir}"/"${_pkgnameprefix}" \
      --libdir lib \
      --buildtype release

    ninja $NINJAFLAGS -C "${_pkgnameprefix}"

    # 32
    export CC='gcc -m32 -mstackrealign'
    export CXX='g++ -m32 -mstackrealign'
    export PKG_CONFIG_PATH='/usr/lib32/pkgconfig'

    arch-meson "${srcdir}/${_vkd3dsrcdir}" "${srcdir}"/lib32-"${_pkgnameprefix}" \
      --libdir lib32 \
      --buildtype release

    ninja $NINJAFLAGS -C lib32-"${_pkgnameprefix}"
  fi
}

_package_vkd3d() {
  provides=("vkd3d=$pkgver")
  conflicts=("vkd3d")

  msg2 'Packaging vkd3d 64...'

  if [ "$_vkd3d_source" != "https://github.com/HansKristian-Work/vkd3d-proton" ] || [ "$_fork_disable_meson" = "true" ]; then
    cd  "${srcdir}"/"${pkgname}"
    make -C "${srcdir}"/"${pkgname}" DESTDIR="${pkgdir}" install
  else
    DESTDIR="$pkgdir" ninja $NINJAFLAGS -C "${_pkgnameprefix}" install
  fi
}

_package_lib32-vkd3d() {
  provides=("lib32-vkd3d=$pkgver")
  conflicts=("lib32-vkd3d")

  msg2 'Packaging vkd3d 32...'

  if [ "$_vkd3d_source" != "https://github.com/HansKristian-Work/vkd3d-proton" ] || [ "$_fork_disable_meson" = "true" ]; then
    cd  "${srcdir}"/"${pkgname}"
    make -C "${srcdir}"/"${pkgname}" DESTDIR="${pkgdir}" install
  else
    DESTDIR="$pkgdir" ninja $NINJAFLAGS -C lib32-"${_pkgnameprefix}" install
  fi

  rm -rf ${pkgdir}/usr/include
  rm -f ${pkgdir}/usr/bin/vkd3d-compiler
}

source /dev/stdin <<EOF
package_$_pkgnameprefix() {
_package_vkd3d
}

package_lib32-$_pkgnameprefix() {
_package_lib32-vkd3d
}
EOF

md5sums=('SKIP')

trap exit_cleanup EXIT
