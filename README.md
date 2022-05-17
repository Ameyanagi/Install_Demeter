# Manual for installing Demeter in Ubuntu 20.04
Demeter is a package to process and analyze X-ray absorption spectroscopy (XAS).
[Demeter](https://github.com/bruceravel/demeter)

This manual is a modification of David Hughes' guide. 
[Installation of Demeter in Ubuntu 12.04](http://bruceravel.github.io/demeter/documents/SinglePage/demeter_nonroot.html)

I had to make modifications since the ifeffit was removed from Ubuntu 20.04 repository, and some of the packages were updated.

## Installation of apt packages (installation of dependencies)
```bash
sudo apt-get install build-essential git gfortran gnuplot liblocal-lib-perl libx11-dev libncurses5-dev libpng-dev libwxgtk3.0-gtk3-dev libmodule-build-perl libwx-perl xauth xterm pgplot5
```

## Making work directory
```bash
mkdir ~/work
```

## Changing to local environment (to prevent using root privilege)
```bash
mkdir ~/local
echo 'eval $(perl -I$HOME/perl5/lib/perl5 -Mlocal::lib)' >> ~/.bashrc
echo 'export PATH=$HOME/local/bin:$PATH' >> ~/.bashrc
source ~/.bashrc
```

## Setting Environment Variables
```bash
export PREFIX=$HOME/local
export WORK_DIR=~/work
```

## Installation of ifeffit
```bash
cd $WORK_DIR
git clone https://github.com/newville/ifeffit
cd ifeffit
./configure --prefix=$PREFIX
sed -i -e 's/TERMCAP_LIB =.*/TERMCAP_LIB =-L\/usr\/lib\/x86_64-linux-gnu\/ -lncurses/g' src/cmdline/Makefile
make
make install

cd $WORK_DIR/ifeffit/wrappers/perl
cp $PREFIX/share/ifeffit/config/Makefile.PL .
perl Makefile.PL ; make ; make install
```

## Installation of Demeter(dependencies)
```bash
cpan
```
(Enter yes for auto-configuration. Type in below in the cpan window. Then press Ctrl+D)

``` bash
cpan[1]> o conf build_requires_install_policy yes
cpan[2]> o conf prerequisites_policy follow
cpan[3]> o conf commit
```

```bash
cd $WORK_DIR
git clone https://github.com/bruceravel/demeter.git
cd demeter

export PERL5LIB=$WORK_DIR/demeter:$PERL5LIB
perl ./Build.PL
#This will show the dependencies missing

./Build installdeps
```
(Enter y for all the packages)

There will be a error installing mod_perl during the installation of demeter.
You can quit by Crtl+C if there is some error installing libperl.
```
Can't stat `../apache_x.x/src'
Please tell me where I can find your apache src
 [../apache_x.x/src] ../apache_x.x/src
```

If there is dependencies missing, try installing again. The installation will resume by the same command.
```bash
./Build installdeps
```

Make sure to check the dependencies.
```bash
perl ./Build.PL
```

## Installation of Demeter
```bash
./Build
./Build test
./Build install
````

Now it should be installed and ready to be used.

```bash
dathena
dartemis
dhephaestus
denergy
```

X11 over SSH is slow for these app. I would suggest using xrdp or other protocols.

# Updating Demeter

```bash
export WORK_DIR=~/work
export PERL5LIB=$WORK_DIR/demeter:$PERL5LIB
cd demeter
git pull

perl Build.PL

./Build installdeps
./Build
./Build test
./Build install
```


