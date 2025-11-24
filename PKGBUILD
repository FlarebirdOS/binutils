pkgname=binutils
pkgver=2.45.1
pkgrel=3
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
makedepends=(
    'git'
)
backup=(etc/gprofng.rc)
options=(
    '!emptydirs'
    '!distcc'
    '!ccache'
)
source=(git+https://sourceware.org/git/binutils-gdb.git#commit=48324fde1e284293dd3d570dba597cb644921c92
    gold-warn-unsupported.patch)
sha256sums=(3e2351760a32f1739f0683da1d4b43e74b42e117867225b88c41c07af6aeec9e
    2d430b66f84a19c154725ec535280c493be1d34f1a90f95208dce02fecd0e4e4)

pkgver() {
    cd binutils-gdb

    git describe --abbrev=12 --tags | sed 's/[^-]*-//;s/[^-]*-/&r/;s/-/+/g;s/_/./g'
}

prepare() {
    cd binutils-gdb

    # Turn off development mode (-Werror, gas run-time checks, date in sonames)
    sed -i '/^development=/s/true/false/' bfd/development.sh

    # Creds @Fedora
    # Change the gold configuration script to only warn about
    # unsupported targets.  This allows the binutils to be built with
    # BPF support enabled.
    patch -Np1 < ${srcdir}/gold-warn-unsupported.patch

    mkdir -v flarebird-build
}

build() {
    cd binutils-gdb/flarebird-build

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
    cd binutils-gdb/flarebird-build

    make DESTDIR=${pkgdir} tooldir=/usr install

    # Remove unwanted files
    rm -f ${pkgdir}/usr/share/man/man1/{dlltool,windres,windmc}*
    rm -rf ${pkgdir}/usr/share/doc/gprofng/
}
