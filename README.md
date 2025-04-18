# README for PyPNG

drj@pobox.com


# INTRODUCTION

PNG module for Python. PyPNG is written entirely in Python.

- PyPNG home page: https://gitlab.com/drj11/pypng/
- PyPNG Documentation: https://pypng.readthedocs.io/en/latest/
- PyPNG mailing list: https://groups.google.com/forum/#!forum/pypng-users


## QUICK START

    import png
    png.from_array([[255, 0, 0, 255],
                    [0, 255, 255, 0]], 'L').save("small_smiley.png")

After that, try `import png` then `help(png)`.
Also, lickable HTML documentation appears in the `html/` directory.
If HTML is no good then you could try the ReST sources
in the `man/` directory.


## INSTALLATION

PyPNG is pure Python and has no dependencies.
It requires Python 3.5 or any compatible higher version.

To install PyPNG package via pip use:

    python -m pip install git+https://gitlab.com/drj11/pypng@pypng-0.20220715.0

After install use

    import png

to access the `png` module in your Python program.

You can also install from source using `setuptools`.
PyPNG uses `setup.cfg` and `pyproject.toml` to record its
configuration.

To install from (version controlled) sources using a suitable
version of `pip`:

`cd` into the directory and execute the command:

    python -m pip install .

PyPNG is so simple, that you don't need installation tools.
You can copy `code/png.py` wherever you like.
It's intended that you can copy `png.py` into
your application and distribute it.
The following `curl` command should copy the latest version into
your current directory:

    curl -LO https://gitlab.com/drj11/pypng/-/raw/main/code/png.py


## RELEASE NOTES

(For issues see https://gitlab.com/drj11/pypng/-/issues/ )


### Release (the next)


### Release 0.20231004.0

A breaking change to the API is that the `lenient` flag has
been removed.
PNG chunk checksum errors are now warnings (not exceptions) and
can be controlled by Python’s warnings mechanism.

`pripamtopng` is upgraded to support two previously unsupported
NetPBM formats: plain PPM file (magic number P3); and
binary PBM (magic number P4).
The plain version of PBM is still unsupported,
probably because the author finds that 0=white convention annoying.

`priforgepng` has a new `rnd` pattern, which is random:

    priforgepng rnd | kitty icat

A restriction on writing `PLTE` chunks has been removed.
Previously a `PLTE` chunk would only be written when it was
required, with a colour type 3 PNG.
Colour types 2 and 6 can, according to the spec, both
have `PLTE` chunks (it is advisory for those colour types).
It is now possible to specify `colormap=False` and
`palette=list(...)`.
Previously various crashes would have happened in this case;
and in particular, that would happen if the `info` dictionary
from a colour type 3 PNG image (with a palette) was read using
`asRGB()` and passed unchanged to `Writer` (which is the sort of
thing that should ordinarily work).
See https://gitlab.com/drj11/pypng/-/issues/121

Numpy arrays can once again be used as a palette; this
was broken due to a numpy change that deliberately broke the conversion
of numpy arrays to `bool`.
See https://gitlab.com/drj11/pypng/-/issues/129


### Release 0.20220715.0

Development moved to gitlab: https://gitlab.com/drj11/pypng

If you pass an empty file to PyPNG
it now raises the builtin Python exception `EOFError`.
This should make it easier to diagnose _empty file_ problems separately
from genuine format errors
(which use `png.FormatError`).
This is a slightly breaking change to the API.

New `prirowpng` tool to join PNG images in a row left-to-right
(old internal `pipcat` tool).

New `pricolpng` tool to join PNG images in a column top-to-bottom.

Support for plain PGM files (magic number P2) added to `pripamtopng`.

New `priplan9topng` tool to convert from Plan 9 image format to PNG.
In reality this has been lurking in the codebase for years, but
has recently been converted to Python 3.
The author has only a limited collection of Plan 9 images,
which limits the testing that can be done.
The author welcomes bug reports for Plan 9 images.

The `priplan9topng` tool has an even more experimental option
`--font` which converts Plan 9 subfont files to a sequence of
PNG files.


### Release 0.0.21

Support for Python 2 is dropped.
Python 3.5 and onwards are supported.
Some of the ancillary tools are modified to work on Python 3.

Installs via wheel files.

`prichunkpng` command line tool now has some new options to add
chunks:
- `--iccprofile` to add a `iCCP` chunk (ICC Profile);
- `--physical` to add a `pHYs` chunk,
  specifying the intended pixel size;
- `--sigbit` to add a `sBIT` chunk,
  specifying the encoded significant bits;
- `--transparent` to add a `tRNS` chunk,
  specifying the transparent colour.

`priditherpng` command line tool standardised and
converted to Python 3.

`pripngtopam` tool now has a `--plain` option to output plain PGM
and PPM formats. The `topam` part of the name is a bit of a
misnomer: when possible (L and RGB PNG files) the tool will
output either a PGM (grey) or a PPM (RGB) file. Essentially all
tools that can process a PAM file can also process a PGM or a
PPM file. PAM files cannot be _plain_ so using the option
will raise an error in the case where a true PAM file is
written.

Better error messages when you write the wrong number of rows.

(Slightly experimentally) running the `png` module as a command
line tool, with `python -m png`, will report the version and
file location of the `png` module.


### Release 0.0.20

Support for earlier versions of Python is dropped.
Python 3.4 and onwards are supported.
Python 2.7 also works, but this is the last
release to support any version of Python 2.

Cython code is removed, which simplifies the implementation.

Removed the (optional) dependency `setuptools`.

Changed the default for `png.Writer` to be greyscale.

Removed 3D support from `.from_array()`.


### Release 0.0.19

Support for earlier versions of Python is dropped in order to
simplify the code.
From the Python 3.x series all versions from 3.2 onward are supported.
From the 2.x series only Python 2.6 and 2.7 are supported.

Code cleaned.
Tests renamed and more organised.
Generally Flake 8 compliant.
Fewer special cases for ancient versions of Python.

The row length is checked when writing PNG files.
Previously it was possible to write badly formed PNG files
by passing in rows of the wrong length.

`pHYS` chunk is now processed.

The `Writer()` interface now supports source pixels
that have a different bitdepth for each channel.
To exploit this, pass in a tuple for the bitdepth argument.

Ancillary tools regularised and simplified.

`pripamtopng` command line tool converts NetPBM PNM/PAM files to PNG.
Previously `png.py` did this.
Note that only one input file is converted,
if you have intensity and opacity in separate files,
you have two options:
either use `pamstack` to convert them into a single PAM file and
convert that, or
convert each file to PNG, then use `priweavepng` to weave them together.
Both will work.

`pripngtopam` command line tool converts PNG to NetPBM PNM/PAM file.
Previously `png.py` did this.

`python -m pngsuite` is now a command line tool to
write various images from the PNG suite of test images.
Previously this was possible using `gen`.

`priweavepng` command line tool performs channel extraction across
multiple images.
This is a more general version of `pipstack` (which has been removed),
and is inspired by `pamstack` from NetPBM.

The `--interlace` option available on many previous tools is
now only available on `priweavepng`,
making it the preferred tool for generating interlaced PNGs.

`prichunkpng` command line tool adds and deletes chunks and
"intelligently" knows about transparent, gamma, background chunks.
It is the preferred tool for adding those chunks,
which was previously possible using various options of other tools.

`gen` has been renamed to `priforgepng`.

`priforgepng` uses centre pixel sampling, which means that a 256 pixel
wide 8-bit gradient takes on all possible 256 values.
It also improves output for very small images.

`priforgepng` uses `Fraction` for internal maths meaning that
the stripe patterns are accurate and do not have loose pixels.

`priforgepng` only outputs greyscale PNG files
(but see next item for a feature to generate colour PNGs).

`priforgepng` can output multiple PNGs onto the same stream.
This aligns well with a feature of `priweavepng` which
accepts multiple PNGs from stdin.
LA, RGB, and RGBA test images can be generated by
piping `priforgepng` into `priweavepng`:
`priforgepng RTL RTR RBR | priweavepng - - -`
will generate an RGB PNG.


### Release 0.0.18

Thanks to `github.com/sean-duffy`,
`.from_array()` can now take a 3D array.

Converting to PNMs was broken in Python 3; this is now fixed.
Issue 26: https://gitlab.com/drj11/pypng/-/issues/26


### Release 0.0.17

Various fixes when running on Python 3 and Windows.
Merging pull requests from `github.com/ironfroggy` and
`github.com/techtonik`,
and merging by hand a commit from `github.com/Scondo`.


### Release 0.0.16

Compatible with nose: `nosetests png.py` now works.

Allow any "file-like" object as an input.

Handle newlines in `texttopng`.


### Release 0.0.15

Fixed various URLs to point at github.com instead of googlecode.


### Release 0.0.14

When using `png.py` as a command line tool,
it can now produce non-square test images.

PyPNG now installs "out of the box" on Python 3 on a plain install
(previously `distribute` or `pip` was required).

PyPNG welcomes the following community contributions:

  Joaquín Cuenca Abela speeds up PNG reading when Cython is available.

  Josh Bleecher Snyder adds a lenient mode
  which has relaxed checksum checking.

  nathan@dunfield.info fixed a problem writing files
  when using the command line tool on Windows (Issue 62).

The following issues have been fixed:

  On github:

  Issue 6: Palette processing is annoying

  On googlecode:

  Issue 62: Problem writing PNG files on Windows

Development has moved from googlecode to github.
All issues below here, and the one immediately above,
are from the googlecode issue tracker.
All newer issue should be on github.


### Release 0.0.13

PyPNG now installs "out of the box" on Python 3.
Thanks to simon.sapin@kozea.fr and nathan@dunfield.info for the patch.

The following issues have been fixed:

  Issue 63: setup.py does not use 2to3
  Issue 64: Typo in documentation


### Release 0.0.12

PyPNG now works on Python 3 if you use the `2to3` tool.
Fix for converting grey images to RGBA.

The following issues have been fixed:

  Issue 60: Greyscale images not properly being converted to RGBA
  Issue 61: Doesn't work on Python 3


### Release 0.0.11

Added the "How Fast is PyPNG" section to the documentation.
Changed it so that more PNG formats return their rows as
Python `array.array` instances.


### Release 0.0.10

Fix for read_flat method (broken for ages).

The following issues have been fixed:

  Issue 56:  read_flat broken


### Release 0.0.9

Tentative fix for a deprecation warning on 64-bit Python 2.5 systems.
Conversion tool for Plan 9 images.

Issue 54 (below) is tentative.
The PyPNG developers have been unable to reproduce the error
(as it seems to be on 64-bit Python 2.5 systems);
any user reports would be most welcome.

The following issues have been fixed:

  Issue 54:  Deprecation warnings when using pypng.
  Issue 55:  Cannot convert Plan 9 images.


### Release 0.0.8

Mostly more robust to dodgy input PNGs,
as a result of testing with `brokensuite`.
One fixed bug was a critical:
an infinite loop for a least one input (Issue 52 below).

The following issues have been fixed:

  Issue 47:  Leading blanks when using write_packed.
  Issue 48:  pipdither fails when input has no gamma chunk.
  Issue 49:  pipdither fail with 1-bit input.
  Issue 50:  pipchunk adds second gamma chunk.
  Issue 51:  piprgb and pipasgrey fail for color mapped images.
  Issue 52:  Some inputs cause infinite loop.


### Release 0.0.7

Better documentation (in `html/ex.html` mostly) for NumPy integration.

The following issues have been fixed:

  Issue 46:  Unclear how to get PNG pixel data into and out of NumPy.


### Release 0.0.6

NumPy integer types now work.

The following issues have been fixed:

  Issue 44:  Cannot use numpy.uint16 for pixel values.


### Release 0.0.5

`sBIT` chunks are now handled,
meaning that PyPNG can handle any (single) bit depth from 1 to 16
from end to end.

The following issues have been fixed:

  Issue 28:  Does not add sBIT chunk.
  Issue 36:  Ignores sBIT chunk when present.


### Release 0.0.4

PyPNG now works on Python 2.2
(significant for Symbian users as PyS60 is based on Python 2.2).
Not all features are supported on Python 2.2.

The following issues have been fixed:

  Issue 16:  Source and doc uses 'K' where it should use 'L'.
  Issue 32:  Does not accept packed data.
  Issue 33:  Cannot create greyscale PNG with transparency.
  Issue 35:  Does not work on Python 2.2.


### Release 0.0.3

Handling PAM files allows end to end handling of alpha channels in
workflows that involve both Netpbm formats and PNG.
PyPNG now works in Python 2.3.

The following issues have been fixed:

  Issue 14:  Does not read PAM files.
  Issue 15:  Does not write PAM files.
  Issue 25:  Incorrect handling of tRNS chunk.
  Issue 26:  asRGBA8 method crashes out for color type 2 images.
  Issue 27:  Fails on Python 2.3.


### Release 0.0.2

Lickable HTML documentation is now provided (see the html/ directory),
generated by Sphinx.

The following issues have been fixed:

  Issue 8:  Documentation is not lickable.
  Issue 9:  Advantage over PIL is not clear.
  Issue 19: Bogus message for PNM inputs with unsupported maxval
  Issue 20: Cannot write large PNG files


### Release 0.0.1

Stuff happened.


## MANIFEST

- .../ - top-level crud (like this README, and setup.py).
- .../asset - assets (needed for testing)
- .../code/ - the Python code.
- .../man/ - manuals (in source/plain-text).
- .../proc/ - documented procedures (release procedure).


## REFERENCES

- Python: www.python.org
- PNG: http://www.w3.org/TR/PNG/

## END
