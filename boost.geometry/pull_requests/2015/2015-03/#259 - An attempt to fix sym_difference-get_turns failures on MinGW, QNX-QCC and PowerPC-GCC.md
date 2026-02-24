# #259 An attempt to fix sym_difference/get_turns failures on MinGW, QNX/QCC and PowerPC/GCC [Closed]

> Username: awulkiew  
> Created at: 2015-03-11 03:36:57 UTC  
> Updated at: 2015-03-12 19:06:52 UTC  
> Closed at: 2015-03-11 18:05:41 UTC  
> Url: https://github.com/boostorg/geometry/pull/259  

This is an attempt to fix an issue observable on [MinGW on Win](http://www.boost.org/development/tests/develop/developer/output/MinGW-w64-4-6%20jc-bell-boost-bin-v2-libs-geometry-test-algorithms-set_operations-sym_difference-sym_difference_linear_linear-test-gcc-mingw-4-6-3-debug.html), probably [QCC on QNX](http://www.boost.org/development/tests/develop/developer/output/NA-QNX650-SP1-x86-boost-bin-v2-libs-geometry-test-algorithms-set_operations-sym_difference-sym_difference_linear_linear-test-qcc-4-4-2_x86-debug-debug-symbols-off.html) and maybe [GCC5.0 on PowerPC](http://www.boost.org/development/tests/develop/developer/output/trippels_powerpc64le_gcc-5-0_c++11-boost-bin-v2-libs-geometry-test-algorithms-set_operations-sym_difference-sym_difference_linear_linear-test-gcc-5-0-0-release-address-model-64-architecture-power-pch-off.html).  
  
Consider a side calculation (`side_by_triangle`) for points `({13, 0.3}, {14, 0.4}, {20, 1})`. Internally calculated value of a determinant is `-2.2204460492503131e-016` on MSVC (there is no problem on the majority of tested compilers so I'm guessing that the value is similar for them). However for MinGW (I tested only this one from the "failing" ones mentioned above) the value is `-2.7755575615628914e-016`, but only if it's calculated as one expression (see the code). Maybe the rvalues are handled differently? Anyway if temporary values are explicitly defined and used the result is the same as the one calculated by the code compiled by MSVC (and probably the majority of compilers).  
  
I'm not sure what's the cause, a bug, optimization or something else, I stopped digging at this stage. Maybe we should compare the assemblies and other compilers? Or maybe someone has an idea what's the cause? I also haven't checked the release build or various optimization parameters. So I don't find this workaround as a solid one. It's rather a test or a temporary workaround.  
  
I think that such cases should be handled with more care. There is a class of issues related to the way how very small values of sides and cramer's rule are handled. If they were supported somehow more robustly by the intersection strategy then I'm guessing that this workaround wouldn't be needed and could be reverted.

---

## Comment 1

> Username: mkaravel  
> Created_at: 2015-03-11 06:11:16 UTC  
> Url: https://github.com/boostorg/geometry/pull/259#discussion_r26190852  

Have you tried writing this as:  
  
```  
    return (rt(ux) * rt(vy)) - (rt(uy) * rt(vx));  
```  
  
It should give the same results as your alternative code.

---

## Comment 2

> Username: awulkiew  
> Created_at: 2015-03-11 14:34:09 UTC  
> Url: https://github.com/boostorg/geometry/pull/259#discussion_r26216587  

Unfortunately, it gives the same result as the original. Btw, I'm compiling using MinGW-w64, it's also shipped with QtCreator for Windows, in case you'd like to play with it.

---

## Comment 3

> Username: barendgehrels  
> Created_at: 2015-03-11 17:06:48 UTC  
> Url: https://github.com/boostorg/geometry/pull/259#issuecomment-78307934  

Is it about overflow?  
Anyway, I've no objection to this change, I'm OK with merging

---

## Comment 4

> Username: awulkiew  
> Created_at: 2015-03-11 17:08:56 UTC  
> Url: https://github.com/boostorg/geometry/pull/259#issuecomment-78308375  

I checked the dissassembly of this simple program:  
  
```  
double ux = 1;  
double uy = 0.10000000000000003;  
double vx = 7;  
double vy = 0.69999999999999996;  
  
double c1 = ux * vy;  
double c2 = uy * vx;  
double t0 = c1 - c2;  
double t1 = ux * vy - uy * vx;  
```  
  
In MinGW-w64 4.9.1 for `c1` and `c2` the `u_` and `v_` variables are loaded, multiplied and stored. Then `c1` and `c2` are loaded subtracted with **fsubl** instruction and stored. However in the case of t1 after loading and multiplying the values are subtracted using **fsubrp** instruction.  
  
On the other hand in MSVC 10 **fsub** and **fsubp** are used respectively.  
  
According to the docs of **FSUBR/FSUBRP/FISUBR--Reverse Subtract**: _These instructions perform the reverse operations of the FSUB, FSUBP, and FISUB instructions. They are provided to support more efficient coding_.  
  
Unfortunately, if O2 flag is passed into the MinGW compiler it optimizes all of the variables out and generates the result calculated by **fsubr** not taking into account the temporaries. MSVC with optimization enabled still generates the same result.  
  
So this PR doesn't solve the issue. I'm going to close it and just add those test cases in get_turns test behind macro check for further investigation.

---
