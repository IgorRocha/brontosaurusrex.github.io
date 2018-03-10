---
layout: mine
comments: true
title: stretchbang
published: true
---
<a href="#toc" id="markdown-toc-toc"><img src="{{site.baseurl}}/media/stretchBangLogo.png" style="max-width:1032px; position:relative; left:50%; transform:translate(-50%, 0%); margin-top: -100px; margin-bottom: -110px"></a>
[![stretchbang.md.png](https://cdn.scrot.moe/images/2017/11/10/stretchbang.png)](https://cdn.scrot.moe/images/2017/11/10/stretchbang.png)

# Install Debian 9

[Official iso](https://www.debian.org/distrib/netinst) or [with non-free firmware](https://cdimage.debian.org/cdimage/unofficial/non-free/cd-including-firmware/)

    md5sum *.iso # or
    md5sum -c MD5SUMS
    # firmware-9.1.0-amd64-netinst.iso: OK

Install base system, skip creating root user and sudo should be the automagic default. When asked for additional stuff only select *standard system utilities* and *SSH server* if you so wish.

Boot into cli, install/configure git, sed, vim

    sudo apt install git apt-transport-https sed vim
    git config --global core.editor "vim" # optional

## get stretchbang git and apply configs

    cd && mkdir source && cd source
    git clone https://github.com/brontosaurusrex/stretchbang
    cd stretchbang
    git config credential.helper store # optional
    # git checkout 01   # optionally checkout version tagged as 01
    ./overwriteConfigs  # copy user stuff, themes, fonts, icons ...
    ./fixPaths          # in .wbar and .Xresources
    ./makeUserDirs      # mkdir documents downloads images ...

sources.list with some https power

    sudo cp -b ect/apt/sources.list /etc/apt
    sudo apt update && sudo apt dist-upgrade

Network-manager

    cd source/stretchbang
    sudo cp -b etc/network/interfaces /etc/network

[https://wiki.debian.org/NetworkManager](https://wiki.debian.org/NetworkManager)

### Install packages

    # automatic
    cd ~/source/stretchbang/packs
    sudo ./installLess # or sudo ./installAll

    # or manual
    cd ~/source/stretchbang/packs
    sudo apt install -y $(cat base.list)
    sudo apt install -y --no-install-recommends \
    $(cat baseNoInstallRec.list)
    # libreoffice, gimp, inkscape:
    sudo apt install -y $(cat extra.list)

    # reboot
    systemctl reboot

Should now boot into lightdm / openbox.

[Virtual box guest additions.](https://virtualboxes.org/doc/installing-guest-additions-on-debian/)

---

# Themes, icons, fonts
Arc-Darker for 'Widget', Arc-Dark_ob_border for 'Window Border' in lxappearance. 'Papirus GTK b' for icons. Cuprum is the main font.

Darker version would be Adapta-Nokto for gtk and openbox, 'Breeze Dark Papirus GTK b full' for icons, .Xresources.adaptaColors, 'browser' script for firefox (patched to wbar and ob menu).

edit: Another nice icon set is [luv](https://github.com/Nitrux/luv-icon-theme). With inheritance in index.theme set to

    Inherits=breeze-dark-Papirus-GTK-b_full

or some other Papirus variant.

# wbar

    sudo apt install --no-install-recommends wbar wbar-config
    sudo rm /etc/xdg/autostart/wbar.desktop # start wbar in ob autostart

- wbar does not autohide and will not take place on desktop < fix it with some openbox margins (bottom in this case)
- wbar does not behave very friendly in multi-monitor enviroments.
- wbar config is local floating point character dependent

The easiest way to reload wbar is right-clicking it.

# Compile new version of tint2

This is optional, either run *~/source/stretchbang/compileTint2* or

Install dependencies
[https://gitlab.com/o9000/tint2/wikis/Install#dependencies](https://gitlab.com/o9000/tint2/wikis/Install#dependencies)

    cd ~/source
    git clone https://gitlab.com/o9000/tint2.git
    cd tint2
    # checkout if you want specific version, see page
    mkdir build && cd build
    # sudo apt install cmake
    cmake ..
    make
    # make a soft link in ~/bin,
    # at next login the new tint2 will be started
    ln -s ~/source/tint2/build/tint2 ~/bin/tint2

# Expected behaviour

## printVolCont in tint2
*~/bin/printVolCont* is something that can be used to control ALSA master volume, alternative would be volumeicon-alsa or pnmixer.

## Install firefox addons

    # ./firefoxAddons # or
    cd ~/tmp || exit
    # wget and install uBlock and Scrollbar anywhere
    wget https://addons.mozilla.org/firefox/downloads/latest/ublock-origin/addon-607454-latest.xpi
    wget https://addons.mozilla.org/firefox/downloads/latest/scrollbaranywhere/addon-292702-latest.xpi
    firefox-esr *.xpi
    # yes, yes, restart

If uBlock is to new, workaround might be to install [older version](https://addons.mozilla.org/en-US/firefox/addon/ublock-origin/versions/beta).

## hosts file
[http://someonewhocares.org/hosts/zero/](http://someonewhocares.org/hosts/zero/)

    cd /etc || exit
    # copy/backup original
    sudo cp hosts hosts.bak # or sudo cp hosts{,.bak}
    cp hosts ~/tmp/orig
    cd ~/tmp || exit
    # get the bad-sites host blocking file
    wget http://someonewhocares.org/hosts/zero/hosts -O zero
    # cat them together
    cat zero orig > hosts
    sudo cp -b hosts /etc

## geany
[![geany.png](https://cdn.scrot.moe/images/2017/08/01/geany.png)](https://cdn.scrot.moe/images/2017/08/01/geany.png)

## openbox menu and pipe script
Static menu with a dynamic twist (powered by *pipeMisc* and *pipeSysInfo*).

![menu]({{site.baseurl}}/media/stretchMenu.png)

update: pipeMisc (pipe) now incorporates ~/bin/pipePlaces (places), rename or remove pipePlaces to disable showing it.

# Basic hotkeys

    alt +
    y,x     left/right half
    c       center window x
    v       full width, quite some height
    m       move to next monitor

    meta +
    f11     fullscreen
    f12     toggle compton # ~/bin/toggleCompton

    meta + alt +
    c       center window x and y

    Run *shortcuts* in terminal for more.

# Aliases and ~/bin scripts

    fsize 18       # set urxvt font size
    weather        # weather
    today          # small weather
    pipeSysInfo    # extremly basic sysinfo
    bronto         # asci brontographics
    easyfind file  # find files using find/grep
    findosaurus -h # find files using find (beta)
    duh            # ncdu's lil brother - disk space usage
    lst            # ls by date
    up             # apt update/upgrade
    search         # apt-cache search

# Autologin

    sudo vi /etc/lightdm/lightdm.conf
    # under [Seat:*] enable
    autologin-user=yourUserName

# locale

    locale -a # list available
    sudo dpkg-reconfigure locales # add some

Making [gsimplecal](https://brontosaurusrex.github.io/2017/08/09/gsimplecal-localized/) to start week with your [locale](https://wiki.archlinux.org/index.php/locale).

[https://wiki.debian.org/Locale](https://wiki.debian.org/Locale)
[https://wiki.debian.org/ChangeLanguage](https://wiki.debian.org/ChangeLanguage)

## set keyboard layout

    setxkbmap us
    setxkbmap de

## read keyboard layout

    setxkbmap -query # or
    setxkbmap -query | grep layout # or
    setxkbmap -query | grep layout | tr -d 'layout:' | tr -d ' '

---

# Bleeding edge

## libreoffice (from backports)

    sudo apt install -t stretch-backports libreoffice libreoffice-gtk3
    # xy = language code you want (might be in xy-zw format)
    sudo apt install -t stretch-backports libreoffice-l10n-xy myspell-xy
    # optional icon theme
    sudo apt install -t stretch-backports libreoffice-style-breeze

[Languagetool](https://extensions.libreoffice.org/extensions/languagetool) extension also exists (untested).

## tilix (from backports)

    sudo apt install -t stretch-backports tilix
    cd .config/tilix
    # to use stretchbang config
    dconf load /com/gexperts/Tilix/ < tilix.dconf
    # or run the provided script.

[![cosIcan.md.png](https://cdn.scrot.moe/images/2018/02/10/tilixThumb.png)](https://cdn.scrot.moe/images/2018/02/10/thisIsASingleTilixWindow2.png)

## gimp 2.9.x (appimage)

[https://discuss.pixls.us/t/community-built-software/2137](https://discuss.pixls.us/t/community-built-software/2137)

## mpv

    sudo apt install libncurses5-dev liblua5.1-0.dev devscripts equivs
    cd ~/source
    git clone https://github.com/mpv-player/mpv-build && cd mpv-build
    # rm any old mpv-build(TAB)
    mk-build-deps
    sudo dpkg -i mpv-build-deps(TAB).deb
    sudo apt-get install -f
    # ./use-mpv-release
    ./rebuild -j4
    ln -s ~/source/mpv-build/mpv/build/mpv ~/bin/mpv

## adapta (theme)

[https://github.com/adapta-project/adapta-gtk-theme](https://github.com/adapta-project/adapta-gtk-theme)

    sudo apt install autoconf automake libgdk-pixbuf2.0-dev libglib2.0-dev libsass0 libxml2-utils pkg-config sassc parallel
    cd ~/source
    git clone https://github.com/adapta-project/adapta-gtk-theme
    cd adapta(TAB)
    ./autogen.sh --enable-parallel --disable-gnome
    make
    sudo make install

Left aligned window titles in openbox

    # example:
    cd /usr/share/themes/Adapta-Nokto/openbox-3
    sudo sh -c 'echo "window.label.text.justify: left" >> themerc'

[![cosIcan.md.png](https://cdn.scrot.moe/images/2017/09/05/cosIcanThumb.jpg)](https://cdn.scrot.moe/images/2017/09/05/cosIcan.png)

[Keeping firefox-esr bright.](https://brontosaurusrex.github.io/2017/08/23/per-app-theme) Also check *.Xresources.adaptaColors.*

## mandelbulber2

    firefox \
    https://sourceforge.net/projects/mandelbulber/files/latest/download
    # unpack to ~/source (unp mandelTAB) and read the README
    # For stretch it should look like:
    sudo apt install build-essential libqt5gui5 qt5-default libpng16-16 libpng-dev qttools5-dev qttools5-dev-tools libgomp1 libgsl-dev qtmultimedia5-dev libsndfile1-dev libqt5multimedia5-plugins liblzo2-2 liblzo2-dev
    # cd to where you unpacked
    cd makefiles
    qmake mandelbulber.pro
    make all
    # will take a while
    cd ..
    ./install
    # delete old settings
    rm -rf ~/mandelbulber ~/.mandelbulber # careful

To have a [gtk2-like-look](https://forums.bunsenlabs.org/viewtopic.php?id=4151) consistent with the rest of the system

    sudo apt install qt5-style-plugins libqt5svg5

Into ~/.xsessionrc add

    for qt5 apps
    #export QT_STYLE_OVERRIDE=GTK+
    export QT_QPA_PLATFORMTHEME=gtk2

[![mandel](https://cdn.scrot.moe/images/2017/09/02/mandelThumb.jpg)](https://scrot.moe/search/images/?q=mandelbulber)

## atom.io

    cd ~/tmp && wget https://atom.io/download/deb -O atom.deb
    sudo dpkg -i atom.deb && rm atom.deb

[![atom](https://cdn.scrot.moe/images/2017/09/02/atomMarkdown.png)](https://cdn.scrot.moe/images/2017/09/02/atomMarkdown.png)

## blender

Get one from [https://builder.blender.org/download/](https://builder.blender.org/download/) or try the script

    ./getBlender

which should download/unpack new one to ~/apps/blender. Script should also work for updates, since it will move/backup old version automagically.

Blender configs might be in

    ~/.config/blender/2.79
    # ^ Nuke that if you wish to start fresh.

## sift

    ./getSift # sift to ~/bin

[Grep on steroids](https://sift-tool.org/). Also check ack (should be installed).

# Comments?

wow, [so value](https://github.com/brontosaurusrex/stretchbang/issues).

# Donate

You can support the good couse by sending some zcash (ZEC) to this transparent address:

    t1ZARh9pACR2kp7xVJCtC5Xsw8agcFWZy3G

or ethereum (ETH) to this address:

    0x3be5db568f839d700ec32f81627d3218adccef4a

# TOC
{:.no_toc}

* TOC
{:toc}

### <a href="#">^</a>
{:.no_toc}
