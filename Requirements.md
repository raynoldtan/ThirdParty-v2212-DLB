## OpenFOAM&reg; ThirdParty System Requirements

For building some particular third-party libraries from source,
the normal [OpenFOAM System Requirements][link openfoam-require]
may not be sufficient.

This is most notably the case for ParaView and/or QT compilation.
As duly noted in [BUILD][link third-build] and [README][link third-readme] information,
building ParaView from source tends to be the most difficult part of
any third-party compilation.

For general functionality, the paraview version distributed with
the operating system or a [binary package][download ParaView]
is likely adequate for your needs.


***Please help us with keeping the information here up-to-date and accurate.***

### Ubuntu

The full dependency list for building ParaView can be
found from the corresponding [debian/control](https://salsa.debian.org/science-team/paraview/-/blob/master/debian/control)
file.
The following subset may be enough:
```
sudo apt install cmake qt5base-dev qttools5-dev qttools5-dev-tools libqt5opengl5-dev libqt5x11extras5-dev libxt-dev
```


### openSUSE

The full dependency list for building ParaView can be
found from the corresponding [rpm spec](https://build.opensuse.org/package/view_file/science/paraview/paraview.spec)
file.
The following subset may be enough:
```
sudo zypper install Mesa-libEGL-devel
sudo zypper install libqt5-qtbase-devel libqt5-qtsvg-devel libqt5-qttools-devel libqt5-qtx11extras
sudo zypper install libXt-devel
```


<!-- Quick links -->

[download ParaView]: https://www.paraview.org/download/


<!-- OpenFOAM -->

[link openfoam-readme]: https://develop.openfoam.com/Development/openfoam/blob/develop/README.md
[link openfoam-config]: https://develop.openfoam.com/Development/openfoam/blob/develop/doc/Config.md
[link openfoam-build]: https://develop.openfoam.com/Development/openfoam/blob/develop/doc/Build.md
[link openfoam-require]: https://develop.openfoam.com/Development/openfoam/blob/develop/doc/Requirements.md
[link third-readme]: https://develop.openfoam.com/Development/ThirdParty-common/blob/develop/README.md
[link third-build]: https://develop.openfoam.com/Development/ThirdParty-common/blob/develop/BUILD.md
[link third-require]: https://develop.openfoam.com/Development/ThirdParty-common/blob/develop/Requirements.md

---
Copyright 2019 OpenCFD Ltd
