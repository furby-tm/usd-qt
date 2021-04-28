# UsdQt

### Qt support for Pixar USD to allow authoring & plugin loading of GUI components

#### Version Compatibility

- release : (Work in Progress) USD 21.05+

## Project Goals

This project is meant to provide support for the authoring of Qt GUI components
in Universal Scene Description. Creating and building QtWidgets, storing Window
state, and loading of custom application components for ad-hoc use in custom tools.

This project has been picked up to serve as the main GUI framework for the upcoming
Digital Content Creation platform - **WINGG**. And will remain an open source project
so that other software tools can make use of USD's GUI functionality, and GUI components
can be shared and reused accross the USD landscape.

## Contents

This project contains a mixture of pure Python and wrapped C++ code. All Qt code
is written in pure Python to avoid the need to link against Qt and get `moc`
involved in the build process. This may change in the future if deemed
beneficial/necessary.

The compiled extension module provides a set of helper classes that interface
with the USD C++ API to accelerate certain USD queries, operations, etc.

#### Installed Python Packages

- `pxr.UsdQt`: Contains various Qt item data models, helper classes for building
UIs, and some other USD and Qt utility code. The `_bindings` module currently
contains all of the wrapped classes from the compiled extension module.
- `pxr.UsdQtEditors`: Contains various Qt UI elements. These range from
purpose-built display/editor widgets to an extensible outliner UI that also
serves as a good usage example for many of the utilities in `pxr.UsdQt`.
- `treemodel`: Contains a generic item tree and a Qt item model mixin that uses
it, which are used by some of the classes in `pxr.UsdQt`.

## Building/Installation

The recommended (and currently only tested) way to build the project is using
CMake. A `setup.py` file is provided as well, but it has not been tested with
the most recent updates.

#### Build Requirements

- Boost Python
- USD
- TBB

#### CMake Options

- `INSTALL_PURE_PYTHON (BOOL)`: Whether to install the pure Python code in
addition to the compiled extension module. This defaults to `ON`.

#### A note about `UsdQt.py` and `UsdQtEditors.py`

You may notice that the source contains `UsdQt.py` and `UsdQtEditors.py` files
alongside the package source directories. These are "shim" modules that we've
found to be very useful during the development of this project, as they enable
us to do more rapid editing and testing on the pure Python code without needing
to run a CMake build and install between each minor edit. They are not installed
with the project.

## Using `UsdQt` with the USD Python bindings

`UsdQt` uses `pkg_resources` (or `pkgutil` if the former is not available) to
declare `pxr` as a "namespace package" at runtime. This allows it to overlay its
sub-packages onto the same `pxr` package used by the USD Python bindings, so
that the two projects can be installed in separate locations and still work
together.

However, the USD Python bindings do not currently declare `pxr` as a namespace
package in a symmetrical way, which unfortunately means that if the USD Python
bindings are listed before `UsdQt` on the module search path, you will not be
able to import `pxr.UsdQt`. Thus, unless/until the USD Python bindings are
updated to declare `pxr` as a namespace package, you will need to make sure
`UsdQt` is listed before the USD Python bindings on the module search path.

More information on namespace packages in Python can be found in the docs for
`pkg_resources` and `pkgutil`:

https://setuptools.readthedocs.io/en/latest/pkg_resources.html#namespace-package-support

https://docs.python.org/2/library/pkgutil.html

## Using an alternate Qt API

`UsdQt` expects its underlying Qt API to adhere to the Qt5 module layout, and
uses the PySide2 API.

By default, it will attempt to import and use `PySide2`. This can be overridden
by setting the `PXR_QT_PYTHON_BINDING` environment variable to the name of the
API package/module you wish to use instead.

This works with the popular [Qt.py](https://github.com/mottosso/Qt.py) project.

## Planned Future Additions

Here are some of the ideas we've had for future additions to the project. We're
happy to consider new suggestions as well.

- UI components for authoring and working with USD variant sets.
- An adaptor class to translate USD notices to Qt signals.
- A `QSyntaxHighlighter` class for `.usda` syntax.
- A Hydra viewport widget.
