# Maintainer : Keshav P R <(the.ridikulus.rat) (aatt) (gemmaeiil) (ddoott) (ccoomm)>
# Contributor: Thomas Bächler <thomas@archlinux.org>
# Contributor: Tobias Powalowski <tpowa@archlinux.org>

__pkgname="syslinux"
_pkgname="${__pkgname}-bios"
pkgname="${_pkgname}-elflink-git"

pkgver=20130120
pkgrel=1
arch=('i686' 'x86_64')
pkgdesc="Collection of boot loaders that boot from FAT, ext2/3/4 and btrfs filesystems, from CDs and via PXE - Built for x86 PC BIOS - GIT elflink branch"
url="http://syslinux.zytor.com/"
license=('GPL2')

makedepends=('git' 'python2' 'nasm')
depends=('perl' 'glibc')
optdepends=('perl-passwd-md5:  For md5pass'
            'perl-digest-sha1: For sha1pass'
            'mtools:           For mkdiskimage and syslinux support'
            'gptfdisk:         For GPT support'
            'util-linux:       For isohybrid')

backup=('boot/syslinux/syslinux.cfg')
# install="${_pkgname}.install"

conflicts=("${__pkgname}" "${_pkgname}")
provides=("${__pkgname}" "${_pkgname}")

options=('!strip' 'docs' '!libtool' 'emptydirs' 'zipman' '!purge' '!makeflags')

source=('syslinux-dont-build-dos-windows-diag-targets.patch'
        'syslinux-install_update'
        'syslinux.cfg')

sha1sums=('0114aa84901e107dacc2a06b5e8ae9dde436d9a9'
          '1b92d1b8e065e6fe6d875e0acf31efddbc13e8ab'
          '4ad6874a8fb0468108236fcc1b23d5deff7a4ced')

_gitroot="git://git.zytor.com/syslinux/syslinux.git"
_gitname="${__pkgname}"
_gitbranch="elflink"

_update_git() {
	
	cd "${srcdir}/"
	
	msg "Connecting to GIT server...."
	
	if [[ -d "${srcdir}/${_gitname}/" ]]; then
		cd "${srcdir}/${_gitname}/"
		git reset --hard
		git fetch
		git checkout "${_gitbranch}"
		git merge "remotes/origin/${_gitbranch}"
		msg "The local GIT repo has been updated."
	else
		git clone "${_gitroot}" "${_gitname}"
		cd "${srcdir}/${_gitname}/"
		git checkout "${_gitbranch}"
		msg "GIT checkout done or server timeout"
	fi
	
	echo
	
}

build() {
	
	_update_git
	
	rm -rf "${srcdir}/${_gitname}_build" || true
	cp -r "${srcdir}/${_gitname}" "${srcdir}/${_gitname}_build"
	
	cd "${srcdir}/${_gitname}_build"
	
	## Unset all compiler FLAGS
	unset CFLAGS
	unset CPPFLAGS
	unset CXXFLAGS
	unset LDFLAGS
	
	## Fix Makefiles
	rm -f "${srcdir}/${_gitname}_build/libinstaller/advconst.h"
	ln -s ../com32/include/syslinux/advconst.h "${srcdir}/${_gitname}_build/libinstaller/advconst.h"
	
	patch -Np1 -i "${srcdir}/syslinux-dont-build-dos-windows-diag-targets.patch" || true
	echo
	
	make PYTHON="python2" spotless
	echo
	
	make PYTHON="python2"
	echo
	
	make PYTHON="python2" installer
	echo
	
}

package() {
	
	cd "${srcdir}/${_gitname}_build/"
	
	make INSTALLROOT="${pkgdir}/" BINDIR="/usr/bin/" SBINDIR="/usr/sbin/" MANDIR="/usr/share/man" AUXDIR="/usr/lib/syslinux/bios/" install
	echo
	
	install -D -m0644 "${srcdir}/syslinux.cfg" "${pkgdir}/boot/syslinux/syslinux.cfg"
	
	## Do not install the installer script, not yet updated for v5.00 aka elflink branch
	# install -D -m0755 "${srcdir}/syslinux-install_update" "${pkgdir}/usr/sbin/syslinux-install_update"
	
}
