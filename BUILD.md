<!--
   |--------------------------------------------------------------------------|
   | =========                 |                                              |
   | \\      /  F ield         | OpenFOAM: The Open Source CFD Toolbox        |
   |  \\    /   O peration     |                                              |
   |   \\  /    A nd           | Copyright (C) 2016 OpenCFD Ltd.              |
   |    \\/     M anipulation  |                                              |
   |--------------------------------------------------------------------------|
  -->

---

# OpenFOAM&reg; ThirdParty Build

OpenFOAM depends to a certain extent on third-party libraries
(*opensource only*). It also provides some interfaces to *opensource* or
*proprietary* libraries. This third-party package contains configurations and
scripts for building third-party packages. It should normally only be used in
conjunction with the corresponding OpenFOAM version.


## Configuration of Third-Party Versions

The distributed make scripts can generally be used for a variety of
versions of the third-party libraries, with the software version
specified on the command-line. For example,

    $ ./makeFFTW -help
    usage: makeFFTW [OPTION] [fftw-VERSION]

If a version is not explicitly specified on the command-line, it will
use the version as specified by the appropriate OpenFOAM
`etc/config.sh/...` entry.

This approach avoids duplicate entries for the default versions and
ensures the best overall consistency between the OpenFOAM installation
and its corresponding third-party installation.

---

## Before Starting

0. Review the [system requirements](http://www.openfoam.com/documentation/system-requirements.php)
   and decide on the following:
   * compiler type/version - if the system compiler is not relatively recent,
     you will need a [third-party compiler](#makeGcc) installation.
   * MPI type/version.
   * ParaView type/version.
   * CMake type/version, ...
1. If you are using a system MPI (eg, openmpi), ensure that this environment
   has also been properly activated for your user.
   Often (but not always) a `mpi-selector` command is available for this purpose.
   You may need to open a new shell afterwards for the change to take effect.
   Using the following command may help diagnosing things:

       which mpicc

2. Adjust the OpenFOAM `etc/bashrc`, `etc/config.sh/...` or equivalent
   `prefs.sh` files to reflect your preferred configuration.
   For many config files, there are several configuration possibilities:
   - Define a particular third-party version.
   - Use a system installation.
   - Disable use of an optional component.
   - Define an alternative site-wide central location.
   - After making the desired changes, use `wmRefresh` or equivalent to use the configurations.


---

## Building

Many components of ThirdParty are *optional* or are invoked
automatically as part of the top-level OpenFOAM `Allwmake`.
Nonetheless it may be necessary or useful to build various
ThirdParty components prior to building OpenFOAM itself.


### Build Sequence
1. `makeGcc` _or_ `makeLLVM` <a name="makeGcc"></a> *(optional)*
   - Makes a third-party [gcc](#gcc-compiler) or [clang](#clang-compiler) installation,
     which is needed if the system gcc is [too old](#gcc-compiler).
     If your system compiler is recent enough, you can skip this step.
   - If you do use this option, you will need the following adjustments to the
     OpenFOAM `etc/bashrc` or your equivalent `prefs.sh` file:
     - `WM_COMPILER_TYPE=ThirdParty`
     - `WM_COMPILER=Gcc48` (for example)
     - or `WM_COMPILER=Clang` and adjust the `clang_version` entry in the OpenFOAM
     `etc/config.sh/compiler` or equivalent.
   - More description is contained in the header comments of the
     `makeGcc` and `makeLLVM` files.
   - *Attention*: If you are building a newer version of clang, you may need to
     update your CMake beforehand.
2. `makeCmake`  *(optional)*
   - Makes a third-party [CMake](#general-packages) installation, which is
     needed if a system CMake does not exist or is [too old](#min-cmake),
   - Note that CMake is being used by an number of third-party packages
     (CGAL, LLVM, ParaView, VTK, ...)
     so this may become an increasingly important aspect of the build.
3. `Allwmake`
   - This will be automatically invoked by the top-level OpenFOAM `Allwmake`, but
     can also be invoked directly to find possible build errors.
   - Builds an mpi library (openmpi or mpich), scotch decomposition, boost, CGAL, FFTW.
   - If the optional metis directory is found, it will also be compiled.
4. `makeParaView`  *(optional but highly recommended)*
   - This is optional, but extremely useful for visualization and for
     run-time post-processing function objects.
     You can build this at a later point in time, but then you should
     remember to rebuild the post-processing function objects and the
     reader module as well.
5. Make any additional optional components


#### Optional Components

`makeCGAL`
- Builds third-party boost and CGAL.
  Automatically invoked from the ThirdParty `Allwmake`,
  but can be invoked directly to resolve possible build errors.

`makeFFTW`
- Builds third-party FFTW.
  Automatically invoked from the ThirdParty `Allwmake`,
  but can be invoked directly to resolve possible build errors.

`makeCCMIO`
- Only required for conversion to/from STARCD/STARCCM+ files.

`makeTecio`
- Only required for conversion of results to Tecplot format.

`makeMesa`, `makeVTK`
- Additional support for building offscreen rendering components.
  Useful if you want to render on computer servers without graphics cards.
  The `makeParaView.example` and `makeVTK.example` files provide some useful
  suggestions about compiling such a configuration.

`makeQt`
- Script to build a [third-party installation of Qt](#makeQt), including qmake.
- Possibly needed for `makeParaView`.

`makeGperftools`
- Build gperftools (originally Google Performance Tools)

`minCmake`
- Scour specified directories for CMakeLists.txt and their cmake_minimum.
  Report in sorted order.

`Allclean`
- After building, this script may be used to remove intermediate build information
and save some disk space.


## Build Notes

### Scotch
- The zlib library and zlib development headers are required.


### Mesa
- Needed for off-screen rendering.
- Building with [mesa-11][older mesa] is fine and [mesa-13][link mesa] also seems to be okay.
- Building with mesa-12 is not possible since it fails to create
  the necessary `include/GL` directory and `osmesa.h` file.

### VTK
- Needed for off-screen rendering and run-time post-processing without
  ParaView.
- Rather than downloading VTK separately, it is easy to reuse the VTK
  sources that are bundled with ParaView.
  For example, by using a symbolic link:

      ln -s ParaView-5.2.0/VTK  VTK-7.1.0

  The appropriate VTK version number can be found from the contents of
  the `vtkVersion.cmake` file.
  For example,

      $ cat ParaView-5.2.0/VTK/CMake/vtkVersion.cmake

      # VTK version number components.
      set(VTK_MAJOR_VERSION 7)
      set(VTK_MINOR_VERSION 1)
      set(VTK_BUILD_VERSION 0)

### ParaView
- Building ParaView requires CMake, qmake and a `qt` development files.
  Use the `-cmake`, `-qmake` and `-qt-*` options for `makeParaView` as
  required.
  See additional notes below about [making Qt](#makeQt) if necessary.

### ParaView
- Both CMake and qmake are required when building ParaView.

#### 5.2.0
- Compiles without patching.

#### 4.4.0/5.0.0/5.0.1/5.1.2
- If using `makeParaView`, the following patches will be automatically
  applied (see the `etc/patches` directory):
  - Bugfix for STL reader - affects 4.4.0 only.
  - Broken installation (ui_pqExportStateWizard.h) - affects 4.4.0/5.0.0/5.0.1/5.1.x
  - Building with gcc-6.1.0 - affects 4.4.0/5.0.0/5.0.1 (patch applied for 5.0.1)
  - The SciberQuestToolKit plugin fails to compile with gcc-6.1.0 and causes
    the compilation of ParaView to halt. The easiest solution is to delete
    the ParaView-5.0.1/Plugins/SciberQuestToolKit directory.


### Making Qt <a name="makeQt"></a>
- Building a third-party Qt installation (prior to building ParaView) requires
  some additional effort, but should nonetheless work smoothly.

1. Download a [*qt-everywhere-opensource-src*][link Qt] package and
   unpack in the third-party directory.
2. Use the `makeQt` script with the QT version number. For example,

       ./makeQt 4.8.7

3. Build ParaView using this third-party QT. For example,

       ./makeParaView -qt-4.8.7 5.2.0

- ParaView does not yet support QT5.

- If you relocate the third-party directory to another location
  (eg, you built in your home directory, but want to install it in a
  central location), you will need to use the `etc/relocateQt` script
  afterwards.


---

## Versions

### Gcc Compiler <a name="gcc-compiler"></a>

The minimum version of gcc required is 4.8.0.

| Name              | Location                                   |
|-------------------|--------------------------------------------|
| [gcc][page gcc]   | [releases][link gcc]                       |
| [gmp][page gmp]   | system is often ok, otherwise [download][link gmp]  |
| [mpfr][page mpfr] | system is often ok, otherwise [download][link mpfr] |
| [mpc][page mpc]   | system is often ok, otherwise [download][link mpc]  |


#### Potential MPFR conflicts

If you elect to use a third-party version of mpfr, you may experience
conflicts with your installed system mpfr.
On some systems, mpfr is compiled as *non-threaded*, whereas the
third-party will use *threaded* by default.
This can cause some confusion at the linker stage, since it may
resolve the system mpfr first (and find that it is *non-threaded*).

You can avoid this by one of two means:
1. Use system components for gmp/mpfr/mpc:  `makeGcc -system ...`
2. Use third-party mpfr, but without threading: `makeGcc -no-threadsafe ...`


#### 32-bit build (on 64-bit)

If you have a 64-bit system, but wish to have a 32-bit compiler, you
will need to enable multi-lib support for Gcc: `makeGcc -multilib`,
which is normally disabled, since many (most?) 64-bit systems do not
install the 32-bit development libraries by default.


### Clang Compiler <a name="clang-compiler"></a>

The minimum version of clang required is 3.3.

*Attention*: If you are building a newer version of clang, you may need to
update your CMake beforehand.
GNU *configure* can only be used prior to clang version 3.9.


| Name                  | Location               |
|-----------------------|------------------------|
| [clang][page clang]   | [download][link clang]  |
| [llvm][page llvm]     | [download][link llvm]   |


### Parallel Processing <a name="parallel"></a>

| Name                  | Location               |
|-----------------------|------------------------|
| [adios][page adios]   | [repo][repo adios] or [github download][link adios] or [alt download][altlink adios] |
| [scotch, ptscotch][page scotch] | [download][link scotch] |
| [openmpi][page openmpi] | [download][link openmpi] |


### General <a name="general-packages"></a>

| Name                  | Location               |
|-----------------------|------------------------|
| [CMake][page cmake]   | [download][link cmake] |
| [boost][page boost]   | [download][link boost] |
| [CGAL][page CGAL]     | [download][link CGAL] or [older][older CGAL] |
| [FFTW][page FFTW]     | [download][link FFTW]  |
| [ADF/CGNS][page CGNS], ccm | [link ccmio][link ccmio] |
| [tecio][page tecio]   | [link tecio][link tecio] |
| gperftools            | [repo][repo gperftools] or [download][link gperftools] |


### Visualization <a name="viz-version"></a>

| Name                  | Location               |
|-----------------------|------------------------|
| [MESA][page mesa]     | [download][link mesa] or [older][older mesa] |
| [ParaView][page ParaView] | [download][link ParaView] or older [5.1][older ParaView-51], [5.0][older ParaView-50], [4.4][older ParaView-44] |
| [Qt][page Qt]         | [repo][repo Qt] or [download][link Qt]. The newer [Qt5][newer Qt5] is **not** currently supported by ParaView. |


### CMake Minimum Requirements <a name="min-cmake"></a>

The minimum CMake requirements for building various components.

    2.8         llvm-3.4.2
    2.8.11      CGAL-4.9
    2.8.12.2    llvm-3.8.0
    2.8.4       cmake-3.6.0
    3.3         ParaView-5.1.2
    3.3         ParaView-5.2.0
    3.4.3       llvm-3.9.0.src
    3.5         ParaView-5.1.0


<!-- gcc-related -->
[page gcc]:       http://gcc.gnu.org/releases.html
[page gmp]:       http://gmplib.org/
[page mpfr]:      http://www.mpfr.org/
[page mpc]:       http://www.multiprecision.org/

[link gcc]:       http://gcc.gnu.org/releases.html
[link gmp]:       ftp://ftp.gnu.org/gnu/gmp/gmp-6.1.0.tar.bz2
[link mpfr]:      ftp://ftp.gnu.org/gnu/mpfr/mpfr-3.1.4.tar.bz2
[link mpc]:       ftp://ftp.gnu.org/gnu/mpc/mpc-1.0.3.tar.gz


<!-- clang-related -->
[page clang]:     http://llvm.org/
[page llvm]:      http://llvm.org/

[link clang]:     http://llvm.org/releases/3.9.0/cfe-3.9.0.src.tar.xz
[link llvm]:      http://llvm.org/releases/3.9.0/llvm-3.9.0.src.tar.xz


<!-- parallel -->
[page adios]:     https://www.olcf.ornl.gov/center-projects/adios/
[repo adios]:     https://github.com/ornladios/ADIOS
[link adios]:     https://github.com/ornladios/ADIOS/archive/v1.11.0.tar.gz
[altlink adios]:  http://users.nccs.gov/%7Epnorbert/adios-1.11.0.tar.gz
[page zfp]:       http://computation.llnl.gov/projects/floating-point-compression/zfp-versions

[page scotch]:    https://www.labri.fr/perso/pelegrin/scotch/
[link scotch]:    https://gforge.inria.fr/frs/download.php/file/34099/scotch_6.0.3.tar.gz

[page openmpi]:   http://www.open-mpi.org/
[link openmpi]:   http://www.open-mpi.org/software/ompi/v1.10/downloads/openmpi-1.10.4.tar.bz2
[newer openmpi]:  https://www.open-mpi.org/software/ompi/v2.0/downloads/openmpi-2.0.1.tar.bz2


<!-- general -->
[page cmake]:     http://www.cmake.org/
[link cmake]:     http://www.cmake.org/files/v3.5/cmake-3.5.2.tar.gz

[page boost]:     http://boost.org
[link boost]:     https://sourceforge.net/projects/boost/files/boost/1.62.0/boost_1_62_0.tar.bz2

[page CGAL]:      http://cgal.org
[link CGAL]:      https://github.com/CGAL/cgal/releases/download/releases%2FCGAL-4.9/CGAL-4.9.tar.xz
[older CGAL]:     https://github.com/CGAL/cgal/releases/download/releases%2FCGAL-4.8.2/CGAL-4.8.2.tar.xz

[page FFTW]:      http://www.fftw.org/
[link FFTW]:      http://www.fftw.org/fftw-3.3.5.tar.gz

[page cgns]:      http://cgns.github.io/
[link ccmio]:     http://portal.nersc.gov/svn/visit/trunk/third_party/libccmio-2.6.1.tar.gz (check usage conditions)

[page tecio]:     http://www.tecplot.com/
[link tecio]:     http://www.tecplot.com/my/tecio-library/ (needs registration)

[repo gperftools]: https://github.com/gperftools/gperftools
[link gperftools]: https://github.com/gperftools/gperftools/releases/download/gperftools-2.5/gperftools-2.5.tar.gz


<!-- Visualization -->

[page ParaView]:  http://www.paraview.org/
[link ParaView]:  http://www.paraview.org/files/v5.2/ParaView-v5.2.0.tar.gz

[older ParaView-44]:  http://www.paraview.org/files/v4.4/ParaView-v4.4.0-source.tar.gz
[older ParaView-50]:  http://www.paraview.org/files/v5.0/ParaView-v5.0.1-source.tar.gz
[older ParaView-51]:  http://www.paraview.org/files/v5.1/ParaView-v5.1.2-source.tar.gz

[page mesa]:  http://mesa3d.org/
[link mesa]:  ftp://ftp.freedesktop.org/pub/mesa/13.0.1/mesa-13.0.1.tar.xz
[older mesa]: ftp://ftp.freedesktop.org/pub/mesa/11.2.2/mesa-11.2.2.tar.xz

[page Qt]: https://www.qt.io/download-open-source/
[repo Qt]: http://code.qt.io/cgit/qt-creator/qt-creator.git
[link Qt]: http://download.qt.io/official_releases/qt/4.8/4.8.7/qt-everywhere-opensource-src-4.8.7.tar.gz
[newer Qt5]: http://download.qt.io/official_releases/qt/5.7/5.7.0/single/qt-everywhere-opensource-src-5.7.0.tar.gz


<!-- Standard Footer -->
## Additional OpenFOAM Links

- [Download and installation instructions](http://www.openfoam.com/releases)
- [Documentation](http://www.openfoam.com/documentation)
- [Reporting bugs/issues (including bugs/suggestions/feature requests) in OpenFOAM+](http://www.openfoam.com/code/bug-reporting.php)
- [Collaborative and Community-based Developments](http://www.openfoam.com/services/community-projects.php)
- [Contacting OpenCFD](http://www.openfoam.com/contact)

---

Copyright 2016 OpenCFD Ltd
