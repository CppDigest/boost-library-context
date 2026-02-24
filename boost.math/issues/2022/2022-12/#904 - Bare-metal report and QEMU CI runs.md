# #904 - Bare-metal report and QEMU CI runs [Closed]

> Username: ckormanyos  
> Created at: 2022-12-27 09:54:24 UTC  
> Updated at: 2023-01-30 20:33:55 UTC  
> Closed at: 2023-01-30 20:33:55 UTC  
> Url: https://github.com/boostorg/math/issues/904  

This is more of a report/question than an issue.  
  
I finally put some of Math/Multiprecision on a bare-metal micro. I did this in real-time and also made a little repo with CI runs on QEMU. See also my repo [boost_math_test_qemu](https://github.com/ckormanyos/boost_math_test_qemu).  
  
This is an AWESOME!!! result so far on 1.81. All the modest things that I tested with my little tests on the metal - both in real-time as well as on QEMU - run perfectly. They also run out-of-the-box on 1.81 standalone with no modifications needed for the metal.  
  
We had never achieved this until 1.81.  
  
- Code size is a bit of an issue that I'd like to discuss.  
- QEMU runs in CI require an embedded compiler, Python, GDB and QEMU placing yet additional requirements on CI.  
  
Anyway I'd like to discuss this year:  
  
- Could we reduce code footprint even further?  
- Any interest in getting some rudimentary QEMU CI runs?  
  
Thoughts?  
  
Cc: @jzmaddock and @mborland and @NAThompson

---

## Comment 1

> Username: jzmaddock  
> Created at: 2022-12-27 12:18:05 UTC  
> Url: https://github.com/boostorg/math/issues/904#issuecomment-1365862759  

> Could we reduce code footprint even further?  
  
Possibly, it depends what the issues are, but we should certainly look into that.  I'm not surprised that the multiprecision calculations have a big footprint though.  
  
> Any interest in getting some rudimentary QEMU CI runs?  
  
Possibly, depends a bit how much CI time they soak up?  
  
Just out of curiosity, are there real world use cases for this beyond "wow this is cool?", I only ask because I know next to nothing about embedded bare metal software!

---

## Comment 2

> Username: mborland  
> Created at: 2022-12-27 13:36:02 UTC  
> Url: https://github.com/boostorg/math/issues/904#issuecomment-1365906849  

`Any interest in getting some rudimentary QEMU CI runs?`  
  
I attempted to add non-x86 platforms here: https://github.com/boostorg/math/pull/868. I need to parse through it more, and make it actually function.

---

## Comment 3

> Username: ckormanyos  
> Created at: 2022-12-27 16:48:38 UTC  
> Updated at: 2022-12-27 16:51:10 UTC  
> Url: https://github.com/boostorg/math/issues/904#issuecomment-1366039213  

>> Could we reduce code footprint even further?  
  
> Possibly, it depends what the issues are, but we should certainly look into that.  
  
At the moment, there is definitely a reduction in I/O streaming and exceptions compared to what we were seeing two years ago.  
  
Even though I compile with `-fno-rtti`, I detect some things looking like `throw` and/or stream-unwinding in the object file(s). I am not, however, sure if these come from Boost.Math itself or from the STD-library's `<string>` header. I did some investigating today and was not able to entirely solve the mystery. It's really not bad though because ARM's compiler has nono-newlib at its availability so that keeps things small.  
  
I would, however, find out who or what pulls in file-I/O handling in the object code.  
  
In [this log](https://github.com/ckormanyos/boost_math_test_qemu/actions/runs/3787206033/jobs/6438790569), I find such functions:  
  
```  
emu_env/gcc-arm-none-eabi-10.3-2021.10/bin/arm-none-eabi-g++ -v  
  
Using built-in specs.  
COLLECT_GCC=emu_env/gcc-arm-none-eabi-10.3-2021.10/bin/arm-none-eabi-g++  
COLLECT_LTO_WRAPPER=/home/runner/work/boost_math_test_qemu/boost_math_test_qemu/emu_env/gcc-arm-none-eabi-10.3-2021.10/bin/../lib/gcc/arm-none-eabi/10.3.1/lto-wrapper  
Target: arm-none-eabi  
Configured with: /mnt/workspace/workspace/GCC-10-pipeline/jenkins-GCC-10-pipeline-338_20211018_1634516203/src/gcc/configure --target=arm-none-eabi --prefix=/mnt/workspace/workspace/GCC-10-pipeline/jenkins-GCC-10-pipeline-338_20211018_1634516203/install-native --libexecdir=/mnt/workspace/workspace/GCC-10-pipeline/jenkins-GCC-10-pipeline-338_20211018_1634516203/install-native/lib --infodir=/mnt/workspace/workspace/GCC-10-pipeline/jenkins-GCC-10-pipeline-338_20211018_1634516203/install-native/share/doc/gcc-arm-none-eabi/info --mandir=/mnt/workspace/workspace/GCC-10-pipeline/jenkins-GCC-10-pipeline-338_20211018_1634516203/install-native/share/doc/gcc-arm-none-eabi/man --htmldir=/mnt/workspace/workspace/GCC-10-pipeline/jenkins-GCC-10-pipeline-338_20211018_1634516203/install-native/share/doc/gcc-arm-none-eabi/html --pdfdir=/mnt/workspace/workspace/GCC-10-pipeline/jenkins-GCC-10-pipeline-338_20211018_1634516203/install-native/share/doc/gcc-arm-none-eabi/pdf --enable-languages=c,c++ --enable-plugins --disable-decimal-float --disable-libffi --disable-libgomp --disable-libmudflap --disable-libquadmath --disable-libssp --disable-libstdcxx-pch --disable-nls --disable-shared --disable-threads --disable-tls --with-gnu-as --with-gnu-ld --with-newlib --with-headers=yes --with-python-dir=share/gcc-arm-none-eabi --with-sysroot=/mnt/workspace/workspace/GCC-10-pipeline/jenkins-GCC-10-pipeline-338_20211018_1634516203/install-native/arm-none-eabi --build=x86_64-linux-gnu --host=x86_64-linux-gnu --with-gmp=/mnt/workspace/workspace/GCC-10-pipeline/jenkins-GCC-10-pipeline-338_20211018_1634516203/build-native/host-libs/usr --with-mpfr=/mnt/workspace/workspace/GCC-10-pipeline/jenkins-GCC-10-pipeline-338_20211018_1634516203/build-native/host-libs/usr --with-mpc=/mnt/workspace/workspace/GCC-10-pipeline/jenkins-GCC-10-pipeline-338_20211018_1634516203/build-native/host-libs/usr --with-isl=/mnt/workspace/workspace/GCC-10-pipeline/jenkins-GCC-10-pipeline-338_20211018_1634516203/build-native/host-libs/usr --with-libelf=/mnt/workspace/workspace/GCC-10-pipeline/jenkins-GCC-10-pipeline-338_20211018_1634516203/build-native/host-libs/usr --with-host-libstdcxx='-static-libgcc -Wl,-Bstatic,-lstdc++,-Bdynamic -lm' --with-pkgversion='GNU Arm Embedded Toolchain 10.3-2021.10' --with-multilib-list=rmprofile,aprofile  
Thread model: single  
Supported LTO compression algorithms: zlib  
gcc version 10.3.1 20210824 (release) (GNU Arm Embedded Toolchain 10.3-2021.10)   
Compile project ./bin/boost_math_test_qemu.elf  
  
Memory region         Used Size  Region Size  %age Used  
             ROM:       54756 B       512 KB     10.44%  
             RAM:         188 B       112 KB      0.16%  
Create project objects and names  
  
Verify project objects and names  
  
-rwxr-xr-x 1 runner docker 458256 Dec 27 11:49 ./bin/boost_math_test_qemu.elf  
-rw-r--r-- 1 runner docker 154062 Dec 27 11:49 ./bin/boost_math_test_qemu.hex  
-rw-r--r-- 1 runner docker 274436 Dec 27 11:49 ./bin/boost_math_test_qemu.map  
-rw-r--r-- 1 runner docker  27596 Dec 27 11:49 ./bin/boost_math_test_qemu_cppfilt.txt  
  
Print all symbols in absolute ELF file  
08000000 00000200 D __isr_vector  
08000000 A _rom_begin  
08000200 T _ctors_begin  
08000204 T __aeabi_drsub  
08000204 T _ctors_end  
0800020c 0000027a T __aeabi_dsub  
0800020c 0000027a T __subdf3  
08000210 00000276 T __adddf3  
08000210 00000276 T __aeabi_dadd  
08000488 0000001e T __aeabi_ui2d  
08000488 0000001e T __floatunsidf  
080004a8 00000022 T __aeabi_i2d  
080004a8 00000022 T __floatsidf  
080004cc 00000042 T __aeabi_f2d  
080004cc 00000042 T __extendsfdf2  
08000510 0000006a T __aeabi_ul2d  
08000510 0000006a T __floatundidf  
08000520 0000005a T __aeabi_l2d  
08000520 0000005a T __floatdidf  
0800057c 00000254 T __aeabi_dmul  
0800057c 00000254 T __muldf3  
080007d0 000001d0 T __aeabi_ddiv  
080007d0 000001d0 T __divdf3  
080009a0 0000008a T __gedf2  
080009a0 0000008a T __gtdf2  
080009a8 00000082 T __ledf2  
080009a8 00000082 T __ltdf2  
080009b0 0000007a T __cmpdf2  
080009b0 0000007a T __eqdf2  
080009b0 0000007a T __nedf2  
08000a2c 00000020 T __aeabi_cdrcmple  
08000a3c 00000010 T __aeabi_cdcmpeq  
08000a3c 00000010 T __aeabi_cdcmple  
08000a4c 00000012 T __aeabi_dcmpeq  
08000a60 00000012 T __aeabi_dcmplt  
08000a74 00000012 T __aeabi_dcmple  
08000a88 00000012 T __aeabi_dcmpge  
08000a9c 00000012 T __aeabi_dcmpgt  
08000ab0 0000002c T __aeabi_dcmpun  
08000ab0 0000002c T __unorddf2  
08000adc 0000004e T __aeabi_d2iz  
08000adc 0000004e T __fixdfsi  
08000b2c 0000016c T __aeabi_frsub  
08000b34 00000164 T __aeabi_fsub  
08000b34 00000164 T __subsf3  
08000b38 00000160 T __addsf3  
08000b38 00000160 T __aeabi_fadd  
08000c98 00000024 T __aeabi_ui2f  
08000c98 00000024 T __floatunsisf  
08000ca0 0000001c T __aeabi_i2f  
08000ca0 0000001c T __floatsisf  
08000cbc 0000008c T __aeabi_ul2f  
08000cbc 0000008c T __floatundisf  
08000ccc 0000007c T __aeabi_l2f  
08000ccc 0000007c T __floatdisf  
08000d48 00000010 T strlen  
08000d60 T memchr  
08000e00 000004fc t long double boost::math::tools::detail::evaluate_rational_c_imp<long double, unsigned long, long double>(long double const*, unsigned long const*, long double const&, std::integral_constant<int, 13> const*) [clone .isra.0]  
080012fc 00000268 t long double boost::math::tools::detail::evaluate_rational_c_imp<long double, long double, long double>(long double const*, long double const*, long double const&, std::integral_constant<int, 8> const*) [clone .isra.0]  
08001564 0000022c t long double boost::math::tools::detail::evaluate_rational_c_imp<long double, long double, long double>(long double const*, long double const*, long double const&, std::integral_constant<int, 7> const*) [clone .isra.0]  
08001790 00000130 t long double boost::math::detail::cos_pi_imp<long double, boost::math::policies::policy<boost::math::policies::overflow_error<(boost::math::policies::error_policy_type)2>, boost::math::policies::promote_double<false>, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy> >(long double, boost::math::policies::policy<boost::math::policies::overflow_error<(boost::math::policies::error_policy_type)2>, boost::math::policies::promote_double<false>, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy> const&) [clone .isra.0]  
080018c0 00000164 t int boost::math::detail::CF1_jy<long double, boost::math::policies::policy<boost::math::policies::promote_double<false>, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy> >(long double, long double, long double*, int*, boost::math::policies::policy<boost::math::policies::promote_double<false>, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy> const&) [clone .isra.0]  
08001a28 00000230 t long double boost::math::detail::expm1_imp<long double, boost::math::policies::policy<boost::math::policies::promote_double<false>, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy> >(long double, std::integral_constant<int, 53> const&, boost::math::policies::policy<boost::math::policies::promote_double<false>, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy> const&) [clone .isra.0]  
08001c58 000006dc t long double boost::math::detail::lgamma_small_imp<long double, boost::math::policies::policy<boost::math::policies::promote_double<false>, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy>, boost::math::lanczos::lanczos13m53>(long double, long double, long double, std::integral_constant<int, 64> const&, boost::math::policies::policy<boost::math::policies::promote_double<false>, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy> const&, boost::math::lanczos::lanczos13m53 const&) [clone .isra.0]  
08002338 00000214 t long double boost::math::detail::sin_pi_imp<long double, boost::math::policies::policy<boost::math::policies::overflow_error<(boost::math::policies::error_policy_type)2>, boost::math::policies::promote_double<false>, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy> >(long double, boost::math::policies::policy<boost::math::policies::overflow_error<(boost::math::policies::error_policy_type)2>, boost::math::policies::promote_double<false>, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy> const&) [clone .isra.0]  
0800254c 00000028 W long double boost::math::unchecked_factorial<long double>(unsigned int)  
08002578 00000758 W long double boost::math::detail::bessel_j0<long double>(long double)  
08002cd0 00000274 W long double boost::math::detail::bessel_j1<long double>(long double)  
08002f48 00000214 W long double boost::math::detail::asymptotic_bessel_phase_mx<long double>(long double, long double)  
08003160 00000514 t long double boost::math::detail::asymptotic_bessel_j_large_x_2<long double, boost::math::policies::policy<boost::math::policies::promote_double<false>, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy> >(long double, long double, boost::math::policies::policy<boost::math::policies::promote_double<false>, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy> const&) [clone .isra.0]  
08003678 000000e4 W long double boost::math::detail::sinpx<long double>(long double)  
08003760 00000430 t long double boost::math::detail::gamma_imp<long double, boost::math::policies::policy<boost::math::policies::promote_double<false>, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy>, boost::math::lanczos::lanczos13m53>(long double, boost::math::policies::policy<boost::math::policies::promote_double<false>, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy> const&, boost::math::lanczos::lanczos13m53 const&) [clone .isra.0]  
08003b90 000002ac t long double boost::math::detail::lgamma_imp<long double, boost::math::policies::policy<boost::math::policies::promote_double<false>, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy>, boost::math::lanczos::lanczos13m53>(long double, boost::math::policies::policy<boost::math::policies::promote_double<false>, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy> const&, boost::math::lanczos::lanczos13m53 const&, int*) [clone .isra.0]  
08003e3c 00000034 t boost::math::tools::promote_args<long double, float, float, float, float, float>::type boost::math::lgamma<long double, boost::math::policies::policy<boost::math::policies::promote_double<false>, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy> >(long double, int*, boost::math::policies::policy<boost::math::policies::promote_double<false>, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy> const&) [clone .isra.0]  
08003e70 000001f8 t long double boost::math::detail::bessel_j_small_z_series<long double, boost::math::policies::policy<boost::math::policies::promote_double<false>, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy> >(long double, long double, boost::math::policies::policy<boost::math::policies::promote_double<false>, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy> const&) [clone .isra.0]  
08004068 000003cc t long double boost::math::detail::tgammap1m1_imp<long double, boost::math::policies::policy<boost::math::policies::promote_double<false>, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy>, boost::math::lanczos::lanczos13m53>(long double, boost::math::policies::policy<boost::math::policies::promote_double<false>, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy> const&, boost::math::lanczos::lanczos13m53 const&) [clone .isra.0]  
08004438 00002a90 t long double boost::math::detail::cyl_bessel_j_imp<long double, boost::math::policies::policy<boost::math::policies::promote_double<false>, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy> >(long double, long double, std::integral_constant<int, 0> const&, boost::math::policies::policy<boost::math::policies::promote_double<false>, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy> const&) [clone .isra.0]  
08006ec8 00000544 T boost::math::test::qemu::run_cyl_bessel_j()  
0800740c 0000002c T qemu_run_standalone  
08007438 00000048 T main  
08007480 00000090 t _GLOBAL__sub_I__ZN5boost4math4test4qemu16run_cyl_bessel_jEv  
08007510 00000002 T __vector_unused_irq  
08007514 00000002 T __nmi_handler  
08007518 00000002 T __hard_fault_handler  
0800751c 00000002 T __mem_manage_handler  
08007520 00000002 T __bus_fault_handler  
08007524 00000002 T __usage_fault_handler  
08007528 00000002 T __svc_handler  
0800752c 00000002 T __debug_mon_handler  
08007530 00000002 T __pend_sv_handler  
08007534 00000002 T __sys_tick_handler  
08007538 00000054 T __my_startup  
08007590 00000004 T ceill  
08007594 00000004 T cosl  
08007598 00000004 T coshl  
0800759c 00000004 T expl  
080075a0 00000004 T floorl  
080075a4 00000004 T fmodl  
080075a8 00000004 T sqrtl  
080075ac 00000004 T logl  
080075b0 00000004 T powl  
080075b4 00000004 T sinl  
080075b8 00000004 T sinhl  
080075bc 00000006 T std::__throw_logic_error(char const*)  
080075c2 00000036 T std::__throw_out_of_range_fmt(char const*, ...)  
080075f8 0000004c T __gnu_cxx::__throw_insufficient_space(char const*, char const*)  
08007644 00000058 T __gnu_cxx::__concat_size_t(char*, unsigned int, unsigned int)  
0800769c 00000082 T __gnu_cxx::__snprintf_lite(char*, unsigned int, char const*, std::__va_list)  
08007720 000000f8 T ceil  
08007818 000000a0 T cos  
080078b8 00000006 T fabs  
080078c0 000000f8 T floor  
080079b8 000000f4 T scalbn  
08007aac 000000b0 T sin  
08007b60 00000054 T cosh  
08007bb8 00000074 T exp  
08007c2c 00000058 T fmod  
08007c84 00000068 T log  
08007cec 000000dc T pow  
08007dc8 00000050 T sinh  
08007e18 0000004e T sqrt  
08007e68 00000148 T __ieee754_cosh  
08007fb0 000003a6 T __ieee754_exp  
08008358 00000294 T __ieee754_fmod  
080085f0 00000400 T __ieee754_log  
080089f0 00000ad8 T __ieee754_pow  
080094c8 00000454 T __ieee754_rem_pio2  
08009920 00000190 T __ieee754_sinh  
08009ab0 0000018c T __ieee754_sqrt  
08009c40 00000260 T __kernel_cos  
08009ea0 00000850 T __kernel_rem_pio2  
0800a6f0 00000184 T __kernel_sin  
0800a874 00000016 t with_errno  
0800a88c 0000002e t xflow  
0800a8bc 0000000a T __math_uflow  
0800a8c8 0000000a T __math_oflow  
0800a8d8 00000490 T expm1  
0800ad68 0000000c T finite  
0800ad74 0000000c T nan  
0800ad80 0000000e T abort  
0800ad90 0000003c T __assert_func  
0800adcc 0000000c T __errno  
0800add8 00000024 T fiprintf  
0800add8 00000024 T fprintf  
0800adfc 0000001c T memcpy  
0800ae18 00000010 T memset  
0800ae28 0000002a t __sfputc_r  
0800ae52 00000024 T __sfputs_r  
0800ae78 00000260 T _vfiprintf_r  
0800ae78 00000260 T _vfprintf_r  
0800b0d8 000000da T _printf_common  
0800b1b4 0000024c T _printf_i  
0800b400 00000050 T _raise_r  
0800b450 00000010 T raise  
0800b460 00000024 T _kill_r  
0800b484 00000004 T _getpid_r  
0800b488 000000a4 T __swbuf_r  
0800b52c 000000dc T __swsetup_r  
0800b608 00000104 T __sflush_r  
0800b70c 00000078 T _fflush_r  
0800b784 00000048 t std  
0800b7cc 0000000c T _cleanup_r  
0800b7d8 0000002c T __sfmoreglue  
0800b804 0000000c T __sfp_lock_acquire  
0800b810 0000000c T __sfp_lock_release  
0800b81c 0000000c T __sinit_lock_acquire  
0800b828 0000000c T __sinit_lock_release  
0800b834 00000070 T __sinit  
0800b8a4 0000008c T __sfp  
0800b930 0000003e T _fwalk_reent  
0800b96e 00000002 T __retarget_lock_init_recursive  
0800b970 00000002 T __retarget_lock_acquire_recursive  
0800b972 00000002 T __retarget_lock_release_recursive  
0800b974 0000004a T __swhatbuf_r  
0800b9c0 00000080 T __smakebuf_r  
0800ba40 00000094 T _free_r  
0800bad4 00000040 t sbrk_aligned  
0800bb14 000000e8 T _malloc_r  
0800bbfc 00000020 T _sbrk_r  
0800bc1c 00000022 T __sread  
0800bc3e 00000038 T __swrite  
0800bc76 00000024 T __sseek  
0800bc9a 00000008 T __sclose  
0800bca4 00000024 T _write_r  
0800bcc8 00000020 T _close_r  
0800bce8 00000024 T _fstat_r  
0800bd0c 00000020 T _isatty_r  
0800bd2c 00000024 T _lseek_r  
0800bd50 0000000c T __malloc_lock  
0800bd5c 0000000c T __malloc_unlock  
0800bd68 00000024 T _read_r  
0800bd8c 00000010 T _close  
0800bd9c 00000010 T _fstat  
0800bdac 00000010 T _getpid  
0800bdbc 00000010 T _isatty  
0800bdcc 00000010 T _kill  
0800bddc 00000010 T _lseek  
0800bdec 00000010 T _read  
0800bdfc 0000001c T _sbrk  
0800be18 00000010 T _write  
0800be28 00000002 T _exit  
0800ce0c 00000034 V boost::math::lanczos::lanczos13m53::lanczos_sum_expG_scaled<long double>(long double const&)::denom  
0800ce40 00000068 V boost::math::lanczos::lanczos13m53::lanczos_sum_expG_scaled<long double>(long double const&)::num  
0800cea8 00000034 V boost::math::lanczos::lanczos13m53::lanczos_sum<long double>(long double const&)::denom  
0800cee0 00000068 V boost::math::lanczos::lanczos13m53::lanczos_sum<long double>(long double const&)::num  
0800cf48 00000038 V boost::math::detail::bessel_j1<long double>(long double)::QS  
0800cf80 00000038 V boost::math::detail::bessel_j1<long double>(long double)::PS  
0800cfb8 00000038 V boost::math::detail::bessel_j1<long double>(long double)::QC  
0800cff0 00000038 V boost::math::detail::bessel_j1<long double>(long double)::PC  
0800d028 00000040 V boost::math::detail::bessel_j1<long double>(long double)::Q2  
0800d068 00000040 V boost::math::detail::bessel_j1<long double>(long double)::P2  
0800d0a8 00000038 V boost::math::detail::bessel_j1<long double>(long double)::Q1  
0800d0e0 00000038 V boost::math::detail::bessel_j1<long double>(long double)::P1  
0800d118 00000040 V boost::math::detail::bessel_j0<long double>(long double)::Q2  
0800d158 00000040 V boost::math::detail::bessel_j0<long double>(long double)::P2  
0800d198 00000038 V boost::math::detail::bessel_j0<long double>(long double)::Q1  
0800d1d0 00000038 V boost::math::detail::bessel_j0<long double>(long double)::P1  
0800d280 00000010 t halF  
0800d290 00000010 t ln2HI  
0800d2a0 00000010 t ln2LO  
0800d2b0 00000010 t Zero  
0800d2c0 00000080 t npio2_hw  
0800d340 00000108 t two_over_pi  
0800d448 00000040 t PIo2  
0800d488 00000010 t init_jk  
0800d4d4 00000004 T _global_impure_ptr  
0800d50c 00000020 T __sf_fake_stderr  
0800d52c 00000020 T __sf_fake_stdin  
0800d54c 00000020 T __sf_fake_stdout  
0800d56c A _rom_data_begin  
08080000 A _rom_end  
20000000 00000001 d boost::math::test::qemu::run_cyl_bessel_j()::boost_math_test_qemu_result_is_ok  
20000000 D _data_begin  
20000008 00000008 D v  
20000010 00000004 D xn  
20000014 00000004 D _impure_ptr  
20000018 00000060 d impure_data  
20000078 00000004 V guard variable for boost::math::detail::expm1_initializer<long double, boost::math::policies::policy<boost::math::policies::promote_double<false>, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy>, std::integral_constant<int, 53> >::initializer  
20000078 B _bss_begin  
20000078 D _data_end  
2000007c 00000004 V guard variable for boost::math::detail::log1p_initializer<long double, boost::math::policies::policy<boost::math::policies::promote_double<false>, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy>, std::integral_constant<int, 53> >::initializer  
20000080 00000004 V guard variable for boost::math::detail::lgamma_initializer<long double, boost::math::policies::policy<boost::math::policies::promote_double<false>, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy> >::initializer  
20000084 00000004 V guard variable for boost::math::detail::bessel_j1_initializer<long double>::initializer  
20000088 00000004 V guard variable for boost::math::detail::bessel_j0_initializer<long double>::initializer  
2000008c 00000004 b boost::math::test::qemu::run_cyl_bessel_j()::boost_math_test_qemu_n_index  
20000090 00000008 B cyj  
20000098 00000004 B qemu_standalone_result  
2000009c 00000004 B __dso_handle  
200000a0 00000001 B __lock___arc4random_mutex  
200000a1 00000001 B __lock___at_quick_exit_mutex  
200000a2 00000001 B __lock___atexit_recursive_mutex  
200000a3 00000001 B __lock___dd_hash_mutex  
200000a4 00000001 B __lock___env_recursive_mutex  
200000a5 00000001 B __lock___malloc_recursive_mutex  
200000a6 00000001 B __lock___sfp_recursive_mutex  
200000a7 00000001 B __lock___sinit_recursive_mutex  
200000a8 00000001 B __lock___tz_mutex  
200000ac 00000004 B __malloc_free_list  
200000b0 00000004 B __malloc_sbrk_start  
200000b4 00000004 B errno  
200000b8 00000004 b heap_end.0  
200000bc B _bss_end  
200000bc B end  
2001c000 A _stack_begin  
2001fffc A __initial_stack_pointer  
20020000 A _stack_end  
```

---

## Comment 4

> Username: ckormanyos  
> Created at: 2022-12-27 16:56:14 UTC  
> Updated at: 2022-12-27 16:59:33 UTC  
> Url: https://github.com/boostorg/math/issues/904#issuecomment-1366043130  

> out of curiosity, are there real world use cases for this beyond "wow this is cool?"  
  
I have a subjective opinion on this. Yes.  
  
Sure we don't find a lot of microcontroller folks demanding better Laguerre polynomials and fancy root-finding, but there are quite a few areas of interest.  
  
Many developers are definitely putting more and more mathematical analyses into small embedded devices. Since these have FPUs now more and more often, the hit of special functioins is realy not that high. Fields like navigation, dynamic behavior detection of any kinds of applications using/analyzing anything wave-like such as ocean currents, etc. need those specfuns.  
  
For large integral calculations, there is high interest in (of course) cryptography (ECC implemented in native big-ints uses large unsigned integers), and rational approximations needing larger integral values often arise. Fixed-point implementations having more than just a few digits need backend large integers beyond 64-bit.  
  
So if we can get rid of streaming and file I/O that has crept in somehow, we can cut out a few tens of kilobytes of code for specfun. That yould be a good start since a few tens of kilobytes are quite a lot on the metal, even if you link with nano-specs.

---

## Comment 5

> Username: ckormanyos  
> Created at: 2023-01-30 20:33:55 UTC  
> Url: https://github.com/boostorg/math/issues/904#issuecomment-1409296126  

Everything works at the moment. Anything like optionally investingating reducing code-size-footprint can be handled optionally in future issues if interest ever arises.
