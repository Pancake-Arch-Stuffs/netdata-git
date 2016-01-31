# Maintainer: SanskritFritz (gmail)

pkgname=netdata-git
_gitname=netdata
pkgver=v0.1.r713.gc10b4e4
pkgrel=1
pkgdesc="Real-time performance monitoring, in the greatest possible detail, over the web."
url="https://github.com/firehol/netdata/wiki"
arch=('i686' 'x86_64')
license=('GPL')
depends=('libmnl' 'libnetfilter_acct' 'zlib')
optdepends=('nodejs: Webbox plugin')
source=("$_gitname::git+https://github.com/firehol/netdata"
        "netdata.service")
backup=('etc/netdata/netdata.conf' 'etc/netdata/charts.d.conf' 'etc/netdata/apps_groups.conf')
install="$pkgname.install"


pkgver() {
	cd "$_gitname"
	git describe --long | sed 's/\([^-]*-g\)/r\1/;s/-/./g'
}

build() {
	cd "$_gitname"

	./autogen.sh
	./configure \
		--prefix="/usr" \
		--sbindir="/usr/bin" \
		--sysconfdir="/etc" \
		--libexecdir="/usr/lib" \
		--localstatedir="/var" \
		--with-zlib --with-math --with-user=netdata \
		CFLAGS="-O3"
	make
}

package() {
	cd "$_gitname"

	make DESTDIR="$pkgdir" install

	touch "$pkgdir/etc/netdata/netdata.conf"
	install -Dm0644 "$srcdir"/netdata.service "$pkgdir"/usr/lib/systemd/system/netdata.service
}

md5sums=('SKIP'
         'f94703d50d019ff8693975c16b8a7fe9')
