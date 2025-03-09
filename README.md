# rpmbuild-envs

## Modular RPM Build
RPMs are great, building RPMs can suck. Using some symlinks and rpmmacros much of this pain can be relieved.

## Installation
1. Clone this repo to `~/rpmbuild`
    - `git clone blah ~/rpmbuild`
2. Run `install-rpmmacros` to modify the `~/.rpmmacros` file.
    - `~/rpmbuild/install-rpmmacros`

## How it works

In order to separeate builds each build has its own directory located in a user's `~/rpmbuild` a symlink named `root` is created in the directory. This symlink points to the directory of the currently active project.

Now when any `rpmbuild` command is ran it will run in the context of `~/rpmbuild/root`.

This is accomplished by the following line in `~/.rpmmacros`:
```
%_topdir %{getenv:HOME}/rpmbuild/root
```
> **Note:** This should be added automatically via `install-rpmmacros`.

## Creating new builds

Builds can be any folder in the `~/rpmbuild` directory.
```
cd ~/rpmbuild
mkdir myproject
./switch-root myproject
cd root
rpmdev-setuptree
```

## Activating differnet builds
To activate a rpm root use `switch-root`.
```
# in ~/rpmbuild
$ ./switch-root otherproject
```

The `~/rpmbuild/root` symlink will now point to `~/rpmbuild/myproject`.

The `root` symlink can also point to directories **outside** of `~/rpmbuild`.
```
$ pwd
/home/smurf/src/someproject/rpmbuild
$ ~/rpmbuild/switch-root .
$ ls -lash ~/rpmbuild
##### OMITTED #####
0 lrwxrwxrwx.  1 scoles scoles   11 Jan 14 19:59 root -> /home/smurf/src/someproject/rpmbuild
##### OMITTED #####
```
Now when any `rpmbuild` command is ran it will run in the context of `/home/scoles/src/inoc-someproject/rpmbuild`.

## Using srpm files
First be sure to create a package root for the package.
```
$ mkdir mypackage
```
Switch to the newly created package root.
```
$ ./switch-root mypackage
$ cd root
```
Generate the rpm file tree.
```
$ rpmdev-setuptree
```
Place the src rpm in the SRPMS folder.
```
$ cp ~/Downloads/mypackage.src.rpm SRPMS
```
Install srpm as usual.
```
$ rpm -Uvh SRPMS/mypackage.src.rpm
```
