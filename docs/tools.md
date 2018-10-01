# Tools

PyCdlib comes with several tools built on the main library API.  These tools are useful in themselves, but also give (relatively) simple examples on how to use the PyCdlib API in a real application.

## pycdlib-genisoimage
The `pycdlib-genisoimage` tool aims to be a flag-compatible drop-in replacement for the venerable [genisoimage](https://linux.die.net/man/1/genisoimage) tool, with the big exception that it uses PyCdlib under the hood.  Please see the man page [pycdlib-genisoimage](pycdlib-genisoimage.html) for more information.

## pycdlib-explorer
The `pycdlib-explorer` tool gives the user a convenient, shell-like interface for opening, exploring, and manipulating existing ISO files.  Please see the man page [pycdlib-explorer](pycdlib-explorer.html) for more information.

## pycdlib-extract-files
The `pycdlib-extract-files` tool is a convenient way to extract particular files or directories from an ISO (including the entire ISO).  Please see the man page [pycdlib-extract-files](pycdlib-extract-files.html) for more information.
