GnuGK 3.5 CVS static build

This is an apt repository.

It can be cloned locally and referred to directly:

    git clone https://github.com/sous/gnugk-precise
    ( echo deb file://`pwd`/gnugk-precise precise main
      echo deb-src file://`pwd`/gnugk-precise precise main ) > /etc/apt/sources.list.d/gnugk-precise.list

Either way, the gpg key for this repo can be imported using:

    curl https://raw.github.com/sous/gnugk-precise/master/apt-key.gpg | sudo apt-key add

Alternatively, this is codified in this chef cookbook: https://github.com/sous/gnugk-cookbook

