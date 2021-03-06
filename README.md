Nim stb_image Wrapper
=====================

This is a Nim wrapper of the stb_image single header file libraries
(`stb_image.h` and `stb_image_write.h`).

Official repo page can be found here:
https://gitlab.com/define-private-public/stb_image-Nim

All other sites should be considered mirrors, though if you log an issue on
GitHub, I'll probably address it.

The used header files are included in this repo (they end in the name
`_header.nim`) but you may provide your own.
The versions that are used are:

 - `stb_image.h`: v2.16
 - `stb_image_write.h`: v1.07

They can be found here: https://github.com/nothings/stb

If something is out of date or incorrect.  Please notify me on the GitLab issue
tracker so I can fix it.  Though if something like a header file is out of date,
you should be able to simply exchange it out (unless something was changed in
the API).


zlib client
-----------
For PNG support, stb_image requires a functioning zlib compressor/decompressor,
and one is built in.  I'd like to thank RSDuck for exposing the functions with
some Nim friend wrappers.  Se you want to see how to use it, check `tests.nim`
for some samples.


Callback Functions & stbi_image_resize
----------------------------------------------------

Only the callback funtions for `stbi_image_write` are implemented.  I'd like to
thank Eduardo Bart (@edubart) for help on this.  As for `stbi_image_resize`, I
felt that the scope of this wrapper should be focused on image loading and
saving, not processing.

Though if someone would like these included in the wrapper I am open to pull
requests.


License
-------

In the nature of Sean Barret (author of stb) putting his libraries in public
domain, so is this code.  Specifically it falls under the "Unlicense."  Please
check the file `LICENSE` for details.


How To Use
----------

I've tried to write this to be as close as possible to the orignal C API, so if
you're already familiar with it, there should be no hassle.  If you don't all of
the functions are documented in `stb_image/read.nim` and `stb_image/write.nim`.

Import what you want to use.  I recommend adding the `as` semantic:

```nim
import stb_image/read as stbi
import stb_image/write as stbiw
```

An original C call would look like this:

```c
#include "stb_image.h"

// Get the image data
int width, height, channels;
unsigned char *data = stbi_load("kevin_bacon.jpeg", &width, &height, &channels, STBI_default);

// Do what you want...

// Cleanup
stbi_image_free(data);
```

But becomes this:

```nim
import stb_image/read as stbi

var
  width, height, channels: int
  data: seq[uint8]

data = stbi.load("kevin_bacon.png", width, height, channels, stbi.Default)
# No need to do any GC yourself, as Nim takes care of that for you!
```

Functions that had names `like_this` have been turned `intoThis`.  That `stbi_`
portion has also been dropped.

If you want to write pixels, it's like what you see here, but in reverse:

```nim
import stb_image/write as stbiw

# Stuff some pixels
var data: seq[uint8] = @[]
data.add(0x00)
data.add(0x80)
data.add(0xFF)

# save it (as monochrome)
stbiw.writeBMP("three.bmp", 3, 1, stbiw.Y, data)
```

Some of the functions (or variables) that effect the library globally are still
there (e.g. `setFlipVerticallyOnLoad()` in `stb_image`), but some other has been
moved to functions calls as to not act in a global manor (e.g. the `useRLE`
parameter for `writeTGA()` in `stb_image/write`).

I also recommend reading through the documentation at the top of the original
header files too, as they give you a bit more of a grasp of how things work and
the limitations of `stb_image`.

All of the functions that can do image reading (which are, well, only found in
`stb_image/read.nim`) will throw an `STBIException` if there was an error
retriving the image data.  The `.message` field of the exception will contain
some slight information into what went wrong.


Future Plans
------------

 - Return a data structure that descrives an image instead of a sequence of
   bytes/shorts/floats from the functions.  This may be a much more natural way
   for the programmer to get an image and make a little more sense than
   returning results in both the "return," statement and "var," parameters.
   Such a system might have a data structure like this:

   ```nim
   type
     # `T` should only be uint8, uint16, or float32
     STBImage[T]* = ref object of RootObj
       width*: int
       height*: int
       channels*: int # One of the values from `stb_image/components.nim`
       pixelData*: seq[T]
   ```

   And the Nim-Friendly functions would change from this:

   ```nim
   # Data
   var
     width, height, channels: int,
     pixels: seq[uint8]

   # Load the image
   pixels = stbi.load("kevin_bacon.jpeg", width, height, channels, stbi.Default)
   ```

   Over to this:

   ```nim
   var image = stbi.load("kevin_bacon.jpeg", stbi.Default)
   ```

   It may also solve an issue with the pixel data being copied (unecessarly)
   with the current wrappers.  See this thread in the Nim Forum for details:
   http://forum.nim-lang.org/t/2665

   The only thing I don't like about this is that it would break the familiarity
   with the original C API.  I don't want to maintain multiple functions that
   have the same functionality so I would be removing those orignal bindings.

   Trying to figure out how to make the `STBImage` type play nice with the
   write functions might be a little more work too (e.g. validating there is
   enough data and correct parameters).

   I'd like to get some comments on this before moving forward with it.

 - I really would like add unit tests for the functions listed in the `Untested
   Functions` section to verify they work, but I'm in need of some very simple
   test images.

 - Add wrappers/bindings for the `stb_image_resize.h` library.  It's part of the
   STB toolkit (and falls under it's "image," section), but it wasn't related to
   image IO so I decided to leave it out.  It also looked like quite a bit of
   work to add in.  If someone wants to submit a pull request, I'll review it.


Untested Functions
------------------

All functions should have tests for them, but I wasn't able to find some test
images (namely for the HDR) functions.  So they do have bindings for
completeness, but they haven't been proven to work.  If you do happent to have
some very simple test images I could use, please provide them.  Here is the list
of functions that are untested.

From `stb_image.h`:
 - `load16()`
 - `loadFromFile16()`
 - `load16FromMemory()`
 - `loadF()`
 - `loadFFromMemory()`
 - `loadFFromFile()`
 - `HDRToLDRGamma()`
 - `HDRToLDRScale()`
 - `LDRToHDRGamma()`
 - `LDRToHDRScale()`
 - `isHDRFromMemory()`
 - `isHDR()`
 - `isHDRFromFile()`
 - `setUnpremultiplyOnLoad()`
 - `stbiConvertIPhoneRGBToPNG()`


From `stb_image_write.h`:
 - `writeHDR()`


Thanks
------
 - Eduardo Bart (@edubart), for help with a segfault issue and write callback
   functions.

