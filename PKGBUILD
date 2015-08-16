# $Id: PKGBUILD 225065 2014-10-20 10:22:18Z andrea $
# Maintainer: Andrea Scarpino <andrea@archlinux.org>
# Contributor: Pierre Schmitz <pierre@archlinux.de>
# Contributor: Ivan Shapovalov <intelfx100@gmail.com>

pkgname=kdebase-workspace-plymouth
_pkgname=kde-workspace
pkgver=4.11.13
_pkgver=4.14.2
pkgrel=1
pkgdesc="Provides the interface and basic tools for the KDE workspace"
arch=('i686' 'x86_64')
url='https://projects.kde.org/projects/kde/kde-workspace'
license=('GPL' 'LGPL' 'FDL')
groups=('kde' 'kdebase')
provides=("kdebase-workspace=$pkgver")
conflicts=('kdebase-workspace')
# note on libxdamage:
# not detected by namcap because libgl depends on it
# but nvidia providing libgl does not depend on libxdamage
depends=("kdelibs>=${pkgver}" 'kdepim-runtime' 'lm_sensors' 'libraw1394'
         'qimageblitz' 'polkit-kde' 'xorg-xprop' 'libxdamage' 'libqalculate'
         'libxklavier' 'xorg-xsetroot' 'libxcomposite' 'libxinerama'
         'xorg-xrdb' 'libxres' 'xorg-xrandr' 'xorg-xmessage' 'libusb-compat'
         'kde-base-artwork' 'xcb-util-renderutil' 'xcb-util-image' 'ttf-font'
         'xcb-util-keysyms' 'xcb-util-wm' 'pciutils' 'glu')
makedepends=('cmake' 'automoc4' 'boost' 'kdebindings-python2' 'networkmanager')
optdepends=('kde-wallpapers: wallpapers for KDE Plasma Workspaces'
            'appmenu-qt: menu applications over dbus')
install="${pkgname}.install"
backup=('usr/share/config/kdm/kdmrc' 'etc/pam.d/kde' 'etc/pam.d/kde-np' 'etc/pam.d/kscreensaver')
source=("http://download.kde.org/stable/${_pkgver}/src/${_pkgname}-${pkgver}.tar.xz"
        'kde.pam' 'kde-np.pam' 'kscreensaver.pam' 'kdm.service' 'kdm.logrotate'
        'etc-scripts.patch' 'terminate-server.patch' 'kdm-xinitrd.patch'
        'khotkeys-qt4.patch' 'kdm-plymouth.patch')
sha1sums=('34dcc710ad8628fefa1cf0fa8eab4efc98ff138f'
          '660eae40a707d2711d8d7f32a93214865506b795'
          '6aeecc9e0e221f0515c6bf544f9a3c11cb6961fe'
          'c6afdf8964b2b2c4809b2e8e694729b2661e29df'
          'd111481284adcca97805f61c8543b3a1175525bf'
          'bbe55f2000217474ce7246f12ee437ceaaf7e9ae'
          'c079ebd157c836ba996190f0d2bcea1a7828d02c'
          'ac7bc292c865bc1ab8c02e6341aa7aeaf1a3eeee'
          'd509dac592bd8b310df27991b208c95b6d907514'
          'aa9d2e5a69986c4c3d47829721ea99edb473be12'
          '599469e821db718dcd2dc2b801e6f524d3f86c84')

prepare() {
        mkdir build

        cd ${_pkgname}-${pkgver}

        # reads the shell scripts in /etc/kde/
        patch -p0 -i "${srcdir}"/etc-scripts.patch
        # FS#26120
        patch -p1 -i "${srcdir}"/kdm-xinitrd.patch
        # FS#39188
        patch -p1 -i "${srcdir}"/khotkeys-qt4.patch

        # KDEBUG#202629
        patch -p0 -i "${srcdir}"/terminate-server.patch

		# plymouth integration
		patch -p0 -i "${srcdir}"/kdm-plymouth.patch
}

build() {
        cd build
        cmake ../${_pkgname}-${pkgver} \
          -DCMAKE_BUILD_TYPE=Release \
          -DKDE4_BUILD_TESTS=OFF \
          -DCMAKE_SKIP_RPATH=ON \
          -DCMAKE_INSTALL_PREFIX=/usr \
          -DWITH_Xmms=OFF \
          -DWITH_libgps=OFF \
          -DPYTHON_EXECUTABLE=/usr/bin/python2 \
          -DWITH_CkConnector=OFF \
          -DWITH_NepomukCore=OFF \
          -DWITH_Soprano=OFF
        make
}

package() {
	cd build
	make DESTDIR="${pkgdir}" install

	install -D -m644 "${srcdir}"/kde.pam "${pkgdir}"/etc/pam.d/kde
	install -D -m644 "${srcdir}"/kde-np.pam "${pkgdir}"/etc/pam.d/kde-np
	install -D -m644 "${srcdir}"/kscreensaver.pam "${pkgdir}"/etc/pam.d/kscreensaver

	install -d -m755 "${pkgdir}"/usr/share/xsessions/
	ln -sf /usr/share/apps/kdm/sessions/kde-plasma{,-safe}.desktop \
      "${pkgdir}"/usr/share/xsessions/
	install -d -m755 "${pkgdir}"/etc/kde/{env,shutdown}

	install -d -g 135 -o 135 "${pkgdir}"/var/lib/kdm
    install -D -m644 "${srcdir}"/kdm.service \
      "${pkgdir}"/usr/lib/systemd/system/kdm.service
    install -Dm644 "${srcdir}"/kdm.logrotate "${pkgdir}"/etc/logrotate.d/kdm
}
