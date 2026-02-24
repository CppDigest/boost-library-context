# #686 - Issue on AIX with -O2 dealing with acos(-1) [Open]

> Username: trex58  
> Created at: 2020-03-25 10:54:40 UTC  
> Updated at: 2020-04-09 14:37:43 UTC  
> Url: https://github.com/boostorg/geometry/issues/686  

Hi,  
On AIX, when compiling Boost 1.69.0 with GCC 8.4 or 9.3, with **-O0**, the test:  
```  
../bin.v2/libs/geometry/test/algorithms/algorithms_densify.test/gcc-8.3.0/debug/visibility-hidden/algorithms_densify.run…  
```  
is 100% OK.  
However, with **-O2**, the test:  
```  
test_linear<ls_t>("LINESTRING(1 1, -179 -1)");  
```  
from:  
```  
../libs/geometry/test/algorithms/densify.cpp  
```  
fails with:  
```  
../libs/geometry/test/algorithms/densify.cpp(129): error: in "test_main_caller( argc, argv )": check g_count < o_count has failed  
  
geometry/test/algorithms/densify.cpp:129:  
        BOOST_CHECK(g_count < o_count);  
```  
  
Instead of seeing: `2 < 5`, I have: `2 < 2` , which is wrong.  
  
After (long) investigation, it appears that the root cause belongs to:  
  
`include/boost/geometry/arithmetic/dot_product.hpp : 39-40`  
  
```  
return get<Dimension>(p1) * get<Dimension>(p2)  
            + dot_product_maker<P1, P2, Dimension+1, DimensionCount>::apply(p1, p2);  
```  
which generates an issue in `../boost/geometry/strategies/spherical/densify.hpp`:  
  
```  
+89          calc_t const dot01 = geometry::dot_product(xyz0, xyz1);  
```  
  
However, that was version 1.69. Looking at current code, I see that it has been deeply modified...  
  
```  
calc_t angle01;  
formula::interpolate_point_spherical<calc_t> formula;  
formula.compute_angle(p0, p1, angle01);  
```  
  
Anyway, looking at the new code (lines 30-33):  
`include/boost/geometry/formulas/interpolate_point_spherical.hpp`  
  
```  
m_xyz0 = formula::sph_to_cart3d<point3d_t>(p0);  
m_xyz1 = formula::sph_to_cart3d<point3d_t>(p1);  
CalculationType const dot01 = geometry::dot_product(m_xyz0, m_xyz1);  
angle01 = acos(dot01);  
```  
  
I think that the issue is still there in 1.72 (and 1.73).  
  
The issue deals with the fact that this code is expecting dot01 to be **EXACTLY -1** .  
However, when compiling on AIX with -O2 (GCC 8 or 9 : the same), in 32bit or 64bit, I get:  
  
```  
dot01 =         f31            **-1.0000000000000002**      (raw 0xbff0000000000001)  
```  
  
and thus the `acos()` routine returns `NaNQ` since -1,....2 does not belong to [-1,+1], breaking the test.  
  
This is due to some optimized Power assembler instruction being used:  
```  
fmadd   f31,f31,f30,f1  
```  
instead of `fmul` and then `fadd`, leading to ONE extra bit in the result.  
  
I will investigate why this fmadd operation is not perfectly equivalent to `fmul`+`fadd`.  
  
Anyway, I think that this Boost code expecting to get EXACTLY -1 may be too strong a requirement.  
I'm new with Boost. Are there some Boost tools for mitigating such very small discrepancies ?  
Thx

---

## Comment 1

> Username: awulkiew  
> Created at: 2020-03-25 11:58:47 UTC  
> Url: https://github.com/boostorg/geometry/issues/686#issuecomment-603800171  

Hi, and thanks for this detailed report!  
  
I haven't looked into it but assuming your findings are the cause then the simplest thing is to trim `dot01` to [-1, 1] range before passing it into `acos()`. Numerical imperfections like that are quite common. We have covered many of them in various places but it seems not in this one.  
  
Sample fix:  
```  
using math::detail::bounded;  
//...  
CalculationType const c1 = 1;  
angle01 = acos(bounded(dot01, -c1, c1));  
```

---

## Comment 2

> Username: trex58  
> Created at: 2020-03-25 12:47:51 UTC  
> Url: https://github.com/boostorg/geometry/issues/686#issuecomment-603819689  

Hi,  
Thanks for the fix!  
I've tested it in my environment, and that works fine!  
  
I have seen another place where I'm sure the -O2 fmadd instructions generates an issue. And I have plenty of NaNQ issues appearing with -O2 . So, when possible, I'll use this kind of patch. However, that requires to find the exact place where the issue appears ; not always easy.  
  
On your side, do you plan to apply your fix to Boost:geometry current code?

---

## Comment 3

> Username: awulkiew  
> Created at: 2020-03-25 14:25:28 UTC  
> Url: https://github.com/boostorg/geometry/issues/686#issuecomment-603868935  

Yes, I will fix it on our side.  
If you find other places like that please let us know.

---

## Comment 4

> Username: trex58  
> Created at: 2020-03-25 14:45:58 UTC  
> Updated at: 2020-03-25 14:55:29 UTC  
> Url: https://github.com/boostorg/geometry/issues/686#issuecomment-603880946  

Yes. Sure. I'm now looking at issues without -O2 still with the "geometry" module .  
I'll warn you if I encounter other issues like this one, or others.  
  
I already know that, for the "math" module, which executes 1,574,437 asserts within Boost v1.69.0, I see about 16,000 errors on AIX... with a few root causes I am now studying and mainly dealing with bugs within GCC that we have never seen before building Boost.  
  
Moreover, still about fmadd issues, I've been warned that "Recent Intel chips (AVX or AVX2) added floating point fused multiple-add instructions as well. I'm not certain why it is not seeing similar behavior, although it is affected by other compiler optimizations, instruction ordering, and the system math library behavior."  
I've had a look at our Fedora/Intel reference machine. It is an old one, without AVX:  
model name      : Intel(R) Xeon(R) CPU           X5550  @ 2.67GHz  
  grep flags /proc/cpuinfo | grep -i avx  
  NO  
So, does the Boost BuildFarm make use of such recent Intel HW with AVX FP mul-add instructions?  
That would be worth to check. And, if not, it would be worth too to try to build/test Boost on such recent Intel HW.

---

## Comment 5

> Username: trex58  
> Created at: 2020-03-25 15:03:07 UTC  
> Url: https://github.com/boostorg/geometry/issues/686#issuecomment-603891471  

More info.  
On AIX, we have a GCC option -ffp-contract=off which enables to NOT use fmadd .  
  
When using it while building test "legendre_stieltjes_test" with -O2, I can see that my issues there disappear.  
The issues I have:  
../libs/math/test/legendre_stieltjes_test.cpp(95): error:  
    in "LegendreStieltjesZeros": absolute value of ls2(zeros[0]){-2.7755575615628914e-16} exceeds 2.2204460492503131e-16  
  
The issue probably comes from here:  
./boost/math/special_functions/legendre.hpp :  
+122851 template <class T1, class T2, class T3>  
+122852 inline typename tools::promote_args<T1, T2, T3>::type  
+122853    legendre_next(unsigned l, T1 x, T2 Pl, T3 Plm1)  
+122854 {  
+122855    typedef typename tools::promote_args<T1, T2, T3>::type result_type;  
.......  
+122869 //printf .........  
.......  
+122876    **return ((2 * l + 1) * result_type(x) * result_type(Pl) - l * result_type(Plm1)) / (l + 1);**  
  
since I see:  
  0x00000001000163c4 <+156>:   **fmadd**   f2,f2,f30,f31      f2 = (f2 * f30) + f31 = (-l * Plm1) + f1 * Pl = (-l * Plm1) + (((l * 2) + 1) * x) * Pl

---

## Comment 6

> Username: trex58  
> Created at: 2020-03-25 15:14:16 UTC  
> Updated at: 2020-03-25 16:28:52 UTC  
> Url: https://github.com/boostorg/geometry/issues/686#issuecomment-603897947  

We have a recent Intel machine with AVX.  
And we can see that the issues are there too.  
  
However, please note that the Intel code which is generated is generic (x86 code compatible with all machines) without using the **-march=native** option. Thus, you can easily reproduce on Fedora/Intel many of my issues by simply building/testing Boost on a Intel machine with AVX by adding this option so that AVX vfmadd132sd code is generated.  
  
Here is a simple C code which shows the first issue:  
` # cat /tmp/round.c  
 #include <stdio.h>  
 #include <stdlib.h>  
   
int main(int argc, char *argv[])  
{  
        double v1, v2, v3, ret;  
  
        v1 = strtod(argv[1], NULL);  
        v2 = strtod(argv[2], NULL);  
        v3 = strtod(argv[3], NULL);  
  
        printf("%.17f %.17f %.17f\n", v1, v2, v3);  
        ret = v1 * v2 + v3;  
        printf("%.17f\n", ret);  
  
        return 0;  
 }  
`  
  
On the machine with AVX:  
 cat /proc/cpuinfo | grep avx  
flags           :  ............ avx ........ avx2  ..........  
  
$ gcc -O2 round.c -o round  
$ ./round 0.99969541350954794 -0.99969541350954794 -0.00060908020797410  
0.99969541350954794 -0.99969541350954794 -0.00060908020797410  
-1.00000000000000000  
  
$ gcc -march=native -O2 round.c -o round  
$ ./round 0.99969541350954794 -0.99969541350954794 -0.00060908020797410  
0.99969541350954794 -0.99969541350954794 -0.00060908020797410  
-1.00000000000000022  
  
 $ gcc -O2 -ffp-contract=off -march=native round.c -o round  
 $ ./round 0.99969541350954794 -0.99969541350954794 -0.00060908020797410  
0.99969541350954794 -0.99969541350954794 -0.00060908020797410  
-1.00000000000000000  
  
The Intel x86 instruction equivalent to Power to fmadd is:  
        vfmadd132sd     %xmm8, %xmm7, %xmm6  
which replaces, when AVX is not used :  
        mulsd   %xmm8, %xmm6  
        addsd   %xmm7, %xmm1

---

## Comment 7

> Username: trex58  
> Created at: 2020-03-26 16:18:52 UTC  
> Url: https://github.com/boostorg/geometry/issues/686#issuecomment-604525397  

I have checked on a Intel hardware machine with AVX & AVX2 : the above test does fail too on such Intel HW when compiled with -O2 and -march=native .  
  
```  
    "g++"  -march=native  -fvisibility-inlines-hidden -fPIC -m64 -g -fvisibility=hidden -O2 -g -m64 -fmessage-length=0 -D_FORTIFY_SOURCE=2  
 -fstack-protector -funwind-tables -fasynchronous-unwind-tables -fno-strict-aliasing -Wno-unused-local-typedefs -Wno-deprecated-declarations   
 -DBOOST_ALL_NO_LIB=1 -DBOOST_NO_AUTO_PTR  -I".." -I"../boost/geometry/extensions/contrib/ttmath" -I"../libs/geometry/test" -c  
 -o "../bin.v2/libs/geometry/test/algorithms/algorithms_densify.test/gcc-7.5.0/debug/visibility-hidden/densify.o"   
"../libs/geometry/test/algorithms/densify.cpp"  
  
…   
  
Running 1 test case...  
../libs/geometry/test/algorithms/densify.cpp(129): error: in "test_main_caller( argc, argv )": check g_count < o_count has failed  
../libs/geometry/test/algorithms/densify.cpp(140): error: in "test_main_caller( argc, argv )": check gr_len <= max_distance || is_close has failed  
../libs/geometry/test/algorithms/densify.cpp(129): error: in "test_main_caller( argc, argv )": check g_count < o_count has failed  
../libs/geometry/test/algorithms/densify.cpp(140): error: in "test_main_caller( argc, argv )": check gr_len <= max_distance || is_close has failed  
  
 *** 4 failures are detected in the test module "Test Program"  
  
EXIT STATUS: 201  
====== END OUTPUT ======  
...failed testing.capture-output ../bin.v2/libs/geometry/test/algorithms/algorithms_densify.test/gcc-7.5.0/debug/visibility-hidden/algorithms_densify.run...  
```
