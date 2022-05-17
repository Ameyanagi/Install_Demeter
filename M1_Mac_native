# Manual for installing Demeter in Mac OS Monterey (M1 chip)
Demeter is a package to process and analyze X-ray absorption spectroscopy (XAS). 
[Demeter](https://github.com/bruceravel/demeter)

This is an installation manual for Mac OS Monterey (M1 Chip). Some modification will be needed to install to intel chip or if there are any further updates to the OS.

This manual is a modification of David Hughes' guide. 
[Installation of Demeter in Ubuntu 12.04](http://bruceravel.github.io/demeter/documents/SinglePage/demeter_nonroot.html)

## The basic strategy of installing Demeter
Because Demeter doesn't have a binary package, it must be installed from the source to work in Mac OS. A MacPorts version of Demeter is available, but it did not work for the latest Mac OS with an M1 chip. This manual will install the package from the source to the local directory.

- Installation of Xcode
-	Installation of dependencies by homebrew
-	Setting up Perl
-	Installation of Ifeffit
-	Installation of Perl wrapper for Ifeffit
-	Installation of dependencies required by Demeter
-	Installation of Demeter

## Updating Xcode
Before installing the Demeter package, Xcode must be installed. Below is a typical command to install Xcode command line tools.

```bash
sudo rm -rf /Library/Developer/CommandLineTools
xcode-select --install
```

## Installation of homebrew dependencies and setting up Perl environment
```bash
brew install kazuakiyama/pgplot/pgplot
brew install perl gnuplot xquartz
brew install openssl wxwidgets wxwidgets@3.0

PERL_MM_OPT="INSTALL_BASE=$HOME/perl5" cpan local::lib
echo 'eval "$(perl -I$HOME/perl5/lib/perl5 -Mlocal::lib=$HOME/perl5)"' >> ~/.zshrc
source ~/.zshrc
```

## Making work directory
```bash
mkdir ~/work
```

## Setting Environment Variables
Chang
```bash
export PREFIX=$HOME/local
export WORK_DIR=~/work
```

## Installation of ifeffit
The path to ncurses had to be patched to build Ifeffit. Please change the lines below if the configurations are different.
The perl wrapper is needed to be patched. (See
[p3.2-ifeffit(Macports)](https://github.com/macports/macports-ports/blob/master/perl/p5-ifeffit/Portfile) for detail)
```bash
cd $WORK_DIR
git clone https://github.com/newville/ifeffit
cd ifeffit
./configure --prefix=$PREFIX
sed -i -e 's/TERMCAP_LIB =.*/TERMCAP_LIB =-L\/opt\/homebrew\/Cellar\/ncurses\/6.3\/lib\/ -lncurses/g' src/cmdline/Makefile
make
make install

cd $WORK_DIR/ifeffit/wrappers/perl
cp -y $PREFIX/share/ifeffit/config/Makefile.PL .
perl Makefile.PL
sed -i -e 's/^PERL_ARCHIVE *= *$/PERL_ARCHIVE = \$(PERL_INC)\/libperl.dylib/g' Makefile
make
install_name_tool -change libifeffit.so $PREFIX/share/ifeffit/libifeffit.so ./blib/arch/auto/Ifeffit/Ifeffit.bundle
make install
chmod 755 $PREFIX/share/ifeffit/libifeffit.so
```

## Installation of Demeter (dependencies)

```bash
cpan
```
(Enter yes for auto-configuration. Type in below in the cpan window. Then press Ctrl+D)

``` bash
cpan[1]> o conf build_requires_install_policy yes
cpan[2]> o conf prerequisites_policy follow
cpan[3]> o conf commit
```

Now you need to install SOAP::Lite manually. Installation of SOAP::Lite fails if you try to install it from the auto-installation package provided by Demeter. The following command will initiate the installation.

```bash
cpan
cpan[1]> install SOAP::Lite
```
Follow the instruction y or default value for most of the choices. (Default value will be ok in most cases.)

Press "q" if the following message appears.

```
Enter `q' to stop search
Please tell me where I can find your apache src
 [../apache_x.x/src]
```

Now you can install the dependencies for Demeter.

```bash
cd $WORK_DIR
git clone https://github.com/bruceravel/demeter.git
cd demeter

export PERL5LIB=$WORK_DIR/demeter:$PERL5LIB
perl ./Build.PL
#This will show the dependencies missing

# export CPPFLAGS="-DwxOSX_USE_QTKIT=0 -D__ASSERT_MACROS_DEFINE_VERSIONS_WITHOUT_UNDERSCORES=1 $CPPFLAGS"
./Build installdeps
```
(Enter y for all the packages)


If dependencies are missing, try installing again. The installation will resume with the same command.
```bash
./Build installdeps
```

There will be error installing wxWidgets. Linking adv can be achieved by following command.
```
chmod +w emacs ~/perl5/lib/perl5/darwin-thread-multi-2level/Alien/wxWidgets/Config/mac_3_1_6_uni_nc_0.pm
emacs ~/perl5/lib/perl5/darwin-thread-multi-2level/Alien/wxWidgets/Config/mac_3_1_6_uni_nc_0.pm
```

Add entry 'adv' after 'aui'
```bash
$Var1 = {
          '_libraries' => {
                            'aui' => {
                                       'dll' => 'libwx_osx_cocoau_aui-3.1.dylib',
                                       'link' => '-lwx_osx_cocoau_aui-3.1'
			                         },
                            'adv' => {
                                       'dll' => 'libwx_osx_cocoau_adv-3.1.dylib',
                                       'link' => '-lwx_osx_cocoau_adv-3.1'
                                     },
```

replace "3.1" by "3.0"
```bash
sed -i -e 's/3\.1/3\.0/g' ~/perl5/lib/perl5/darwin-thread-multi-2level/Alien/wxWidgets/Config/mac_3_1_6_uni_nc_0.pm
```

Then resume installing the dependencies.
```bash
./Build installdeps
```

Make sure to check the dependencies.
```bash
perl ./Build.PL
```

## Installation of Demeter
You can install Demeter after you install the dependencies.

```bash
sed -i -e 's/dylib/bundle/g' DemeterBuilder.pm
cp $WORK_DIR/ifeffit/wrappers/perl/blib/arch/auto/Ifeffit/Ifeffit.bundle ./src/
```

```bash
./Build
./Build test
./Build install
````

It is ready to be used. I had no troubles using dathena, but dartemis showed segmentation fault when I tried to launch. It is probably due somewhere around wxWidgets, but it needs to be investigated.

```bash
dathena
dartemis
dhephaestus
denergy
```

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
