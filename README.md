debosh
======

Instant debian packaging for Perl modules and scripts.

If you want to deploy your Perl software on Ubuntu, you may find this small tool useful.
The key concept of `debosh` is to make packaging of simple software as easy as possible.

Installation
------------

You need to have the following packages installed:

* debhelper
* fakeroot
* git-core
* libfile-type-perl
* libfile-slurp-perl
* libfile-spec-perl
* libfile-temp-perl
* liblist-moreutils-perl
* libmodule-info-perl
* libyaml-perl
* rsync
* subversion

To install `debosh` on your system you have to make a package for it.
Let's use `debosh` to package itself!

    git clone git://github.com/komarov/debosh.git debosh
    cd debosh
    bin/debosh
    cd debian
    sudo dpkg -i debosh*.deb

How it works
------------

If you run `debosh` without arguments, it assumes that it is being run in the root directory of your working copy (only Git and Subversion are supported). 
`debosh` tries to determine the repo url to checkout a clean working copy in a tmp dir, where all the magic happens.

If you don't have a local working copy, you can provide repo url as an argument:

    debosh --url git://github.com/komarov/debosh.git

Alternatively, you can force `debosh` to use current directory as a source directory:

    debosh --dirty

as a result, you package will have `~dirty` suffix in its version number (see *changes* on version numbers).

What is expected of your source
-------------------------------

Your root directory has to have these two files:

* changes
* meta.yaml

Also your source dir has to have a specific layout.
The following directories are recognized and their content is copied to target directories as described below:

* bin -> /usr/local/bin/
* etc -> /etc/
* lib -> /usr/share/perl5/
* opt -> /opt/
* sbin -> /usr/local/sbin/
* share -> /usr/local/share/
* var -> /var/

If `t` directory is present, the tests in it will be run before proceeding further.
Use `--no-tests` option to skip this step.

If `actions` directory is present and contains some of `preinst`, `postinst`, `prerm` or `postrm` files, 
these files becomes package maintainer scripts. 
See also [Debian Policy Manual](https://www.debian.org/doc/debian-policy/ch-maintainerscripts.html). 

### changes

`debosh` parses `changes` to get the version number, actually it just takes the first line and checks the format.

Sample changes file:

    0.03
    - fixed a nasty bug
    - improved documentation

    0.02
    - added feature XXX

    0.01
    initial release

### meta.yaml

meta.yaml is a YAML file with the following fields:

* package - package name (required)
* description - short description of your package (required)
* maintainer - Your Name <your@email> (`$ENV{DEBFULLNAME}` and `$ENV{DEBEMAIL}` are used as default)
* requires - a hash of required packages (package name is a key, its version is a corresponding value, use zero if you don't want to fix the version)
* conflicts - a hash of the same format as `requires`

Perl specifics
--------------

Checks Perl modules in `lib` and Perl scripts in `bin` to find out their dependencies automagically (up to package names, but not to their versions).

In `--strict` mode these automatically obtained results are compared to the `requires` section of `meta.yaml`. 

Caveats
-------

It is supposed that `debosh` is used on the same OS version as your target system.

So it is generally a bad idea to install packages made on one version of Ubuntu to boxes with other Ubuntu versions.

Author
------

Oleg Komarov <komarov.om@gmail.com>

Contributors
------------

* Elena Bolshakova <helena@cpan.org>
* Sergey Zhuravlev <zhurs@ya.ru>

License
-------

debosh is free software; you can redistribute it and/or modify it
under the same terms as Perl itself.
