# vim: set ts=2 sw=2 et:

pkgname=pacman
pkgver=4.2.1
pkgrel=4
pkgdesc="A library-based package manager with dependency support"
arch=('i686' 'x86_64')
url="http://www.archlinux.org/pacman/"
license=('GPL')
groups=('base' 'base-devel')
depends=('bash' 'glibc' 'libarchive>=3.1.2' 'curl>=7.39.0'
         'gpgme' 'pacman-mirrorlist' 'archlinux-keyring')
makedepends=('asciidoc')   # roundup patch alters docs
checkdepends=('python2' 'fakechroot')
provides=('pacman-contrib')
conflicts=('pacman-contrib')
replaces=('pacman-contrib')
backup=(etc/pacman.conf etc/makepkg.conf)
options=('strip' 'debug')
source=(https://sources.archlinux.org/other/pacman/$pkgname-$pkgver.tar.gz{,.sig}
        ensure-matching-database-and-package-version.patch
        pacman.conf.orange
        makepkg.conf)
md5sums=('2a596fc8f723e99660c0869a74afcf47'
         'SKIP'
         'SKIP'
         'SKIP'
         'SKIP')
validpgpkeys=()


prepare() {
  cd "$pkgname-$pkgver"

  patch -p1 -i $srcdir/ensure-matching-database-and-package-version.patch
}

build() {
  cd "$pkgname-$pkgver"

  ./configure --prefix=/usr --sysconfdir=/etc \
    --localstatedir=/var --enable-doc \
    --with-scriptlet-shell=/usr/bin/bash \
    --with-ldconfig=/usr/bin/ldconfig
  make
  make -C contrib
}

check() {
  make -C "$pkgname-$pkgver" check
}

package() {
  cd "$pkgname-$pkgver"

  make DESTDIR="$pkgdir" install
  make DESTDIR="$pkgdir" -C contrib install

  # install Arch specific stuff
  install -dm755 "$pkgdir/etc"
  install -m644 "$srcdir/pacman.conf.orange" "$pkgdir/etc/pacman.conf"

  # set things correctly in the default conf file
  install -m644 "$srcdir/makepkg.conf" "$pkgdir/etc"

  # put bash_completion in the right location
  install -dm755 "$pkgdir/usr/share/bash-completion/completions"
  mv "$pkgdir/etc/bash_completion.d/pacman" "$pkgdir/usr/share/bash-completion/completions"
  rmdir "$pkgdir/etc/bash_completion.d"

  for f in makepkg pacman-key; do
    ln -s pacman "$pkgdir/usr/share/bash-completion/completions/$f"
  done

  install -Dm644 contrib/PKGBUILD.vim "$pkgdir/usr/share/vim/vimfiles/syntax/PKGBUILD.vim"
}
