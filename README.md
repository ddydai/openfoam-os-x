# OpenFOAM on OS X

Patches for OpenFOAM compilation on OS X. Detailed installation instructions
can be found in [wiki](https://github.com/mrklein/openfoam-os-x/wiki).

## June 5, 2015

Added patches for OpenFOAM 2.4.0 and 2.4.x (commit b750988).

The patches are an attempt to build OpenFOAM with -Wall -Wextra -std=c++11
flags. The following appears after addition of the flags:

1. C++11 deprecates register storage class. So I have removed it, also flex
   generates code that put this keyword in several places: added sed pipe after
   flex code generation to remove it.
2. Lots of "unused parameter" warnings. Solved this problem commenting out
   names of the unused parameters, like: fvPatch& /\* pp \*/.
3. Lots of "unused variable" warnings. These are due to love of developers to
   do thing like `#include "readTimeControls.H"` in the beginning of the
   solver, though not all created variables are used. Solved the problem by
   introducing `readInitialTimeControls.H` file and partially pruning unused
   chunks of code.
4. Quite a lot of hidden virtual functions warnings. These are due to mess in
   return types and method parameters (`postProcessing/functionObjects` library
   is an example of the problem). Solved by either declaring and implementing
   correct methods, or by "using Class::method" addition.
5. Tried to address constant reference dereferencing problem. Since it is not
   strictly defined behavior and, guess, results depend on compiler. So either
   added method returning pointer (since frequently OpenFOAM does this: const
   T& method() { return \*ptr; } ... a = method(); if (&a) { ... }, in general
   if condition should always be true), or boolean returning function checking
   validity of the pointer.
6. Corrected loop condition in readKivaGrid.H, though since it is there from
   version 1.1 (according to Bruno Santos), guess ether this incorrect loop
   exit condition never lead to any problems, or nobody converts Kiva meshes.
7. Removed trailing white-space in several places.
8. Added of240 and of24x environment setup functions to openfoam-env-setup.sh.
9. From now on, patches for git version will contain commit SHA for which they
   were created.

As usual, build was tested on:

```
alexey at daphne in ~$ sw_vers 
ProductName:	Mac OS X
ProductVersion:	10.10.3
BuildVersion:	14D136
```

with the following compiler

```
alexey at daphne in ~$ clang --version
Apple LLVM version 6.1.0 (clang-602.0.53) (based on LLVM 3.6.0svn)
Target: x86_64-apple-darwin14.3.0
Thread model: posix
```

and with the following third party package versions:

```
boost 1.57.0
cgal 4.6
gmp 6.0.0a
metis 5.1.0
mpfr 3.1.2-p11
open-mpi 1.8.4_1
parmetis 4.0.3
parmgridgen 0.0.1
scotch 6.0.4_1
```

## May 5, 2015

1. As versions 2.1.x and 2.2.x have rather funny memory allocation bug, I have
   only one laptop, and I do not use those versions, further support of patches
   for these versions is dropped.
2. Patches for 2.3.1 and 2.3.x (commit
   [OpenFOAM/OpenFOAM-2.3.x@00eea57](https://github.com/OpenFOAM/OpenFOAM-2.3.x/commit/00eea576852a0b95b772665dc4414b9bc32ce17f)) include:
       - new printStack implementation (so there is no need in Python script for address resolution)
       - update of the code for new version of CGAL
       - corrections in METIS decomposition build logic
       - corrections in fvAgglomerationMethods build logic, so it can use Homebrew-installed ParMGridGen
       - corrections in sigFpe.C, so there is no more infinite loop in parallel run (due to print stack functionality in OpenMPI)
3. Initial foam-extend (commit [ec44c9](https://sourceforge.net/p/openfoam-extend/foam-extend-3.1/ci/ec44c952c9f8841ea5fba2bdb9235a5914c6c1c5/tree/)) patch
   attempt. Installation guide is in [wiki](https://github.com/mrklein/openfoam-os-x/wiki/foam-extend---Homebrew).
4. Homebrew formulae for Mesquite and ParMGridGen.

Build was tested on

```sh
alexey at daphne in ~$ sw_vers 
ProductName:	Mac OS X
ProductVersion:	10.10.3
BuildVersion:	14D136
```

With the following versions of third party packages

```sh
alexey at daphne in ~$ brew ls --versions
bison27 2.7.1
boost 1.57.0
cgal 4.6
gmp 6.0.0a
hwloc 1.9
libmpc 1.0.3
mesquite 2.1.2
metis 5.1.0
mpfr 3.1.2-p11
open-mpi 1.8.4
parmetis 4.0.3
parmgridgen 0.0.1
scotch 6.0.3
```

## Mar. 26, 2015

1. Updated OpenFOAM 2.3.1 patch for new version of Scotch.
2. Added patch to fix Scotch linking. Though patch was tested on 2.3.1, guess
   it will work on 2.3.0 and 2.2.x versions.

## Dec. 13, 2014

Added patch for OpenFOAM 2.3.1. Build was tested on:

```
myself at daphne in openfoam-os-x$ sw_vers
ProductName:	Mac OS X
ProductVersion:	10.10.1
BuildVersion:	14B25

myself at daphne in openfoam-os-x$ clang --version
Apple LLVM version 6.0 (clang-600.0.56) (based on LLVM 3.5svn)
Target: x86_64-apple-darwin14.0.0
Thread model: posix
```
