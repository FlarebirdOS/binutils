pkgname=binutils
pkgver=2.45
pkgrel=1
pkgdesc="A set of programs to assemble and manipulate binary and object files"
arch=('x86_64')
url="https://www.gnu.org/software/binutils/"
license=(
    'GPL-2.0-or-later'
    'GPL-3.0-or-later'
    'LGPL-2.0-or-later'
    'LGPL-3.0-or-later'
    'GFDL-1.3'
    'FSFAP'
)
groups=('base-devel')
depends=('glibc' 'zlib' 'zstd')
backup=(etc/gprofng.rc)
options=('!emptydirs' '!distcc' '!ccache')
source=(https://ftp.gnu.org/gnu/binutils/${pkgname}-${pkgver}.tar.xz)
sha256sums=(c50c0e7f9cb188980e2cc97e4537626b1672441815587f1eab69d2a1bfbef5d2)

prepare() {
    cd ${pkgname}-${pkgver}

    mkdir -v flarebird-build
}

build() {
    cd ${pkgname}-${pkgver}/flarebird-build

    local configure_args=(
        --sysconfdir=/etc
        --enable-ld=default
        --enable-plugins
        --enable-shared
        --disable-werror
        --enable-64-bit-bfd
        --enable-new-dtags
        --with-system-zlib
        --enable-default-hash-style=gnu
        ${configure_options}
    )

    ../configure "${configure_args[@]}"

    make tooldir=/usr
}

package() {
    cd ${pkgname}-${pkgver}/flarebird-build

    make DESTDIR=${pkgdir} tooldir=/usr install

    rm -rf ${pkgdir}/usr/share/doc/gprofng/
}

