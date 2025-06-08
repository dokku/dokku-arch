# dokku-arch

## Arch Linux Packages

Arch Linux packages are not really built, because all that is needed for an Arch User Repo (AUR) package is the description of how to build the package. To make this process as easy as possible there is a vagrant box called `build-arch` that updates the version of this build description (a file called `PKGBUILD`), then runs some helper scripts to fill all additional information and does test if the package could be build. Then only those changes need to be pushed to the AUR repo and an updated version of the package is ready for usage for our Arch Linux users. For detailed information see the section below.

The workflow looks like this:

```shell
# having dokku-arch in ../dokku-arch
vagrant up build-arch
# wait for "==> build-arch: ==> Finished making: dokku 0.35.19-2 (Mon Feb 22 23:20:37 CET 2016)"
cd ../dokku-arch
git add PKGBUILD .SRCINFO
git commit -m 'Update to dokku 0.9.9'
git push aur master
```

> If you are a maintainer and need access to the AUR repositories in order to make a release, please contact @morrisjobke or @josegonzalez to get this co-maintainership.

### Detailed information for Arch Linux packages

All of the information to build the Arch Linux package is in the AUR git repository (see [dokku AUR page](https://aur.archlinux.org/packages/dokku/)). The release of a AUR package only consists of pushing the package information into the AUR git repo. Then users could use that information to build the package on their machines.

To update the package clone the repository and adjust the files in the repository. Then a helper script - `updpkgsums` - to update the SHA sum could  be called (check against the original SHA sum). Another helper script - `mksrcinfo` - needs to be called to update the meta information of the package in a file called `.SRCINFO`. The next step builds the package locally for verification - `makepkg`. As last step commit your changes and push the commit.

* dependencies are defined in the `depends` attribute in `PKGBUILD`
* build steps during package build time are defined in the `package()` method in `PKGBUILD`
* steps that should be executed during install/update/remove time are defined in the file `dokku.install`
* detailed information about all attributes in `PKGBUILD` could be found in the [Arch Linux wiki](https://wiki.archlinux.org/index.php/PKGBUILD)
* detailed information about the AUR workflow could be found in the [AUR article](https://wiki.archlinux.org/index.php/Arch_User_Repository) in the Arch Linux wiki

That is the usual workflow:

```shell
updpkgsums # update sha sums - compare them with the original ones
mksrcinfo # update package metadata for AUR
makepkg # test package builds
git add PKGBUILD .SRCINFO
git commit -m 'Update to dokku 0.9.9'
git push
```

> If there is something unclear simply ask @morrisjobke for help.
