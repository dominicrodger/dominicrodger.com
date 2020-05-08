---
date: 2013-04-21 20:30:00+00:00
title: Installing PIL on 64-bit Ubuntu
slug: pil-64-bit
---

I've just got a new laptop, and wanted to avoid messing around with
symbolic links in order to install PIL in a virtual environment. I've
just discovered patch, and thought I should automate my process of
installing PIL (a process which previously involved me making a
trivial edit to the `setup.py` file).

<!-- more -->

This is obviously not particularly revolutionary, but I thought it
was quite neat, so thought I'd share it. The script (which is just
bash) looks like this:

```
pip install -I PIL --no-install

cat <<EOF>setup_py.patch
--- setup.py 2013-04-21 21:09:42.349000664 +0100
+++ setup.py.patch 2013-04-21 21:10:20.685000986 +0100
@@ -34,8 +34,8 @@
 # TIFF_ROOT = libinclude("/opt/tiff")

TCL_ROOT = None
-JPEG_ROOT = None
-ZLIB_ROOT = None
+JPEG_ROOT = '/usr/lib/x86_64-linux-gnu'
+ZLIB_ROOT = '/usr/lib/x86_64-linux-gnu'
 TIFF_ROOT = None
 FREETYPE_ROOT = None
 LCMS_ROOT = None
EOF

patch $VIRTUAL_ENV/build/PIL/setup.py < setup_py.patch
pip install -I PIL --no-download
```

To use this, just save this as a bash script, and run it with an
active virtualenv.

I've got another script which uses a similar approach to build Python
2.6 from source (a process which is necessary for running multiple
versions of Python on Ubuntu), which I'll share another day (once
I've figured out if I can adjust the approach to install 2.4, 2.5,
and 3.3[^1]).

[^1]: Ubuntu 12.10 currently ships with Python 3.2 as the readily
      available version of Python 3.
