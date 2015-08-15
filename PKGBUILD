# Maintainer: Dave Reisner <d@falconindy.com>
# Contributor: Andrea Fagiani <andfagiani_at_gmail_dot_com>

pkgname=eclim-juno-git
pkgver=20120808
pkgrel=1
pkgdesc="Brings Eclipse functionality to Vim"
url="http://eclim.org/"
license=('GPL3')
arch=(i686 x86_64)
depends=('vim' 'eclipse>=4.2')
makedepends=('apache-ant' 'python2-sphinx')
optdepends=('eclipse-pdt: Eclipse PHP Development Tools support'
            'eclipse-cdt: Eclipse C/C++ Plugin support'
            'eclipse-dltk-core: Eclipse Dynamic Languagues Toolkit support'
            'eclipse-dltk-ruby: Eclipse Ruby support'
            'eclipse-wtp-wst: Eclipse Web Developer Tools support')
conflicts=('eclim' 'eclim-git')
source=(resources.patch)
install=eclim.install
md5sums=('15d96cc259f9f91b68c25e1f950b9ca8')

_gitroot="git://github.com/ervandew/eclim.git"
_gitname="eclim"

build() {
  cd "$srcdir"
  msg "Connecting to GIT server...."

  if [[ -d $_gitname ]] ; then
    ( cd $_gitname && git pull origin; )
    msg "The local files are updated."
  else
    git clone "$_gitroot"
  fi

  msg "GIT checkout done or server timeout"
  msg "Starting make..."

  rm -rf "$srcdir/$_gitname-build"
  git clone "$srcdir/$_gitname" "$srcdir/$_gitname-build"
  cd "$srcdir/$_gitname-build"

  patch -Np0 < "$srcdir"/resources.patch

  # Manage plugins
  # jdt, ant and maven only require eclipse so they can be enabled without worries
  # might want to edit the _plugins array with those that suit your needs
  # options are : jdt, ant, maven, pdt, cdt, wst, dltk, dltkruby
  _plugins='jdt,ant,maven'

  # Eventually enabling additional plugins, be sure to comment this if you're defining your own
  _plugins=$(pacman -Qs eclipse- | sed -n 's/.*\(pdt\|cdt\|wst\|dltk\(-ruby\)*\).*/\1/p' | tr '\n-' ',\0')$_plugins

  # Get the ANT_HOME environment variable
  source /etc/profile.d/apache-ant.sh

  export ECLIM_ECLIPSE_HOME="/usr/share/eclipse"

  msg2 "Building..."
  ant -Dvim.files=/usr/share/vim/vimfiles \
      -Dplugins=$_plugins \
      build

  msg2 "Deploying..."
  ant -Declipse.local=$pkgdir/usr/share/eclipse \
      -Dvim.files=$pkgdir/usr/share/vim/vimfiles \
      -Dplugins=$_plugins \
      deploy vimdocs

  # fix eclim paths
  sed -i -e "s|$pkgdir||g" $pkgdir/usr/share/vim/vimfiles/eclim/plugin/eclim.vim

  # delete Windows stuff
  find "$pkgdir" -regex ".*bat\|.*cmd\|.*exe" -delete
}

