# Maintainer: D357R0Y3R <109698175+D357R0Y3R@users.noreply.github.com>

pkgname=Hardened-Anonymized-DNSCrypt-Proxy
_pkgname=dnscrypt-proxy
pkgver=2.1.14.r107.g6cb6faf8
pkgrel=1
pkgdesc="Eradicate Surveillance From Your Network Stack"
arch=(x86_64)
url="https://github.com/D357R0Y3R/Hardened-Anonymized-DNSCrypt-Proxy"
license=(ISC)
depends=(glibc)
makedepends=(
  git
  go
)
optdepends=('python-urllib3: for generate-domains-blocklist')
provides=(dnscrypt-proxy)
conflicts=(dnscrypt-proxy)
install=$_pkgname.install
# NOTE: LTO breaks reproducibility :(
options=(!lto)
source=(
  git+https://github.com/dnscrypt/$_pkgname.git
  $_pkgname.toml
  $_pkgname.service
)
sha512sums=('SKIP'
            'e1cc2ca7b03b0814df5e218bf151e673a39edb374fdcd9b89d91728cfe0921524192aa7183cc486b969f79ebf05f373e084e2fb2ac17cadf4a81726cf943dc5f'
            '50e6c878115c96e72f6118008e92871957a699d89bd0b85c80af45e6880a30b0832995e4718ab585b086049cc64e2b0759f8f4263ef814d74929933534403f92')

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
  export GOPATH="$srcdir"
  export GOFLAGS="-buildmode=pie -mod=readonly -modcacherw"

  go build -ldflags "-compressdwarf=false -linkmode external" .
}

check() {
  cd $_pkgname
  go test ./...
}

package() {
  local _config
  cd $_pkgname
  # executable
  install -vDm 755 $_pkgname/$_pkgname -t "$pkgdir/usr/bin/"
  # config files
  install -vDm 644 ../$_pkgname.toml "$pkgdir/etc/$_pkgname/$_pkgname.toml"
  for _config in {{allowed,blocked}-{ips,names},{cloaking,forwarding}-rules,captive-portals}.txt; do
    install -vDm 644 $_pkgname/example-$_config "$pkgdir/etc/$_pkgname/$_config"
  done
  # utils
  install -vDm 644 utils/generate-domains-blocklist/*.{conf,txt} -t "$pkgdir/usr/share/$_pkgname/utils/generate-domains-blocklist"
  install -vDm 755 utils/generate-domains-blocklist/generate-domains-blocklist.py "$pkgdir/usr/bin/generate-domains-blocklist"
  # systemd service/socket
  install -vDm 644 ../$_pkgname.service -t "$pkgdir/usr/lib/systemd/system/"
  # license
  install -vDm 644 LICENSE -t "$pkgdir/usr/share/licenses/$_pkgname"
  # docs
  install -vDm 644 {ChangeLog,README.md} -t "$pkgdir/usr/share/doc/$_pkgname"
}
# vim:set ts=2 sw=2 et:
