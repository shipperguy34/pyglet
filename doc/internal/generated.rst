ctypes Wrapper Generation
=========================

The following modules in pyglet are entirely (or mostly) generated from one or
more C header files:

* pyglet.gl.agl
* pyglet.gl.gl
* pyglet.gl.glext_abi
* pyglet.gl.glext_nv
* pyglet.gl.glx
* pyglet.gl.glxext_abi
* pyglet.gl.glxext_nv
* pyglet.gl.wgl
* pyglet.gl.wglext_abi
* pyglet.gl.wglext_nv
* pyglet.window.xlib.xlib
* pyglet.window.xlib.xinerama

The wrapping framework is in ``tools/wraptypes``, and pyglet-specialised batch
scripts are ``tools/genwrappers.py`` (generates xlib wrappers) and
``tools/gengl.py`` (generates gl wrappers).

Generating GL wrappers
----------------------

This process needs to be followed when the wraptypes is updated, the header
files are updated (e.g., a new release of the operating system), or the GL
extensions are updated.  Each file can only be generated a a specific
platform.

Before beginning, remove the file ``tools/.gengl.cache`` if it exists.  This
merely caches header files so they don't need to be repeatedly downloaded (but
you'd prefer to use the most recent uncached copies if you're reading this,
presumably).

On Linux, generate ``pyglet.gl.gl``, ``pyglet.gl.glext_abi`` and
``pyglet.gl.glext_nv`` (the complete user-visible GL
package)::

    python tools/gengl.py gl glext_abi glext_nv

The header files for ``pyglet.gl.gl`` are located in
``/usr/include/GL``.  Ensure your Linux distribution has recent versions
of these files (unfortunately they do not seem to be accessible outside of a
distribution or OS).

The header files for ``pyglet.glext_abi`` and ``pyglet.glext_nv`` are
downloaded from http://www.opengl.org and http://developer.nvidia.com,
respectively.

On Linux still, generate ``pyglet.gl.glx``, ``pyglet.gl.glxext_abi`` and
``pyglet.gl.glxext_nv``::

    python tools/gengl.py glx glxext_abi glxext_nv

The header file for ``pyglet.gl.glx`` is in ``/usr/include/GL``, and
is expected to depend on X11 header files from ``/usr/include/X11``.
``glext_abi`` and ``glext_nv`` header files are downloaded from the above
websites.

On OS X, generate ``pyglet.gl.agl``::

    python tools/gengl.py agl

Watch a movie while you wait -- it uses virtually every header file on the
system.  Expect to see one syntax error in ``PictUtils.h`` line 67, it is
unimportant.

On Windows XP, generate ``pyglet.gl.wgl``, ``pyglet.gl.wglext_abi`` and
``pyglet.gl.wglext_nv``::

    python tools/gengl.py wgl wglext_abi wglext_nv

You do not need to have a development environment installed on Windows.
``pyglet.gl.wgl`` is generated from ``tools/wgl.h``, which is a hand-coded
header file containing the prototypes and constants for WGL and its
dependencies.  In a real development environment you would find these mostly
in ``WinGDI.h``, but wraptypes is not quite sophisticated enough to parse
Windows system headers (see below for what needs implementing).  It is
extremely unlikely this header will ever need to change (excepting a bug fix).

The headers for ``pyglet.gl.wglext_abi`` and ``pyglet.gl.wglext_nv`` are
downloaded from the same websites as for GL and GLX.

Generated GL wrappers
---------------------

Each generated file contains a pair of markers ``# BEGIN GENERATED CONTENT``
and ``# END GENERATED CONTENT`` which are searched for when replacing the
file.  If either marker is missing or corrupt, the file will not be modified.
This allows for custom content around the generated content.  Only ``glx.py``
makes use of this, to include some additional enumerators that are not
generated by default.

If a generating process is interrupted (either you get sick of it, or it
crashes), it will leave a partially-complete file written, which will not
include both markers.  It is up to you to restore the file or otherwise
reinsert the markers.

Generating Xlib wrappers
------------------------

On Linux with the Xinerama extension installed (doesn't have to be in use,
just available), run::

    python tools/genwrappers.py

This generates ``pyglet.window.xlib.xlib`` and
``pyglet.window.xlib.xinerama``.

Note that this process, as well as the generated modules, depend on
``pyglet.gl.glx``.  So, you should always run this `after` the above GL
generation.


