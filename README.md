# EXAMPLE
Simple ILE CL example program built and installed via Makefile

-----

This is an example of repository structured in order to be served by **PASERIE/INSTALL** utility (by *Andrea Ribuoli*).

Rather that describing (and issuing) all the build steps in the conventional **QCLSRC/BUILD.CLLE** script, this same ILE CL, once automatically compiled in QTEMP, will use the services of the *newly* packaged *TMKMAKE* command located in the **TMKMAKE** library. 

This way the build dependencies can be properly specified.

The conventional invocation of TMKMAKE from ILE CL BUILD script sets three macros first:

```
 CHGVAR VAR(&MACRO1) VALUE('SRCLIB=' *CAT &SRCLIB)
 CHGVAR VAR(&MACRO2) VALUE('BLDLIB=' *CAT &BLDLIB)
 CHGVAR VAR(&MACRO3) VALUE('TGTLIB=' *CAT &TGTLIB)
``` 

This way the developer in charge of writing the Makefile (**QMAKSRC/BUILD.TXT**) can extend the role of **&SRCLIB**, **&BLDLIB** and **&TGTLIB**.

```
All:  PART_A 

PART_A: PART_A.MODULE PART_B.MODULE PART_C.MODULE
      CRTPGM PGM(&TGTLIB/PART_A) +
      MODULE(&BLDLIB/PART_A &BLDLIB/PART_B &BLDLIB/PART_C) +

PART_A.MODULE: PART_A.QCLSRC
      CRTCLMOD MODULE(&BLDLIB/PART_A) +
      SRCFILE(&SRCLIB/QCLSRC) +
      OUTPUT(*NONE)

PART_B.MODULE: PART_B.QCLSRC
      CRTCLMOD MODULE(&BLDLIB/PART_B) +
      SRCFILE(&SRCLIB/QCLSRC) +
      OUTPUT(*NONE)

PART_C.MODULE: PART_C.QCLSRC
      CRTCLMOD MODULE(&BLDLIB/PART_C) +
      SRCFILE(&SRCLIB/QCLSRC) +
      OUTPUT(*NONE)

```
