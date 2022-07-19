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
      MODULE(&BLDLIB/PART_A &BLDLIB/PART_B &BLDLIB/PART_C)      

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

## METHODOLOGY

The **PASERIE** library is made up of three tools:

* PASERIE/INSTALL
* PASERIE/LIBCLONE
* PASERIE/INSTALLOC


### PASERIE/INSTALL

This tool installs GitHub repositories as IBM i native libraries that adhere to the following few conventions:

* the repository includes a **GUIDANCE.TXT** file in the root of the repository
* a **QCLSRC** sub\-directory exists in the root of the repository
* a **BUILD.CLLE** ILE CL source file exists in the *QCLSRC* sub\-directory

The INSTALL utility, having received the *GitHub user* and the *repository name*, downloads the file named *GUIDANCE.TXT* and interprets its rows as a list of source files to be downloaded.
All the listed files are re\-created in **QTEMP**.
Then the utility attempts to compile *QTEMP/QCLSRC(BUILD)* source member.
If this compilation is successful the utility gives the generated executable direct control by calling it. 
 
From this moment on the BUILD source code is responsible for how the installation will proceed.  


### PASERIE/LIBCLONE

This tool helps in developing the repository content so that all the accessory files are **auto\-generated**
by cloning a native IBM i library only looking for source files and members included.


### PASERIE/INSTALLOC

This tool helps in testing locally the repository content, located in an IFS directory, installing the 
library. The directory can be synchronized with a public (or private) GitHub repository so that standard
installation can be finally tested with PASERIE/INSTALL.


## SAME SOURCE, MULTIPLE TARGET VERSIONS

One of the benefits is that PASERIE tools are available for:

* IBM i 7.2
* IBM i 7.3
* IBM i 7.4 
* IBM i 7.5

This means that the same build process can be performed on different systems.

## TGTRLS

By executing: `PASERIE/INSTALL GIT_USER(itpassdev) PACKAGEN(EXAMPLE)` we will find a library `EXAMPLE`
being created with a program `PART_A` inside.

If our system is at V7R4 we will display (`DSPPGM PGM(EXAMPLE/PART_A)`):

```
 . . .
 Release program created on . . . . . . . . . . :   V7R4M0 
 Release program created for  . . . . . . . . . :   V7R4M0 
 Earliest release program can run . . . . . . . :   V7R4M0 
```

By executing: `PASERIE/INSTALL GIT_USER(itpassdev) PACKAGEN(EXAMPLE) TGTRLS(*PRV)` we will find that the program `PART_A` has been recreated:

```
 . . .
 Release program created on . . . . . . . . . . :   V7R4M0 
 Release program created for  . . . . . . . . . :   V7R3M0 
 Earliest release program can run . . . . . . . :   V7R3M0 
```

By executing: `PASERIE/INSTALL GIT_USER(itpassdev) PACKAGEN(EXAMPLE) TGTRLS(V7R2M0)` we will find that the program `PART_A` has been recreated:

```
 . . .
 Release program created on . . . . . . . . . . :   V7R4M0 
 Release program created for  . . . . . . . . . :   V7R2M0 
 Earliest release program can run . . . . . . . :   V7R2M0 
```

The Makefile that implements this is different from the one before (note the **TGTRLS(&TGTRLS)** option):

```
All:  PART_A 

PART_A: PART_A.MODULE PART_B.MODULE PART_C.MODULE
      CRTPGM PGM(&TGTLIB/PART_A) +
      MODULE(&BLDLIB/PART_A &BLDLIB/PART_B &BLDLIB/PART_C) +
      TGTRLS(&TGTRLS) 

PART_A.MODULE: PART_A.QCLSRC
      CRTCLMOD MODULE(&BLDLIB/PART_A) +
      SRCFILE(&SRCLIB/QCLSRC) +
      TGTRLS(&TGTRLS) OUTPUT(*NONE)

PART_B.MODULE: PART_B.QCLSRC
      CRTCLMOD MODULE(&BLDLIB/PART_B) +
      SRCFILE(&SRCLIB/QCLSRC) +
      TGTRLS(&TGTRLS) OUTPUT(*NONE)

PART_C.MODULE: PART_C.QCLSRC
      CRTCLMOD MODULE(&BLDLIB/PART_C) +
      SRCFILE(&SRCLIB/QCLSRC) +
      TGTRLS(&TGTRLS) OUTPUT(*NONE)
```

## TMKMAKE

Years ago an attempt was made by IBM i to implement a native **make** utility targetting IBM i native development.
I have repackaged the tool using this same PASERIE utility to simplify installation.
The latest version of PASERIE/LIBCLONE generates a *QTEMP/QMAKSRC(BUILD)* file.
This service is limited to ILE CL source files. I am studying what could be required to implement a rudimentary
**automake** logic targetting IBM i. 


