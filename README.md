# ECL crosscompilation
Crosscompiling ECL (from Linux to Windows)

## Remarks

This is **not an official ECL installer**, I am not a member of the ECL team,
just a personal experiment, to show how that works.
(I also do a similiar procedure for the Maxima Windows installer).

It does include an up-to-date version of libgmp, the included (older)
version did not compile. It includes the Mingw libraries, Microsoft
VS redistributable dlls are not needed.

If the compiliation works, you can look in the logfiles (if you are
logged in into Github) and can find an artifact download link at the
end of the "upload" step - the installer (in a zip file).
Unsigned, of course, it cannot be signed in an automatic github CI job.

If you want to use the C compiler interface, you need a C compiler too,
thats not included. E.g. the mingw-w64 compilers from https://winlibs.com/
Since ECL is build using the Mingw (cross) compiler, `compiler:*cc*` is
set to that compiler.

```
> (require 'cmp)

;;; Loading #P"c:/Program Files/ecl-26.3.27/cmp.fas"
("CMP")
> compiler:*cc*

"x86_64-w64-mingw32-gcc"
> (quit)
```


Regards, Wolfgang Dautermann
