# KISS Package Experiment.

This is an alternative package system I am experimenting with. Instead of the usual `PKGBUILD`, `APKBUILD`, `xbps-template` and `Pkgfile` format, this repository explores a more unixy approach.

Each Package is split into multiple files.

```sh
zlib/            # Package name.
├─ build         # Build script.
├─ depends       # Dependencies (one per line).
├─ sources       # Sources (one per line).
├─ version       # Package version.
┘

# Files generated by the package manager.
├─ manifest      # The built package's files and directories.
├─ checksums     # The checksums for the source files.
┘

# Optional files.
├─ post_install  # The script to run after install.
├─ release       # Force a package update for the same version.
┘
```

When a built package is installed, this entire directory tree is copied to `/var/db/puke` where it becomes a database entry. Listing the dependencies for a package is a simple as printing the contents of the `depends` file. Searching for which package owns a file is as simple as checking each `manifest` file.

This new structure also allows the package manager to be stupid simple. POSIX `sh` has no arrays. However, they are mimicked by looping over each line of each file. No more insecure `depends="pkg pkg pkg"` and `for pkg in $depends`.

Instead, the following can be done.

```sh
while read -r depend; do
    # do thing.
done < depends
```

## Table of Contents

<!-- vim-markdown-toc GFM -->

* [`build`](#build)

<!-- vim-markdown-toc -->

## `build`

The `build` file should contain the necessary steps to patch, configure, build and install the package. When at the install step; the variable `$pkg_dir` is available. This variable points to the directory the package manager uses for built packages. Whatever is in this directory will become part of the package's manifest and will be copied to `/` (or `$PUKE_ROOT`).
