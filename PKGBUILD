# Maintainer: Raphiel Rollerscaperers <raphielscape@raphielgang.org>
# Contributor: Stephanie Wilde-Hobbs <git@stephanie.is>
# Contributor: Sven-Hendrik Haase <sh@lutzhaase.com>
# Contributor: SanskritFritz (gmail)

pkgname=netdata-git
_gitname=netdata
pkgver=v1.26.0.r113.g4548a54cf
pkgrel=1
pkgdesc="Real-time performance monitoring, in the greatest possible detail, over the web"
url="https://github.com/netdata/netdata/wiki"
arch=('x86_64')
license=('GPL')
options=(!buildflags)
depends=('libmnl' 'libnetfilter_acct' 'zlib' 'judy' 'libuv' 'openssl')
makedepends=('git')
optdepends=('nodejs: for monitoring named and SNMP devices'
            'lm_sensors: for monitoring hardware sensors'
            'iproute2: for monitoring Linux QoS'
            'python: for most of the external plugins'
            'python-psycopg2: for monitoring PostgreSQL databases'
            'python-requests: for monitoring elasticsearch'
            'hddtemp: for monitoring hhd temperature'
            'apcupsd: for monitoring APC UPS'
            'iw: for monitoring Linux as access point')
source=("$_gitname::git+https://github.com/netdata/netdata.git"
        "${_gitname}.tmpfiles"
        "${_gitname}.sysusers")
sha512sums=('SKIP'
            '3f934ddd1f5248f9e11c29050c023d60b76e4098ec9c8d413bb362d43e9242f767fd58310d966076e8400779af8bda2459afcc314b267fcb9f1c84173e14e313'
            'a910809a823ba58ca7bdaa72e8d68427b48f452c1fbb2343fa182ecb0a091a7640e73af24b8ba2fdd90e00aed8ef53b7fccd25cb8f04ca9b9fa6c8e52223ca66')
provides=('netdata')
conflicts=('netdata')
install="$_gitname.install"

pkgver() {
  cd "$_gitname"
  git describe --long | sed 's/\([^-]*-g\)/r\1/;s/-/./g'
}

build() {
  cd "$_gitname"

  CFLAGS+="-falign-functions=32 -fno-math-errno -fno-trapping-math \
            -fstack-protector-strong -funroll-loops"
  CXXFLAGS+="-march=native -mtune=native -falign-functions=32 -fno-math-errno -Wno-unknown-warning-option\
              -fno-trapping-math -fstack-protector-strong -funroll-loops -pipe -fno-plt \
              -fasynchronous-unwind-tables -fno-omit-frame-pointer -Wp,-D_REENTRANT"
  LDFLAGS+="-Wl,-O2,--sort-common,--as-needed,-z,relro,-z,now"
  autoreconf -ivf

  ./configure \
    --prefix="/usr" \
    --sbindir="/usr/bin" \
    --sysconfdir="/etc" \
    --libexecdir="/usr/lib" \
    --localstatedir="/var" \
    --with-zlib --with-math --with-user=netdata
  make
}

package() {
  cd "$_gitname"

  make DESTDIR="$pkgdir" install

  # Remove /var/*, pacman creates it via tmpfiles hook
  rm -vrf "${pkgdir}/var"

  mkdir -p "$pkgdir/etc/netdata"
  chown -R 134:134 "$pkgdir"/etc/netdata
  chown -R 134:134 "$pkgdir"/usr/share/netdata/web

  install -Dm0644 "system/netdata.service" "$pkgdir/usr/lib/systemd/system/netdata.service"
  install -Dm0644 "system/netdata.logrotate" "$pkgdir/etc/logrotate.d/netdata"
  install -Dm0644 "${srcdir}/${_gitname}.tmpfiles" "${pkgdir}/usr/lib/tmpfiles.d/${_gitname}.conf"
  install -Dm0644 "${srcdir}/${_gitname}.sysusers" "${pkgdir}/usr/lib/sysusers.d/${_gitname}.conf"
}

