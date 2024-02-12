# cygwin_use_gui_app

## 1 cygwin installer download

Go to the official website of cygwin and download the [installer](https://www.cygwin.com/). 
For 64-bit computers, choose `setup-x86_64.exe`.

## 2 Install base package

### 2.1 Choose to install from the Internet

![choose_to_install_from_the_internet](./img/choose_to_install_from_the_internet.png)

### 2.2 Select the software package you need to install

When we enter, we first choose the `best` one. At this time, the installer will 
help us select some software to ensure the basic operation of the system.

![we_chose_best_to_install](./img/we_chose_best_to_install.png)

Since we need to compile and execute some software, we choose to install tools. 
In order to avoid unnecessary trouble, we can select both `compilation` and `build tools`.

![choose_devel](./img/choose_devel.png)

Our program requires `perl5`, and some programs require `python` to execute. To 
avoid trouble in the future, here we install them all completely.

![perl_and_python_install](./img/perl_and_python_install.png)

Similarly, our GUI program requires the `X11` execution environment, so `X11` is 
also selected.

![x11_selected](./img/x11_selected.png)

Search for `gcrypt`, and then select all the searched software packages 
(double-click to select, do not double-click the category title, directly 
double-click the specific package, otherwise install all the software under the 
title, this is not what we need.)

![gcypt_selected](./img/gcypt_selected.png)

Search for `pinyin` and install the relevant components. This is related to the 
CJK input method and will be needed by users in East Asian countries.

![ibus_pinyin](./img/ibus_pinyin.png)

### 2.3 Perform first installation

After we select, some software may have compatibility issues. Let's 
ignore it here and accept the conflict resolution provided by the software by 
default. If you need to install other software later, please reinstall it. 
Because we can re-run the installer, and then incrementally install the 
software we want to install later.

![solve_confliect](./img/solve_conflict.png)

The first installation takes a long time, so wait quietly for the software 
installation to complete.

## 3 Install lxqt desktop environment

This is a relatively lightweight and simple desktop environment. For specific 
installation instructions, please see this 
[link](https://x.cygwin.com/docs/ug/using.html). Pay attention here:

![install_lxqt](./img/install_lxqt.png)

We need to install the `lxqt-session` package. After the above basic packages are 
installed, we reopen `setup-x86_64.exe`. Then be sure to select the following 
before choosing any software. Don't choose the `best` this time. Because if you 
choose, it will restore you according to the best configuration, and your 
software may be uninstalled, remember! You can only choose to `keep` it.

## 4 Install GTK3 dependent libraries

Since our program needs to use the GTK3 font library, we need to install 
Cargo-related libraries. We reopen `setup-x86_64.exe`, then search for cairo and 
pango, and install all the relevant packages searched.

![perl-pangeo-selected](./img/perl-pango-selected.png)

Note that the library for drawing lines here must be selected.


![perl-cargo-selected](./img/perl-cairo-selected.png)

Remember to select the `line drawing` software package.

## 5 Configuring the agent for the environment

If necessary, you can configure the environment's agent by doing this:
export http_proxy=http://username:password@xx.xx.com:8080
export https_proxy=https://username:password@xx.xx.com:8080

In this format, if there are special characters in the username and password, 
their corresponding URL codes are used instead.

When installing the perl5 library, if you enter the cpan environment, it may be 
affected by the system environment variables. Before doing this, you can cancel 
the system environment variables and then enter the cpan shell.

export -n http_proxy
export -n https_proxy

If you want these environment variables to take effect permanently, you can put 
them in .bash_profile in the user directory. It takes effect every time you 
start the shell.

## 6 Install perl5 modules

I have tested most of the modules, so when installing using cpan, I only need 
to specify `notest install`. It can save a lot of testing time.

- First enter the installation environment of cpan shell

```bash
perl -MCPAN -e shell
```

- install App::Asciio

```bash
notest install App::Asciio
```

- Wait for the automatic installation to complete

If an SSL error is reported during the installation process, you can solve it 
by following the steps below:

```bash
Open the CPAN shell. You can open it by typing cpan on the command line.
In the CPAN shell, enter o conf init urllist.
This will display a list of URLs that CPAN uses to download modules.
Save and exit the CPAN shell.
```

We update the following list

```bash
cpan[4]> o conf init urllist

Now you need to choose your CPAN mirror sites.  You can let me
pick mirrors for you, you can select them from a list or you
can enter them by hand.

Would you like me to automatically choose some CPAN mirror
sites for you? (This means connecting to the Internet) [yes] yes

Trying to fetch a mirror list from the Internet
Fetching with HTTP::Tiny:
https://cpan.org/MIRRORED.BY

Looking for CPAN mirrors near you (please be patient)
.. done!

New urllist
  http://www.cpan.org/

Please remember to call 'o conf commit' to make the config permanent!

cpan[5]> o config commit

Known options:
  conf    set or get configuration variables
  debug   set or get debugging options

cpan[6]> o conf commit
commit: wrote '/home/pc/.cpan/CPAN/MyConfig.pm'

cpan[7]>

```

If the above does not solve the problem, you can disable hostname checking: You 
can disable hostname checking by setting the environment variable 
`PERL_LWP_SSL_VERIFY_HOSTNAME=0`.

```bash
export PERL_LWP_SSL_VERIFY_HOSTNAME=0
```

- Some missing related libraries can be installed manually

```bash
/usr/lib/gcc/x86_64-pc-cygwin/11/../../../../x86_64-pc-cygwin/bin/ld: 找不到 -lnsl: No such file or directory
collect2: 错误：ld 返回 1
make: *** [Makefile:483：blib/arch/auto/Params/Util/Util.dll] 错误 1
  REHSACK/Params-Util-1.102.tar.gz
  /usr/bin/make -- NOT OK
```

If you encounter a similar error during the installation process, it proves 
that there is no `-lnsl` in our system. This module requires us to download and 
install it manually from cpan, and delete the content of `-lnsl` in the generated 
Makefile.

![deal_lnsl](./img/deal_lnsl.png)

```bash
perl Makefile.PL
#- delete -lnsl
make
make install
```

After installation, the missing modules can be installed manually.

![manually_install](./img/manually_install.png)

`Term::Size` also needs to be processed in a similar way.

If some modules cannot be installed, it may not affect the use. For example, 
in my environment, `Term::TermKey` cannot be installed.

## 7 Use the lxqt desktop environment to execute GTK3 GUI applications

Execute this command in the cygwin terminal:

```bash
startx /etc/X11/xinit/Xsession lxqt
```

The terminal can be found here.

![lxqt_find_terminal](./img/lxqt_find_terminal.png)

## 8 Use the starxwin window to execute GTK3 GUI applications

The advantage of this method is that the window is the same as the windows 
window and can be seamlessly integrated. The disadvantage is that the drawing 
efficiency of GUI programs in this window environment is not very high.

Start method:

```bash
startxwin 2>/dev/null &
export DISPLAY=:0.0
asciio 2>/dev/null &
```

## 9 Set lxqt desktop to multiple

Edit this file: `~/.config/openbox/lxqt-rc.xml`. In my environment, the path of 
this file is: `E:\programes\cygwin\home\pc\.config\openbox\lxqt-rc.xml`

Pay attention here:

```bash
<desktops>
  <!-- this stuff is only used at startup, pagers allow you to change them
	   during a session

	   these are default values to use when other ones are not already set
	   by other applications, or saved in your session

	   use obconf if you want to change these without having to log out
	   and back in -->
  <number>2</number>
  <firstdesk>1</firstdesk>
  <names>
	<!-- set names up here if you want to, like this:
	<name>desktop 1</name>
	<name>desktop 2</name>
	-->
  </names>
  <popupTime>875</popupTime>
  <!-- The number of milliseconds to show the popup for when switching
	   desktops.  Set this to 0 to disable the popup. -->
</desktops>
```

Change the `2` in the `<number>` tag above to the number of virtual desktops you 
want, for example, to `5`. You can use `5` virtual desktops at one time and use 
the mouse wheel to switch in the blank space of the desktop.

## 10 Install Chinese input method

### 10.1 First ensure that the ibus framework is installed

This has already been done when selecting ibus-related software packages.

### 10.2 Install libpinyin library

For specific installation, please refer to this [link](https://zhuanlan.zhihu.com/p/186289340).

- First download the source code from the source code [repository](https://github.com/libpinyin/libpinyin).
- After unzipping and entering the source code directory, then use the following command to build

```bash
./autogen.sh --prefix=/usr --disable-lua-extension --disable-english-input-mode --disable-stroke-input-mode
```

- After the build is complete, run the compile and install

```bash
make -j4
make install
```

### 10.3 Install ibus-libpinyin input method

The `libpinyin` we just installed is just a library, and `ibus-libpinyin` is 
the specific implementation of this library under the ibus input method 
framework. After we install it, we can use the intelligent Chinese input method.
For the specific installation process, please refer to this [article](https://www.jianshu.com/p/ead4aaf09711).

- First download the source code from the source code [repository](https://github.com/epico/ibus-libpinyin).
- Confirm whether the `gnome-common` dependency package is installed in the environment.

If it is in the following status, it means it is not installed.

```bash
pc@DESKTOP-0MVRMOU /cygdrive/d/tmp/ibus-libpinyin-1.15.6                                             
$ cygcheck -c gnome-common
Cygwin Package Information
Package              Version    Status

pc@DESKTOP-0MVRMOU /cygdrive/d/tmp/ibus-libpinyin-1.15.6                                             
$ 
```

You can re-run `setup-x86_64.exe`, then choose to keep it, and then find the 
packages we need and install them.

![gnome-common-reinstall](./img/gnome-common-reinstall.png)

When we select an installation package, we may find that there may be many 
packages that cygwin chooses to install for us in the end. Don't worry, this is 
the dependency of the package we want to install that cygwin automatically looks 
for. Let cygwin handle it in the default way.

![cygwin_auto_solve_conflict](./img/cygwin_auto_solve_conflict.png)

- After unzipping and entering the source code directory, then use the 
following command to build

- Run the build

```bash
./autogen.sh --disable-english-input-mode prefix=/usr
```

If an error is reported during the construction process, indicating that the 
version of libibus1.0 is too low, then reinstall the following two installation 
packages.

![libibus-ver-low](./img/libibus-ver-low.png)

In addition, sqlite3 related packages are also required

![sqlite3-require](./img/sqlite3-require.png)

You may encounter another problem during the compilation process

```bash
  CXX      ibus_engine_libpinyin-PYLibPinyin.o
In file included from PYLibPinyin.cc:21:
PYLibPinyin.h:79:34: 错误：‘FILE’未声明
   79 |                                  FILE * dictfile,
	  |                                  ^~~~
PYLibPinyin.h:82:36: 错误：‘FILE’未声明
   82 |     bool forwardNetworkDictionary (FILE * dictfile,
	  |      
```

The way to solve this problem is to add `#include <stdio.h>` to the top of the 
`PYLibPinyin.h` header file and then make again.

- Compile and install after the build is complete

```bash
make
make install
```

- After the installation is complete, you can set it in the lxqt desktop system

Remember to select ibus in the input method selector first

![select_ibus](./img/select_ibus.png)

![select_ibus2](./img/select_ibus2.png)

## 11 Set up the cygwin environment to share the font directory of windows

How to install a windows font into cygwin, you can read the [article](https://darrengoossens.wordpress.com/2022/03/01/add-a-windows-font-to-cygwin/).

first step:
Create soft links between the windows font directory and the cygwin font directory:
`ln -s /cygdrive/C/Users/pc/AppData/Local/Microsoft/Windows/Fonts /usr/share/fonts`

Second step:
Refresh the font library:
`/etc/postinstall/zp_fontconfig_cache_1.sh`

