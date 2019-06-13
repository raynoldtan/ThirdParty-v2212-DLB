## OpenFOAM&reg; ThirdParty System Requirements

For building some particular third-party libraries from source,
the normal [OpenFOAM System Requirements][link openfoam-require]
will be insufficient.

This is most notably the case for ParaView and/or QT compilation.
As duly noted in [BUILD][link third-build] and [README][link third-readme] and information,
building ParaView from source tends to be the most difficult part of
any third-party compilation.

For general functionality, the paraview version distributed with
the operating system or a [binary package][download ParaView]
may be sufficient for your needs.


**Please help us with keeping the information here up-to-date and accurate.**

### Ubuntu

Dependencies:
```
sudo apt-get install qt4-dev-tools libqt4-dev libqt4-opengl-dev freeglut3-dev libqtwebkit-dev  libxt-dev
```

### openSUSE

Dependencies:

```
sudo zypper install glu-devel
sudo zypper install libQtWebKit-devel libqt4-devel qt4-assistant-adp-devel qt4-x11-tools
```

<!-- Quick links -->

[download ParaView]: https://www.paraview.org/download/


<!-- OpenFOAM -->

[link openfoam-readme]: https://develop.openfoam.com/Development/OpenFOAM-plus/blob/develop/README.md
[link openfoam-config]: https://develop.openfoam.com/Development/OpenFOAM-plus/blob/develop/doc/Config.md
[link openfoam-build]: https://develop.openfoam.com/Development/OpenFOAM-plus/blob/develop/doc/Build.md
[link openfoam-require]: https://develop.openfoam.com/Development/OpenFOAM-plus/blob/develop/doc/Requirements.md
[link third-readme]: https://develop.openfoam.com/Development/ThirdParty-plus/blob/develop/README.md
[link third-build]: https://develop.openfoam.com/Development/ThirdParty-plus/blob/develop/BUILD.md
[link third-require]: https://develop.openfoam.com/Development/ThirdParty-plus/blob/develop/Requirements.md

---
Copyright 2019 OpenCFD Ltd
