Nim stb_image Wrapper
=====================

Please note before using this wrapper is that it is still in development.
Though eventually it should be complete.

This is a Nim wrapper of the stb_image single header file libraries
(`stb_image.h` and `stb_image_write.h`).  It is still a work in progress, so
all functions/features might not be fully exposed yet; do hold tight.


Official repo page can be found here:
https://gitlab.com/define-private-public/stb_image-Nim

All other sites should be considered mirrors.

The used header files are included in this repo but you may provide your own.
The versions that are used are:

 - `stb_image.h`: v2.13
 - `stb_image_write.h`: v1.02

They can be found here: https://github.com/nothings/stb

If something is out of date or incorrect.  Please notify me on the GitLab issue
tracker so I can fix it.  Though if something like a header file is out of date,
you should be able to simply exchange it out (unless something was changed in
the API).


Callback Functions, ZLIB client, & stbi_image_resize
----------------------------------------------------

Originally I was going to include these in the wrapper, but I decided to drop
support for them.  I wasn't sure how to go about wrapping the callback
functions.  As for `stbi_image_resize`, I felt that the scope of this wrapper
should be focused on image loading and saving, not processing.

Though if someone would like these included in the wrapper I am open to pull
requests.


License
-------

In the nature of Sean Barret (a.k.a. "stb") putting his libraries in public
domain, so is this code.  Specifically it falls under the "Unlicense."  Please
check the file `LICENSE` for details.



Other Notes
-----------

TODO:
 - [ ] wrap `stb_image.h`
   - [x] Make a list of functions/exposables
   - [ ] info functions
   - [ ] premultiply function
   - [ ] iphone function
   - [ ] flip function
   - [ ] 16 bit functions
   - [ ] float functions
   - [ ] hdr functions
 - [ ] Provide some examples of each
   - [ ] Image read example
   - [ ] Image write example
 - [ ] Figure out how to handle including the header files
   - Embedd the source in the .nim files?
   - Tell the user they need to do a `--cincludes:.`?
 - [ ] A very simple "How to use" section
   - [ ] including the header files
 - [ ] Test on OS X
 - [ ] Test on Windows


Untested Functions
------------------

All functions should have tests for them, but I wasn't able to find some test
images (namely for the HDR) functions.  So they do have bindings for
completeness, but they haven't been proven to work.  If you do happent to have
some very simple test images I could use, please provide them.  Here is the list
of functions that are untested.

From `stb_image.h`:
 -

From `stb_image_write.h`:
 - `stbWriteHDR()`

