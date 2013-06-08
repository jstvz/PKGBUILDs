# Please report PKGBUILD bugs at
# https://github.com/ystein/archlinux-aur-packages

# Maintainer: Yannik Stein <yannik.stein [at] gmail.com>

pkgname='libgcj12'
pkgver=4.6.4_2
pkgrel=1
pkgdesc="Dynamically load and interpret java class files. Built from binary \
executables available in Debian repositories. Please report PKGBUILD bugs at \
https://github.com/ystein/archlinux-aur-packages."
url=http://gcc.gnu.org/java/
arch=(i686 x86_64)
license=(GPL)
conflicts=(gcc-gcj)

if [ $CARCH = 'i686' ]; then
  _DEBARCH=i386
  md5sums=(37aa19e5e5d8d6b53cd961f16534858a)
else
  _DEBARCH=amd64
  md5sums=(a65ef892b9d31727963477cdcf3e1635)
fi
source=(http://ftp.de.debian.org/debian/pool/main/g/gcj-4.6/${pkgname}_${pkgver//_/-}_${_DEBARCH}.deb)

build() {
  tar xf data.tar.gz
}

package() {
  find -type f -name 'libgcj.so*' -execdir install -Dm755 {} $pkgdir/usr/lib/{} \;

  # find newest library
  NLIB="/"$(cd $pkgdir; find usr/lib -maxdepth 1 -type f -name 'libgcj.so*' | sort --reverse | head -1)
  ln -s $NLIB $pkgdir/usr/lib/libgcj12.so
}
