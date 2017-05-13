# Maintainer: David Manouchehri
# Contributor: Tony Lambiris <tony@criticalstack.com>

_gitname=PF_RING
pkgbase="${_gitname,,}-git"
pkgname=("pf_ring-kernel-git" "pf_ring-userland-git")
# pkgname=("pfring-kernel-git" "pfring-userland-git" "pfring-drivers-git")
_gitbranch=dev
_gitauthor=ntop
pkgdesc="High-speed packet processing framework (dev branch)"
url="https://github.com/${_gitauthor}/${_gitname}"
license=('LGPL')
source=("git://github.com/${_gitauthor}/${_gitname}#branch=${_gitbranch}")
depends=('')
makedepends=('git')
# conflicts=("${_gitname}")
# provides=("${_gitname}")
sha512sums=('SKIP')
arch=('aarch64' 'armv6h' 'armv7h' 'i686' 'x86_64') # arch=('any')
pkgver=v6.0.3.r1094.g9e72191
pkgrel=1
# sha512sums_x86_64=('')
# sha512sums_i686=('')
# source_x86_64=('')
# source_i686=('')

pkgver() {
	cd "${srcdir}/${_gitname}"
	(
		set -o pipefail
		git describe --long 2>/dev/null | sed 's/\([^-]*-g\)/r\1/;s/-/./g' ||
		printf "r%s.%s" "$(git rev-list --count HEAD)" "$(git rev-parse --short HEAD)"
	)
}

build() {
	cd "${srcdir}/${_gitname}/kernel"
	./configure --prefix=/usr
	sed -i "s/INSTDIR\ \:=\ \$(DESTDIR)/INSTDIR := \$(DESTDIR)\/usr/g" Makefile
	make

	cd "${srcdir}/${_gitname}/drivers/intel/e1000e/e1000e-3.2.7.1-zc/src/"
	make

	# cd "${srcdir}/${_gitname}/drivers/intel/igb/igb-5.3.3.5-zc/src/"
	# make

	cd "${srcdir}/${_gitname}/drivers/intel/ixgbe/ixgbe-4.1.5-zc/src/"
	make

	cd "${srcdir}/${_gitname}/drivers/intel/i40e/i40e-1.5.18-zc/src/"
	make

	cd "${srcdir}/${_gitname}/drivers/intel/fm10k/fm10k-0.20.1-zc/src/"
	make

	cd "${srcdir}/${_gitname}/userland"
	./configure --prefix=/usr
	make
}

package_pf_ring-kernel-git() {
	pkgdesc+=" - kernel module"
	makedepends+=('linux-lts-headers' 'make' 'binutils' 'flex' 'bison')
	depends+=('linux-lts' 'numactl')
	conflicts+=("pfring-kmod-dev-git" "pfring-kmod-git")
	provides+=("pfring-kmod-dev-git")

	cd "${srcdir}/${_gitname}/kernel"
	mkdir -p "${pkgdir}/usr/include/linux"
	make DESTDIR="${pkgdir}" install
}


package_pf_ring-userland-git() {
	pkgdesc+=" - userland tools"
	makedepends+=('parallel')

	cd "${srcdir}/${_gitname}/userland"
	make DESTDIR="${pkgdir}" install
	mkdir -p "${pkgdir}/usr/bin"
	cd "${pkgdir}/usr/bin"
	find "${srcdir}/${_gitname}/userland/examples/" \
		"${srcdir}/${_gitname}/userland/examples_zc/" -executable -type f \
		-print0 | parallel -0 'cp -v {} ./{/}'
}

package_pf_ring-drivers-git() {
	pkgdesc+=" - kernel driver modules"
	makedepends+=('linux-lts-headers' 'make' 'binutils' 'flex' 'bison')
	depends+=('linux-lts' 'numactl')
	conflicts+=("pfring-kmod-dev-git" "pfring-kmod-git")
	provides+=("pfring-kmod-dev-git")

	cd "${srcdir}/${_gitname}/drivers"
	make DESTDIR="${pkgdir}" install
}

# build() {
# 	cd "${srcdir}/${_gitname}"
# 	# ./autogen.sh
# 	# ./configure --prefix=/usr
# 	make
# }

# package() {
# 	cd "${srcdir}/${_gitname}"
# }

# vim:set et sw=2 sts=2 tw=80:
