# Maintainer: Caspar Verhey <caspar at verhey dot net>
# Contributor: Seth Fulton  <seth@sysfu.com>
# Contributor: Aaron Griffin <aaron@archlinux.org>
# Contributor: judd <jvinet@zeroflux.org>
# Contributor: benetnash <benetnash@mail.icpnet.pl>
# Contributor: Thomas Haider <t.haider@vcnc.org>
pkgname=openssh-hpn
pkgver=6.6p1
pkgrel=1
pkgdesc='A Secure SHell server/client with High Performance patch'
arch=('i686' 'x86_64')
url="http://www.psc.edu/networking/projects/hpn-ssh/"
license=('custom:BSD')
depends=('krb5' 'openssl' 'libedit')
optdepends=('x11-ssh-askpass: input passphrase in X without a terminal')
provides=('openssh')
conflicts=('openssh')
backup=('etc/ssh/ssh_config' 'etc/ssh/sshd_config' 'etc/pam.d/sshd' 'etc/conf.d/sshd')

_pkgname=openssh
_hpn_ver='hpnssh14v5.diff'
_hpn_patch="${_pkgname}-${pkgver}-${_hpn_ver}"

source=("ftp://ftp.openbsd.org/pub/OpenBSD/OpenSSH/portable/${_pkgname}-${pkgver}.tar.gz"
	${_hpn_patch}.gz::"http://sourceforge.net/projects/hpnssh/files/HPN-SSH%2014.5%206.6p1/${_hpn_patch}.gz/download"
	'sshdgenkeys.service'
	'sshd.service'
	'sshd@.service'
	'sshd.socket'
        'sshd.confd'
        'sshd.pam')

md5sums=('3e9800e6bca1fbac0eea4d41baa7f239'
        'ff122e6b1fc1010f47329d156750f3eb'
	'1949e417ac269b8467f325c956c6d89d'
	'4bbc5818d93a2ad336d7191ed35c69f7'
	'fdc4a47b4b8d0f0e2c395c9edcee8c14'
        '76f52c66fb3193f301fe0a666e047ab1'
        'e2cea70ac13af7e63d40eb04415eacd5'
	'0f494f12464e844654c53ac5da2323a5')

build() {
    cd ${srcdir}/${_pkgname}-${pkgver}
    patch -p1 < ../${_hpn_patch}

    ./configure \
            --prefix=/usr \
            --libexecdir=/usr/lib/ssh \
            --sysconfdir=/etc/ssh \
            --with-privsep-user=nobody \
            --with-md5-passwords \
            --with-pam \
            --with-mantype=man \
            --with-xauth=/usr/bin/xauth \
            --with-kerberos5=/usr \
            --with-ssl-engine \
            --with-libedit=/usr/lib \
            --disable-strip
    make 
}

package() {
    cd "${srcdir}/${_pkgname}-${pkgver}"

    make DESTDIR="${pkgdir}" install

    install -Dm644 ../sshd.pam "${pkgdir}"/etc/pam.d/sshd
    install -Dm644 ../sshdgenkeys.service "${pkgdir}"/usr/lib/systemd/system/sshdgenkeys.service
    install -Dm644 ../sshd.service "${pkgdir}"/usr/lib/systemd/system/sshd.service
    install -Dm644 ../sshd@.service "${pkgdir}"/usr/lib/systemd/system/sshd@.service
    install -Dm644 ../sshd.socket "${pkgdir}"/usr/lib/systemd/system/sshd.socket
    install -Dm644 ../sshd.confd "${pkgdir}"/etc/conf.d/sshd
    install -Dm644 LICENCE "${pkgdir}/usr/share/licenses/${pkgname}/LICENCE"

    rm "${pkgdir}/usr/share/man/man1/slogin.1"
    ln -sf ssh.1.gz "${pkgdir}"/usr/share/man/man1/slogin.1.gz

    #additional contrib scripts that we like
    install -Dm755 contrib/findssl.sh "${pkgdir}"/usr/bin/findssl.sh
    install -Dm755 contrib/ssh-copy-id "${pkgdir}"/usr/bin/ssh-copy-id
    install -Dm644 contrib/ssh-copy-id.1  "${pkgdir}"/usr/share/man/man1/ssh-copy-id.1

    # PAM is a common, standard feature to have
    sed \
            -e '/^#ChallengeResponseAuthentication yes$/c ChallengeResponseAuthentication no' \
            -e '/^#UsePAM no$/c UsePAM yes' \
            -i "${pkgdir}"/etc/ssh/sshd_config
}
