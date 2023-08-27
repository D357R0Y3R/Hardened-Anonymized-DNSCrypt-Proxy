# Maintainer: BL4CKH47H4CK3R <109698175+BL4CKH47H4CK3R@users.noreply.github.com>

pkgname=Hardened-Anonymized-DNSCrypt-Proxy
_pkgname=dnscrypt-proxy
pkgver=2.1.5.r4.g5ae83c15
pkgrel=1
pkgdesc="Wipe Snoopers Out Of Your Networks"
arch=('x86_64' 'x86_64_v3')
url="https://github.com/BL4CKH47H4CK3R/Hardened-Anonymized-DNSCrypt-Proxy"
license=(ISC)
depends=(glibc)
makedepends=(git go)
optdepends=('python-urllib3: for generate-domains-blocklist')
provides=(dnscrypt-proxy)
conflicts=(dnscrypt-proxy)
install=$_pkgname.install
# NOTE: disabled until gcc is fixed to produce reproducible bytecode for go
options=(!lto)
source=(
	git+https://github.com/dnscrypt/$_pkgname.git
	$_pkgname.toml
	$_pkgname.service
)
sha512sums=('SKIP'
            'd271c52b6cdbe2da157d3242fb25beb83b2d996998c3a33e023782f58b6d0a6ea320afbb6a4f1ea9cdd5e2ed86d98d58bce42497809cb99fd23030de049986cd'
            'a62fe2b5c8e194931a1c3948b262b0a4ab766c1b649431aabe2ec9e527abd23346fd21d1fc0e25783b9137d278d49256e50f9ca8ed456c59e36b48414607bda2')

pkgver() {
	cd "$_pkgname"
	git describe --long | sed 's/\([^-]*-g\)/r\1/;s/-/./g'
}

build() {
	cd $_pkgname/$_pkgname
	export CGO_CPPFLAGS="$CPPFLAGS"
	export CGO_CFLAGS="$CFLAGS"
	export CGO_CXXFLAGS="$CXXFLAGS"
	export CGO_LDFLAGS="$LDFLAGS"
	export GOFLAGS="-buildmode=pie -trimpath -ldflags=-linkmode=external -mod=readonly -modcacherw"
	go build
}

package() {
	local _config
	cd $_pkgname
	# executable
	install -Dm 755 $_pkgname/$_pkgname -t "$pkgdir/usr/bin/"
	# config files
	install -Dm 644 ../$_pkgname.toml "$pkgdir/etc/$_pkgname/$_pkgname.toml"
	for _config in {{allowed,blocked}-{ips,names},{cloaking,forwarding}-rules,captive-portals}.txt; do
		install -Dm 644 $_pkgname/example-$_config "$pkgdir/etc/$_pkgname/$_config"
	done
	# utils
	install -Dm 644 utils/generate-domains-blocklist/*.{conf,txt} -t "$pkgdir/usr/share/$_pkgname/utils/generate-domains-blocklist"
	install -Dm 755 utils/generate-domains-blocklist/generate-domains-blocklist.py "$pkgdir/usr/bin/generate-domains-blocklist"
	# systemd service
	install -Dm 644 ../$_pkgname.service -t "$pkgdir/usr/lib/systemd/system/"
	# license
	install -Dm 644 LICENSE -t "$pkgdir/usr/share/licenses/$_pkgname"
	# docs
	install -Dm 644 {ChangeLog,README.md} -t "$pkgdir/usr/share/doc/$_pkgname"
}
# vim:set ts=2 sw=2 et:
