# ECL crosscompilation
Crosscompiling ECL (from Linux to Windows)

## Remarks

This is **not an official ECL installer**, I am not a member of the ECL team,
just a personal experiment, to show how that works.
(I also do a similiar procedure for the Maxima Windows installer).

ECL is compiled with the bytecode compiler, without a C compiler and
without ASDF (got an error, when trying that) - using the flags:
`--with-cmp=no --with-bytecmp=yes -with-asdf=no`

It does include an up-to-date version of libgmp, the included (older)
version did not compile too. It included the Mingw libraries, Microsoft
VS redistributable dlls are not needed.

Regards, Wolfgang Dautermann
