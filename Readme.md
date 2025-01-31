# RPM creation and generation scripts
**Originally written by Karl Ingram**

These tools provide a common set of commands and templates for systems administrators to create and maintain RPMs (Redhat and AIX)

## Usage
To create a new RPM run `bin/make-rpm-dirs -r <package>`. This will create `packages/<package>` and populate it with templates of all the files needed to create the basic RPM.

```
<package>/src
<package>/release
<package>/rpmbuild
```

where `<package>` is the name of the rpm to be created.

The `<package>/src` directory is treated as the `root` for your installed files. Place all your files in directories in it to have them installed when the rpm is installed
The `<package>/release` directory contains the actual RPM that's built from your source files.
The `<package>/rpmbuild` directory contains files that are used to contain things like the pre-install/post-install scripts and other RPM specific options. They each contain documentation to help the administrator to complete them appropriately.

Once the installable files and the associated scripts are in place, the `build-rpm` shows what commands need to be run to take the current files in `<package>/src` and create a release directory based on the version number defined on the command line.  This release directory serves the place of revision control for the `<package>/src` directory.
The version number needs to be in the format X.Y.Z (e.g. 1.0.1).  This directory is then used to package up the files under the correct version number.

## The commands in the `bin` directory are

make-rpm-dirs: Creates the directory structure for a new RPM build area and copies the templates into the `<package>/rpmbuild` directory
build-rpm: Spits out the make-release and packager commands that you need to run to do the work
All other files in the `bin` directory are used by these two scripts.

For example:
```
cd Company-GGVCSagent
$ build-rpm -r 1.0.18 -l 'test build'
Run these
../../bin/make-release -r 1.0.18 -l "test build"
../../bin/packager -r 1.0.18
$ ../../bin/make-release -r 1.0.18 -l "test build"
LOG: creating ./1.0.18 dir
$ ../../bin/packager -r 1.0.18
[INFO]: RPM stored in ./release/Company-GoldenGateVCSagent-1.0.18-1.any-x86_64.rpm
$
```

See build-rpm -h for help

```
The <package>/rpmbuild directory will be where the specific install scripts / package name files will be kept
<package>/rpmbuild/rpm-package     defines the package name
<package>/rpmbuild/rpm-pre         defines the pre-install scripts for the rpm
<package>/rpmbuild/rpm-post        defines the post-install scripts for the rpm
<package>/rpmbuild/rpm-preun       defines the pre-uninstall scripts for the rpm
<package>/rpmbuild/rpm-postun      defines the postun-install scripts for the rpm
<package>/rpmbuild/rpm-depends     defines the dependencies for the rpm
```

The format of the files is as if they were part of the rpm spec file.
The `pre/post(un)-install` scripts the format is standard shell
```
if [[ -f /etc/file ]]; then
  print do the following
fi
```

The format of the rpm-depends file is:
```
PreReq: sudo
Conflicts:      pam
```

The format of name name of the rpm-package is
```
Company_package_name
```

# When things go wrong.

The most common problem is that someone tries to rebuild using the same release. 
If you do want to do this there are files that need to be cleaned up in /var/tmp.
They should be pretty obvious
