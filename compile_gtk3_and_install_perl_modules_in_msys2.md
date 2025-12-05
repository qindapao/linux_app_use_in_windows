# compile_gtk3_and_install_perl_modules_in_msys2

## Compile and install GTK3 related modules

### Use msys2's ucrt terminal. Pre-install the following packages.

```bash
pacman -Syu
pacman -S mingw-w64-ucrt-x86_64-make
pacman -S mingw-w64-ucrt-x86_64-pkgconf
pacman -S mingw-w64-ucrt-x86_64-perl
pacman -S mingw-w64-ucrt-x86_64-toolchain
pacman -S mingw-w64-ucrt-x86_64-crt-git
pacman -S mingw-w64-ucrt-x86_64-gtk3 \
			mingw-w64-ucrt-x86_64-glib2 \
			mingw-w64-ucrt-x86_64-pango \
			mingw-w64-ucrt-x86_64-cairo \
			mingw-w64-ucrt-x86_64-harfbuzz \
			mingw-w64-ucrt-x86_64-freetype \
			mingw-w64-ucrt-x86_64-libpng \
			mingw-w64-ucrt-x86_64-gobject-introspection
pacman -S mingw-w64-ucrt-x86_64-libtool
pacman -S mingw-w64-ucrt-x86_64-unibilium mingw-w64-ucrt-x86_64-libtermkey
pacman -S mingw-w64-ucrt-x86_64-libthai
pacman -S mingw-w64-ucrt-x86_64-libdatrie
```

### Manually install `cpanm` into the environment.

download: https://raw.githubusercontent.com/miyagawa/cpanminus/master/cpanm ,
Save to file `cpanm`，Then copy it to the `/usr/bin/` directory of our `UCRT`
terminal through the command.

```bash
cp -f cpanm /usr/bin/
chmod +x /usr/bin/cpanm
```


### Install the `perl5` bindings for `Cairo`.

(1). Modify version differences

Since the perl5 of our current environment is `perl5.38`, The latest version of `Cairo` is adapted to `perl5.36`, and
some definitions have changed in `perl5.38`，To compile successfully in `perl5.36`, we need to modify `CairoSurface.xs`
in the `Cairo` module.


```bash
wget https://cpan.metacpan.org/authors/id/X/XA/XAOC/Cairo-1.109.tar.gz
tar -xzf Cairo-1.109.tar.gz
cd Cairo-1.109
```




```c
unsigned long length;
mime_data = (const unsigned char *) SvPV(data, length);
```

Change to:

```c
STRLEN length;
mime_data = (const unsigned char *) SvPV(data, length);
```

`STRLEN` is the standard type for string lengths in Perl 5.38, replacing the old
`unsigned long`.


(2).  Then run `Makefile.PL` to generate the `Makefile` file.

```bash
perl Makefile.PL
```

(3). Manually modify the `EXTRALIBS` and `LDLOADLIBS` variables in the
`Makefile` file to let the linker find the correct `dll` file.

```Makefile
EXTRALIBS = -lcairo -lcairo -lfreetype -lmoldname -lkernel32 -luser32 -lgdi32 -lwinspool -lcomdlg32 -ladvapi32 -lshell32 -lole32 -loleaut32 -lnetapi32 -luuid -lws2_32 -lmpr -lwinmm -lversion -lodbc32 -lodbccp32 -lcomctl32
LDLOADLIBS = -lcairo -lcairo -lfreetype -lmoldname -lkernel32 -luser32 -lgdi32 -lwinspool -lcomdlg32 -ladvapi32 -lshell32 -lole32 -loleaut32 -lnetapi32 -luuid -lws2_32 -lmpr -lwinmm -lversion -lodbc32 -lodbccp32 -lcomctl32
BSLOADLIBS =
```


(4). Perform build and installation.

```bash
mingw32-make
mingw32-make install
```

(5). Verify installation was successful

```bash
perl -MCairo -e 'print $Cairo::VERSION, "\n"'
```

If the version number: `1.109` is printed, it means the installation is successful.

### Install the `perl5` bindings for `Glib`.

(1). Make sure the value of the environment variable `PKG_CONFIG_PATH` is correct.

```bash
xx@DESKTOP-0KALMAH UCRT64 /f/temp/2025-10/Cairo-1.109
# echo $PKG_CONFIG_PATH
/ucrt64/lib/pkgconfig:/ucrt64/share/pkgconfig

xx@DESKTOP-0KALMAH UCRT64 /f/temp/2025-10/Cairo-1.109
#
```

(2). Verify `pkg-config` can find `glib-2.0`

```bash
xx@DESKTOP-0KALMAH UCRT64 /f/temp/2025-10/Cairo-1.109
# pkg-config --cflags glib-2.0
-ID:/msys64/ucrt64/include/glib-2.0 -ID:/msys64/ucrt64/lib/glib-2.0/include

xx@DESKTOP-0KALMAH UCRT64 /f/temp/2025-10/Cairo-1.109
#
```

(3). Download the installation package manually.

```bash
wget http://www.cpan.org/authors/id/X/XA/XAOC/Glib-1.3294.tar.gz
tar -xzf Glib-1.3294.tar.gz
cd Glib-1.3294
```

(4). Generate `Makefile`.

```bash
perl Makefile.PL
```

(5). As in the case of `Cairo`, we modify the link library manually.

```bash
EXTRALIBS = "D:\msys64\ucrt64\lib\libgobject-2.0.a" "D:\msys64\ucrt64\lib\libglib-2.0.a" "D:\msys64\ucrt64\lib\libintl.a" "D:\msys64\ucrt64\lib\libgthread-2.0.a" "D:\msys64\ucrt64\lib\libglib-2.0.a" "D:\msys64\ucrt64\lib\libintl.a" "D:\msys64\ucrt64\lib\libmoldname.a" "D:\msys64\ucrt64\lib\libkernel32.a" "D:\msys64\ucrt64\lib\libuser32.a" "D:\msys64\ucrt64\lib\libgdi32.a" "D:\msys64\ucrt64\lib\libwinspool.a" "D:\msys64\ucrt64\lib\libcomdlg32.a" "D:\msys64\ucrt64\lib\libadvapi32.a" "D:\msys64\ucrt64\lib\libshell32.a" "D:\msys64\ucrt64\lib\libole32.a" "D:\msys64\ucrt64\lib\liboleaut32.a" "D:\msys64\ucrt64\lib\libnetapi32.a" "D:\msys64\ucrt64\lib\libuuid.a" "D:\msys64\ucrt64\lib\libws2_32.a" "D:\msys64\ucrt64\lib\libmpr.a" "D:\msys64\ucrt64\lib\libwinmm.a" "D:\msys64\ucrt64\lib\libversion.a" "D:\msys64\ucrt64\lib\libodbc32.a" "D:\msys64\ucrt64\lib\libodbccp32.a" "D:\msys64\ucrt64\lib\libcomctl32.a"
LDLOADLIBS = "D:\msys64\ucrt64\lib\libgobject-2.0.a" "D:\msys64\ucrt64\lib\libglib-2.0.a" "D:\msys64\ucrt64\lib\libintl.a" "D:\msys64\ucrt64\lib\libgthread-2.0.a" "D:\msys64\ucrt64\lib\libglib-2.0.a" "D:\msys64\ucrt64\lib\libintl.a" "D:\msys64\ucrt64\lib\libmoldname.a" "D:\msys64\ucrt64\lib\libkernel32.a" "D:\msys64\ucrt64\lib\libuser32.a" "D:\msys64\ucrt64\lib\libgdi32.a" "D:\msys64\ucrt64\lib\libwinspool.a" "D:\msys64\ucrt64\lib\libcomdlg32.a" "D:\msys64\ucrt64\lib\libadvapi32.a" "D:\msys64\ucrt64\lib\libshell32.a" "D:\msys64\ucrt64\lib\libole32.a" "D:\msys64\ucrt64\lib\liboleaut32.a" "D:\msys64\ucrt64\lib\libnetapi32.a" "D:\msys64\ucrt64\lib\libuuid.a" "D:\msys64\ucrt64\lib\libws2_32.a" "D:\msys64\ucrt64\lib\libmpr.a" "D:\msys64\ucrt64\lib\libwinmm.a" "D:\msys64\ucrt64\lib\libversion.a" "D:\msys64\ucrt64\lib\libodbc32.a" "D:\msys64\ucrt64\lib\libodbccp32.a" "D:\msys64\ucrt64\lib\libcomctl32.a"
BSLOADLIBS = 
```

Change to:

```bash
EXTRALIBS = -lgobject-2.0 -lglib-2.0 -lintl -lgthread-2.0 -lglib-2.0 -lintl -lmoldname -lkernel32 -luser32 -lgdi32 -lwinspool -lcomdlg32 -ladvapi32 -lshell32 -lole32 -loleaut32 -lnetapi32 -luuid -lws2_32 -lmpr -lwinmm -lversion -lodbc32 -lodbccp32 -lcomctl32
LDLOADLIBS = -lgobject-2.0 -lglib-2.0 -lintl -lgthread-2.0 -lglib-2.0 -lintl -lmoldname -lkernel32 -luser32 -lgdi32 -lwinspool -lcomdlg32 -ladvapi32 -lshell32 -lole32 -loleaut32 -lnetapi32 -luuid -lws2_32 -lmpr -lwinmm -lversion -lodbc32 -lodbccp32 -lcomctl32
BSLOADLIBS =

```

(6). Perform build and installation.

```bash
mingw32-make
mingw32-make install
```

(7). Print the version number to check whether the installation was successful.

```bash
perl -MGlib -e 'print $Glib::VERSION, "\n"'
```

If the version number is printed: `1.3294`, it means the installation is successful.

### Install `Glib::Object::Introspection`

(1). Download the installation package manually.

```bash
wget http://www.cpan.org/authors/id/X/XA/XAOC/Glib-Object-Introspection-0.052.tar.gz
tar -xzf Glib-Object-Introspection-0.052.tar.gz
cd Glib-Object-Introspection-0.052
```

(2). Generate `Makefile`.

```bash
perl Makefile.PL
```

(3). Manually modify the link library.

```bash
EXTRALIBS = -lgirepository-1.0 -lgobject-2.0 -lglib-2.0 -lintl -lgmodule-2.0 -lglib-2.0 -lintl -lffi -lgobject-2.0 -lglib-2.0 -lintl -lgthread-2.0 -lglib-2.0 -lintl -lmoldname -lkernel32 -luser32 -lgdi32 -lwinspool -lcomdlg32 -ladvapi32 -lshell32 -lole32 -loleaut32 -lnetapi32 -luuid -lws2_32 -lmpr -lwinmm -lversion -lodbc32 -lodbccp32 -lcomctl32
LDLOADLIBS = -lgirepository-1.0 -lgobject-2.0 -lglib-2.0 -lintl -lgmodule-2.0 -lglib-2.0 -lintl -lffi -lgobject-2.0 -lglib-2.0 -lintl -lgthread-2.0 -lglib-2.0 -lintl -lmoldname -lkernel32 -luser32 -lgdi32 -lwinspool -lcomdlg32 -ladvapi32 -lshell32 -lole32 -loleaut32 -lnetapi32 -luuid -lws2_32 -lmpr -lwinmm -lversion -lodbc32 -lodbccp32 -lcomctl32
BSLOADLIBS =
```

(4). Perform build and installation.

```bash
mingw32-make
mingw32-make install
```

During the `make` process, we don't need to worry about the errors in `pl2bat.bat`, because our `dll` has been generated correctly, which is enough.


(5). Print the version number to check whether we installed it successfully.

```bash
perl -MGlib::Object::Introspection -e 'print $Glib::Object::Introspection::VERSION, "\n"'
```

If it prints `0.052` it proves that our installation was successful.


### Install `Cairo::GObject`

(1). Download the installation package manually.


```bash
wget http://www.cpan.org/authors/id/X/XA/XAOC/Cairo-GObject-1.005.tar.gz
tar -xzf Cairo-GObject-1.005.tar.gz
cd Cairo-GObject-1.005
```


(2). Generate `Makefile`.

```bash
perl Makefile.PL
```

(3). Manually modify the link library.

```bash
EXTRALIBS = -lcairo-gobject -lcairo -lgobject-2.0 -lglib-2.0 -lintl -lcairo -lcairo -lfreetype -lgobject-2.0 -lglib-2.0 -lintl -lgthread-2.0 -lglib-2.0 -lintl -lmoldname -lkernel32 -luser32 -lgdi32 -lwinspool -lcomdlg32 -ladvapi32 -lshell32 -lole32 -loleaut32 -lnetapi32 -luuid -lws2_32 -lmpr -lwinmm -lversion -lodbc32 -lodbccp32 -lcomctl32
LDLOADLIBS = -lcairo-gobject -lcairo -lgobject-2.0 -lglib-2.0 -lintl -lcairo -lcairo -lfreetype -lgobject-2.0 -lglib-2.0 -lintl -lgthread-2.0 -lglib-2.0 -lintl -lmoldname -lkernel32 -luser32 -lgdi32 -lwinspool -lcomdlg32 -ladvapi32 -lshell32 -lole32 -loleaut32 -lnetapi32 -luuid -lws2_32 -lmpr -lwinmm -lversion -lodbc32 -lodbccp32 -lcomctl32
BSLOADLIBS =
```

(4). Perform build and installation.

```bash
mingw32-make
mingw32-make install
```

(5). Print the version number to check whether we installed it successfully.

```bash
perl -MCairo::GObject -e 'print $Cairo::GObject::VERSION, "\n"'
```

If `1.005` is printed, it proves that our module is installed successfully.

### 安装 `Pango`

(1). Download the installation package manually.

```bash
wget http://www.cpan.org/authors/id/X/XA/XAOC/Pango-1.227.tar.gz
tar -xzf Pango-1.227.tar.gz
cd Pango-1.227
```

(2). Generate `Makefile`.

```bash
perl Makefile.PL
```

(3). Manually modify the link library.

```bash
EXTRALIBS = -lpango-1.0 -lgobject-2.0 -lharfbuzz -lglib-2.0 -lintl -lpangocairo-1.0 -lpango-1.0 -lcairo -lharfbuzz -lgobject-2.0 -lglib-2.0 -lintl -lcairo -lcairo -lfreetype -lgobject-2.0 -lglib-2.0 -lintl -lgthread-2.0 -lglib-2.0 -lintl -lmoldname -lkernel32 -luser32 -lgdi32 -lwinspool -lcomdlg32 -ladvapi32 -lshell32 -lole32 -loleaut32 -lnetapi32 -luuid -lws2_32 -lmpr -lwinmm -lversion -lodbc32 -lodbccp32 -lcomctl32
LDLOADLIBS = -lpango-1.0 -lgobject-2.0 -lharfbuzz -lglib-2.0 -lintl -lpangocairo-1.0 -lpango-1.0 -lcairo -lharfbuzz -lgobject-2.0 -lglib-2.0 -lintl -lcairo -lcairo -lfreetype -lgobject-2.0 -lglib-2.0 -lintl -lgthread-2.0 -lglib-2.0 -lintl -lmoldname -lkernel32 -luser32 -lgdi32 -lwinspool -lcomdlg32 -ladvapi32 -lshell32 -lole32 -loleaut32 -lnetapi32 -luuid -lws2_32 -lmpr -lwinmm -lversion -lodbc32 -lodbccp32 -lcomctl32
BSLOADLIBS =
```

(4). Perform build and installation.

```bash
mingw32-make
mingw32-make install
```

(5). Print the version number to check whether we installed it successfully.

```bash
perl -MPango -e 'print $Pango::VERSION, "\n"'
```

If `1.227` is printed, it proves that our module is installed successfully.

### Install `Gtk3`

```bash
cpanm Gtk3
```

Under normal circumstances, the installation will be successful directly, and
then we can query the version number:

```bash
perl -MGtk3 -e 'print $Gtk3::VERSION, "\n"'
```

### Install `Unicode-LineBreak`

(1). Download the installation package manually.

```bash
wget http://www.cpan.org/authors/id/N/NE/NEZUMI/Unicode-LineBreak-2019.001.tar.gz
tar -xzf Unicode-LineBreak-2019.001.tar.gz
cd Unicode-LineBreak-2019.001
```

(2). Generate the `Makefile` file.

```bash
perl Makefile.PL
```

(3). we modify the link library manually.

`-ldatrie -lshlwapi` It's missing, we need to add it.

```bash
EXTRALIBS = -ldatrie -lshlwapi -lthai -lmoldname -lkernel32 -luser32 -lgdi32 -lwinspool -lcomdlg32 -ladvapi32 -lshell32 -lole32 -loleaut32 -lnetapi32 -luuid -lws2_32 -lmpr -lwinmm -lversion -lodbc32 -lodbccp32 -lcomctl32
LDLOADLIBS = -ldatrie -lshlwapi -lthai -lmoldname -lkernel32 -luser32 -lgdi32 -lwinspool -lcomdlg32 -ladvapi32 -lshell32 -lole32 -loleaut32 -lnetapi32 -luuid -lws2_32 -lmpr -lwinmm -lversion -lodbc32 -lodbccp32 -lcomctl32
BSLOADLIBS = 
```

(4). Perform build and installation.

```bash
mingw32-make
mingw32-make install
```

(5). Verify that the installation was successful.

```perl
perl -MUnicode::GCString -e 'print Unicode::GCString->new("你好世界！")->columns, "\n";'
```

If the printout is `10`, it proves that we succeeded.


## Install other dependencies and asciio

```bash
cpanm --force Module::Build
cpanm Data::TreeDumper::Renderer::GTK
cpanm App::Asciio
```

Modules that may fail to install:


1. Devel::CheckLib

```bash
wget http://www.cpan.org/authors/id/M/MA/MATTN/Devel-CheckLib-1.16.tar.gz
tar -xzf Devel-CheckLib-1.16.tar.gz
cd Devel-CheckLib-1.16
perl Makefile.PL
mingw32-make
mingw32-make install
```

2. Term::TermKey

```bash
cpanm --force Term::TermKey
```

3. Sereal::Encoder → Sereal::Decoder → Sereal

```bash
cpanm Sereal::Encoder
cpanm Sereal::Decoder
cpanm Sereal
```

Just reinstall it. The previous failure may be due to dependence on
`Devel::CheckLib`.


4. Path::Tiny → Path::Class

```bash
cpanm --force Path::Tiny
cpanm --force Path::Class
```

5. File::Find::Rule

```bash
wget http://www.cpan.org/authors/id/R/RC/RCLAMP/File-Find-Rule-0.35.tar.gz
tar -xzf File-Find-Rule-0.35.tar.gz
cd File-Find-Rule-0.35
perl Makefile.PL
mingw32-make
mingw32-make install
```

6. Test::Block → Test::Strict

```bash
cpanm --force Test::Block
cpanm --force Test::Strict
```


7. Directory::Scratch → Directory::Scratch::Structured

```bash
cpanm Directory::Scratch
cpanm --force Directory::Scratch::Structured
```


8. Data::Compare

```bash
cpanm Data::Compare
```

9. Eval::Context

```bash
cpanm --force Eval::Context
```


10. IO::Prompter

```bash
cpanm --force IO::Prompter
```

11. Unicode::GCString

```bash
cpanm Unicode::GCString
```

