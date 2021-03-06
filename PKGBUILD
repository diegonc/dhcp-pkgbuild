# Contributor: Daniel Isenmann <daniel @archlinux.org>

pkgbase=dhcp
pkgname=('dhcp' 'dhclient')

# separate patch levels with a period to maintain proper versioning.
# XXX: newer version won't work, yet
pkgver=3.1.3
_pkgver=3.1.3

pkgrel=1
arch=('i686')
license=('custom:isc-dhcp')
url="https://www.isc.org/software/dhcp"
_patches=(
	Makefile.dpatch
	dhclient-script-exit-status.dpatch
	dhclient.c.stale-pids.dpatch
	dhcpd-chdir.dpatch
	dhclient-chdir.dpatch
	remove-excessive-junk.dpatch
	ignore-invalid-interfaces.dpatch
	hurd.dpatch
	netmask-overflow.dpatch
	server-clientid-crash.dpatch
	remove-usr-references.patch
	ifconfig-binary-name.patch )

_ldap_patches=(
	dhcp-3.1.0-ldap-docs.dpatch
	dhcp-3.1.0-ldap-code.dpatch
	dhcp-3.1.0-ldap-fix-sigpipe.dpatch
	dhcp-3.1.0-ldap-fix-memory-leak.dpatch
	remove-paths-from-site.h.patch )

source=(http://ftp.isc.org/isc/${pkgname}/${pkgname}-${_pkgver}.tar.gz
        dhcpd dhcp.conf.d "${_patches[@]}" "${_ldap_patches[@]}" )

md5sums=('6ee8af8b283c95b3b4db5e88b6dd9a26'
         'df22cffa7d7415ece7bb025b7bf774dd'
         '49da3192e5c885e3c7d02f447c2dea5e'
         '0a4a17caa3cf655ee6717165cc622530'
         'f50bae281ca63cc0034cc1ec6d5bc3c3'
         'ac1c16dd7cdba336ed747b724195ee41'
         '4ab7924171e39bc62d0c3f8e9b304a63'
         '425abbefd031c8cfd46e59723109aa86'
         '713489e986196259ded5e54c1861fe3e'
         '20ba3e4c78d87f556edbf66fc3acd0cc'
         '5ad5a4e564ec0da6420c5ed66197ae7f'
         'b5f0b7bc38ed349a00850b6df7482809'
         '8fe3864b1adb2c838342aee14bd2a385'
         '3209d3a27f77c9b88621dc1defe42da0'
         '1db8087ef7af351bcf3a0103631756be'

         'b6fc2286f97cc7d2d50e6e4582b43b78'
         '67510d86c180f77ba979549547039da6'
         'dc89374074500052e271e5b2305c51bd'
         '6c6fbfdb71a833568ce93d79295bad02'
         '84ea5e76104c81a86f9a0316267914f4')

build() {
  cd ${srcdir}/${pkgname}-${_pkgver}

  for _patch in "${_patches[@]}"; do
    patch -i "$srcdir/$_patch" -p1
  done
## Uncomment if you really need LDAP support
#  for _patch in "${_ldap_patches[@]}"; do
#    patch -i "$srcdir/$_patch" -p1
#  done

  ./configure gnu

  make
}

package_dhcp(){
  pkgdesc="A DHCP server, client, and relay agent"
  depends=('openssl>=0.9.8a' libldap)
  backup=('etc/conf.d/dhcp')
  install=dhcp.install
  
  cd ${srcdir}/${pkgname}-${_pkgver}
  cd work.gnu
  make -C server   DESTDIR=${pkgdir} install
  make -C relay    DESTDIR=${pkgdir} install
  make -C dhcpctl  DESTDIR=${pkgdir} install
  make -C omapip   DESTDIR=${pkgdir} install
  make -C dst      DESTDIR=${pkgdir} install
  make -C common   DESTDIR=${pkgdir} install
  make -C minires  DESTDIR=${pkgdir} install
  cd ..
  install -D -m755 ${srcdir}/dhcpd ${pkgdir}/etc/rc.d/dhcpd
  install -D -m644 ${srcdir}/dhcp.conf.d ${pkgdir}/etc/conf.d/${pkgbase}
  mkdir -p ${pkgdir}/var/state/dhcp

  # install dhcpd.conf
  install -m644 -D server/dhcpd.conf ${pkgdir}/etc/dhcpd.conf.example

  # install license
  install -m644 -D LICENSE ${pkgdir}/share/licenses/dhcp/LICENSE
}

package_dhclient(){
  pkgdesc="dhclient is standalone client from the dhcp package"
  depends=('bash')

  cd ${srcdir}/${pkgname}-${_pkgver}
  cd work.gnu
  make -C client DESTDIR=${pkgdir} install
  cd ..

  # install dhclient.conf
  install -m644 -D client/dhclient.conf ${pkgdir}/etc/dhclient.conf.example

  # install dhclient-script
  install -m755 -D client/scripts/gnu ${pkgdir}/sbin/dhclient-script

  # install license
  install -m644 -D LICENSE ${pkgdir}/share/licenses/dhclient/LICENSE
}
