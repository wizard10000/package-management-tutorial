# package-management-tutorial
tutorial on package management readme for debian community discord

Software management in Debian is kind of a big topic so we're just going to hit the high points - each tool we use to manage software is well-documented and you can get a lot more information by looking at the application's man page and Debian's wiki.

* tools you can use and the difference between them

There are several tools you'd use to manage Debian packages, we'll touch on them here.  If you run GNOME or KDE I'm sure you're aware of the software centers in those desktop environments, we'll share some more tools -

apt.  apt is the primary interactive interface used for package management.  apt is designed to be an interactive tool and is not recommended for use in scripts.  apt has several features you'll find helpful - it can install a local .deb while also installing any dependencies that may be required if they're available.

apt-get.  apt-get is the stable interface for apt and is recommended in scripts.  Note that apt and apt-get don't have feature parity so they're not completely interchangeable; for instance while apt can install a local .deb apt-get isn't able to do that.

aptitude.  aptitude has both a command line and a text user interface and has features that some will find useful.  aptitude will stop you, offer solutions and ask for input if you try to install a package that would break other packages.  aptitude also autoremoves automatically.

nala.  nala is an alternative to apt that can do parallel downloads and also autoremoves automatically.

gdebi.  gdebi can also install local .debs and offers a graphical component (gdebi-gtk) that can integrate with graphical file managers to allow you to right-click a .deb and install it.

synaptic.  synaptic is a graphical package management tool with extensive search and filtering capabilities.

dpkg.  Every tool mentioned above is a frontend for dpkg, which actually installs the software.  dpkg is an extremely powerful tool and you can use it to install a local .deb but it *will not* install any dependencies that may be required.

* don't break debian - being careful with foreign packages

Before we go much further with package management we should probably touch on Don't Break Debian, which has a whole web page dedicated to the concept - https://wiki.debian.org/DontBreakDebian

The reason enabling foreign repositories can be dangerous is that the foreign repository can break Debian if it overwrites core components.  Some foreign repositories are safe, some are not and it's the responsibility of the end user to determine whether that proposed foreign repo is safe to add to Debian.

Installing single .deb package instead of mapping foreign repositories can be much safer.  If Debian can't meet a package's dependencies the package will fail to install without breaking anything.  We'll touch on dependencies a little more later in this article.

* apt update - keeping your local database current

The first thing we should do when checking for software updates is update your local package database.  We do that by running `sudo apt update` What this does is download current package information from Debian repositories so that apt has accurate data to work with.

If you want to update and upgrade with a one-liner `sudo apt update --upgrade` will do the trick for you.

* apt upgrade - installing the upgrade

Next, for our upgrade we'd run `sudo apt upgrade` which will download and install any upgrades available in Debian respoitories.  

There are two kinds of upgrade commands, apt upgrade and apt full-upgrade.  The difference between the two is that a full-upgrade does what Debian calls "smart removal" of any packages that are no longer needed by the system.  Normally you'd do a full-upgrade during a release upgrade such as from Debian 13 to Debian 14 when it's released.  Debian recommends against using full-upgrade in their Testing and Unstable builds because it it can remove packages you may want to keep.

* installing software

So how can I install software with apt?  If the package is in Debian's repositories all you'd need to do is `sudo apt install <package-name>` If you've got a local .deb you want to install you'd `cd` to the directory where you downloaded the .deb and then do `sudo apt install ./package-name.deb`

* removing/purging/autoremoving software

If you decide you want to remove a package you'd use `sudo apt remove <package-name>` There are several ways to do this, though - a simple `apt remove` will leave any configuration files behind, `apt purge` will remove the package along with any configuration files and `apt autopurge` will purge the package and also autoremove any dependencies that are no longer required.

* searching - how to figure stuff out

Debian has several resources available to help you manage your software.  We'll use synaptic as examples here so you can see how this stuff works.

packages.debian.org will be your primary resource for learning about which version of which package is available in Debian - with most Debian web resources you can search using the site name and then the package name so if we do https://packages.debian.org/synaptic we land on a page that tells us which versions are available in which Debian build.  You can drill down and learn about any package dependencies, find a list of files contained in the .deb or even download the .deb and install it locally.

If you want to know what the current status of a package is or why a package hasn't been upgraded you can use Debian's package tracker - for example https://tracker.debian.org/synaptic tells us what the current status of that package is, where it's available and if it hasn't been migrated to Testing it'll give the reason why.

You can learn about dependencies using `apt depends <package-name>` or `apt rdepends <package-name>`.  apt depends will tell you which packages are dependencies, recommends and suggests for <package-name> 

wizard@laptop 17:08:59 $ apt depends synaptic

synaptic

  Depends: libapt-pkg7.0 (>= 1.9.0)
  
  Depends: libc6 (>= 2.38)
  
  Depends: libgcc-s1 (>= 3.0)
  
  Depends: libgdk-pixbuf-2.0-0 (>= 2.22.0)
  
  Depends: libglib2.0-0t64 (>= 2.12.0)
  
  Depends: libgtk-3-0t64 (>= 3.21.5)
  
  Depends: libpango-1.0-0 (>= 1.14.0)
  
  Depends: libstdc++6 (>= 13.1)
  
  Depends: libvte-2.91-0 (>= 0.49.92)
  
  Depends: hicolor-icon-theme
 |
 Depends: polkitd
 
  Depends: <policykit-1>
 |
 Depends: pkexec
 
  Depends: <policykit-1>
  
  Recommends: libgtk3-perl
  
  Recommends: xdg-utils
  
  Suggests: dwww
  
  Suggests: <deborphan>
  
  Suggests: apt-xapian-index
  
  Suggests: tasksel
  
  Suggests: software-properties-gtk
  
  remembering that by default depends and recommends are installed, suggests are not.
  
  apt rdepends lists what they call reverse dependencies - a list of packages which depend on <package-name> 
  
wizard@laptop 17:09:05 $ apt rdepends synaptic

synaptic

Reverse Depends:

 |Suggests: apt
 
  Recommends: task-xfce-desktop
  
  Recommends: task-phosh-desktop
  
  Recommends: task-mate-desktop
  
  Recommends: task-lxqt-desktop
  
  Recommends: task-lxde-desktop
  
  Recommends: task-lomiri-desktop
  
  Recommends: task-gnome-flashback-desktop
  
  Suggests: mate-menu
  
  Suggests: lxqt-config
  
  Recommends: cinnamon-desktop-environment
  
  so you can see that most Debian desktop environments recommend synaptic (except for GNOME and KDE, which have their own software management tool).
  
  If you want to learn which package provides a particular file there are two ways to do it, depending on whether the package is installed.  If the package is installed you can just use for example, dpkg -S synaptic and you can see the output here - there was a lot of output but the package name is in the left column 
  
wizard@laptop 17:10:29 $ dpkg -S synaptic

synaptic: /usr/share/synaptic/html/figures/synaptic-packagedetails.png

synaptic: /usr/share/applications/synaptic.desktop

synaptic: /usr/share/locale/ml/LC_MESSAGES/synaptic.mo

synaptic: /usr/share/locale/lt/LC_MESSAGES/synaptic.mo

If the package is not installed you'd need to install `apt-file`, run `sudo apt-file update` (you only have to do this once) and then do an `apt-file search <filename>` and apt will return a list of packages that contain that file.

* and last, maintaining apt's package cache

apt takes pretty good care of itself but any package you install using apt gets cached in /var/cache/apt/archives and over time it can take up a fair amount of disk space.  `sudo apt clean` will empty the package cache and `sudo apt autoclean` will clean the cache but only remove stale packages that can no longer be downloaded.


