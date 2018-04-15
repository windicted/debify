# debify

This is a cross-platform utility for creating DEB-packages. It is written in
pure JavaScript and doesn't rely on any system utilities (such as `dpkg`, `ar`,
`tar`, etc), so it can be used on MacOS, Windows, and GNU/Linux.

Initially `debify` was intended to be used for creating DEB-packages from
single-page web applications, so that they can be installed into Debian based
GNU/Linux distributions in a proper way, i.e. using standard package managers
like `apt`, `aptitude` or `dpkg`, but since it's a general tool, you can
"debify" anything you want.

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
`CONTROL_DIRECTORY` which must contain at least `control` file—the [binary
package control file](https://www.debian.org/doc/debian-policy/#binary-package-control-files-debian-control)
with the control fields for the package. There are five mandatory control
fields: `Package`, `Version`, `Architecture`, `Maintainer`, and `Description`.
The fields can have hard-coded values or can refer to environment variables
using `${VARIABLE_NAME}` syntax. All other metadata files (such as `conffiles`,
`postinst`, `postrm`, `preinst` and `prerm`) is used as well if present in
`CONTROL_DIRECTORY`.

## Example

Let's say there is a web application (named `example`) which consists of three
files: `index.html`, `index.js` and `index.css`. To create a DEB-package which
installs them into `/var/www/example` directory, we will need a `control` file
like this one:

```
Package: example
Version: 1.0.0${SUFFIX}
Architecture: all
Maintainer: John Doe <john.doe@example.com>
Description: An example web-application
```

Also we will need `data` and `debian` directories. We will place `index.html`,
`index.js` and `index.css` into desired subdirectory under the former directory,
and the newly created `control` file into the latter directory. At the end we
should got the following directory structure:

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

No we can launch `debify` utility giving it paths to `data` and `debian`
directories as the fist and second arguments respectively (we assume that
current working directory is `example`):

``` bash
$ debify data debian
```

This will create the DEB-package in the current working directory. According to
the `control` file it will be named `example_1.0.0-all.deb`, but since `Version`
control field refers to `SUFFIX` environment variable, we can use it for
creating release candidates (`example_1.0.0rc1-all.deb`) without changing the
`control` file:

``` bash
$ SUFFIX=rc1 debify data debian
```

