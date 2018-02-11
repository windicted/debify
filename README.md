# debify

This is a cross-platform utility for creating DEB-packages. It is written in
pure JavaScript and doesn't rely on any system utilities (such as `dpkg`, `ar`,
`tar`, etc), so it can be used on MacOS, Windows, and GNU/Linux.

Initially `debify` was intended to be used for creating DEB-packages from
single-page web applications, so that they can be installed into Debian based
GNU/Linux distributions in a proper way, i.e. using standard package managers
like `apt`, `aptitude` or `dpkg`. But since it's a general tool, you can
"debify" anything you want to be a DEB-package.

## Installation

``` bash
$ npm install -g debify
```

## Usage

This utility requires two arguments: `DATA_DIRECTORY` and `CONTROL_DIRECTORY`:

``` bash
$ debify <DATA_DIRECTORY> <CONTROL_DIRECTORY>
```

It creates a DEB-package using contents of `DATA_DIRECTORY` and metadata from
`CONTROL_DIRECTORY` which must at least contain a well-formed `control` file.
All other standard control files (such as conffiles, postinst, postrm, preinst
and prerm) will be used as well if present. The result DEB-package is placed
into current working directory and its name is up to the contents of the
`control` file.

## Example

Let's imagine that there is a single-page web application (named `example`)
which consists of three files: `index.html`, `index.js`, and `index.css`. To
create a DEB-package which will install them into `/var/www/example` directory,
we need to create a directory structure like this one:

```
example
├── data
│   └── var
│       └── www
│           └── example
│               ├── index.css
│               ├── index.html
│               └── index.js
└── debian
    └── control
```

Except for the files of the web application itself there is a `control` file.
The file must comply with [binary package control file](https://www.debian.org/doc/debian-policy/#binary-package-control-files-debian-control),
and has to include at least five fields: `Package`, `Version`, `Architecture`,
`Maintainer`, and `Description`:

```
Package: example
Version: 1.0.0
Architecture: all
Maintainer: John Doe <john.doe@example.com>
Description: An example web-application
```

To create the DEB-package (according to our `control` file it will be named
`example_1.0.0-all.deb`) we have only to change the current working directory
to `example` and run `debify`:

``` bash
$ debify data debian
```
