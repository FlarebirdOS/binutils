pkgname=binutils
pkgver=2.45
pkgrel=2
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
depends=(
    'glibc'
    'jansson'
    'libelf'
    'zlib'
    'zstd'
    )
backup=(etc/gprofng.rc)
options=(
    '!emptydirs'
    '!distcc'
    '!ccache'
)
source=(https://ftp.gnu.org/gnu/binutils/${pkgname}-${pkgver}.tar.xz
    0001-strip-Don-t-treat-fat-IR-objects-as-plugin-object.patch)
sha256sums=(c50c0e7f9cb188980e2cc97e4537626b1672441815587f1eab69d2a1bfbef5d2
    cd1816becb0e992ebc09f2ff66050e4b92fad33f0b7acc833cf513f4e4791d1a)

prepare() {
    cd ${pkgname}-${pkgver}

    # Turn off development mode (-Werror, gas run-time checks, date in sonames)
    sed -i '/^development=/s/true/false/' bfd/development.sh

    # fix https://sourceware.org/bugzilla/show_bug.cgi?id=33246
    patch -Np1 < ${srcdir}/0001-strip-Don-t-treat-fat-IR-objects-as-plugin-object.patch

    mkdir -v flarebird-build
}

build() {
    cd ${pkgname}-${pkgver}/flarebird-build

    local configure_args=(
        --sysconfdir=/etc
        --disable-gdb
        --disable-gdbserver
        --disable-libdecnumber
        --disable-readline
        --disable-sim
        --disable-werror
        --enable-cet
        --enable-colored-disassembly
        --enable-default-execstack=no
        --enable-deterministic-archives
        --enable-gold
        --enable-install-libiberty
        --enable-jansson
        --enable-ld=default
        --enable-new-dtags
        --enable-pgo-build=lto
        --enable-plugins
        --enable-relro
        --enable-shared
        --enable-targets=x86_64-pep,bpf-unknown-none
        --enable-threads
        --with-bugurl=https://github.com/FlarebirdOS/binutils/issues
        --with-lib-path=/usr/lib64:/usr/local/lib64
        --with-pic
        --with-pkgversion="Flarebird Binutils ${pkgver}"
        --with-system-zlib
        ${configure_options}
    )

    ../configure "${configure_args[@]}"

    make -O tooldir=/usr
}

package() {
    cd ${pkgname}-${pkgver}/flarebird-build

    make DESTDIR=${pkgdir} tooldir=/usr install

    # Remove unwanted files
    rm -f ${pkgdir}/usr/share/man/man1/{dlltool,windres,windmc}*
    rm -rf ${pkgdir}/usr/share/doc/gprofng/
}

