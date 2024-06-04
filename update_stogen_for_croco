# ADAPT STOGEN IN CROCO COMPILATION TESTS

* Problem with the order in which the STOGEN routines are compiled 
- include in `./OCEAN/Makefile` the list of STOGEN routines in `SRCS90 =` in the right order od dependencies
- add a space between `stoexternal` and the coma in all STOGEN routines dependencie calls (because the dependencies in croco are find with grep)

* In `stoexternal.F90` :
- add `#include "cppdefs.h"` at the begining of the script
- change `include 'scalars.h'` to `USE scalars`
- change `include 'grid.h'` to `#include "grid.h"`
- Add `IMPLICIT NONE` and `PRIVATE` to use only explicit variables and to avoid duplicates when including a script 

* In `grid.f` :
- change this section 
```
#ifdef SPHERICAL
      real latr(GLOBAL_2D_ARRAY)
      real lonr(GLOBAL_2D_ARRAY)
```
to :
```
#ifdef SPHERICAL
      real, TARGET :: latr(GLOBAL_2D_ARRAY)
      real, TARGET :: lonr(GLOBAL_2D_ARRAY)
```
- change this section 
```
#ifdef MASKING
      real rmask(GLOBAL_2D_ARRAY)
      real pmask(GLOBAL_2D_ARRAY)
      real umask(GLOBAL_2D_ARRAY)
      real vmask(GLOBAL_2D_ARRAY)
      real pmask2(GLOBAL_2D_ARRAY)
```
to :
```
#ifdef MASKING
      real, TARGET :: rmask(GLOBAL_2D_ARRAY)
      real pmask(GLOBAL_2D_ARRAY)
      real, TARGET :: umask(GLOBAL_2D_ARRAY)
      real, TARGET :: vmask(GLOBAL_2D_ARRAY)
      real pmask2(GLOBAL_2D_ARRAY)
```
