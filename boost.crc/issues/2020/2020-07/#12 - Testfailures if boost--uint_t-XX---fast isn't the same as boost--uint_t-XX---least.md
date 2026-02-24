# #12 - Testfailures if boost::uint_t<XX>::fast isn't the same as boost::uint_t<XX>::least [Open]

> Username: MBalszun  
> Created at: 2020-07-16 19:19:47 UTC  
> Updated at: 2020-08-17 11:36:15 UTC  
> Url: https://github.com/boostorg/crc/issues/12  

I don't know what the exact reason is, but if you apply the following patch to Boost.Integer, crc_test and crc_test2 fail:  
  
	--- include/boost/integer.hpp  
	+++ include/boost/integer.hpp  
	@@ -48,6 +48,13 @@ namespace boost  
		  typedef fast     type;  
	   }; // imps may specialize  
  
	+  template<>  
	+  struct int_fast_t<unsigned short>  
	+  {  
	+      typedef unsigned int fast;  
	+      typedef unsigned int type;  
	+  };  
	+  
	   namespace detail{  
  
	   //  convert category to type  
  
Best I can tell, this is the correct way to tell boost that it is faster to perform operations on ints than on shorts on a particular platform, even if you only need 16 bit.  
  
Background: I was trying to convert Boost.CRC int a standalone version without any boost dependencies using the `std::uint_fast_XX_t / _least_XX_t` type alieases instead of the `boost::uint_t<XX>::fast/least` ones.   
At least on msvc x64, `std::uint_fast16_t` is `unsigned int`, not `unsigned short`. After some investigation I could reproduce this even without touching boost crc but only changing `boost::uint_t<16>` in Boost.Integer as above.

---

## Comment 1

> Username: MBalszun  
> Created at: 2020-08-17 11:36:15 UTC  
> Url: https://github.com/boostorg/crc/issues/12#issuecomment-674829221  

Error message when executing b2 in the boost.crc test directory after `boost-1.74.0` was checked out in the superproject and `git update --init` was called.  
  
    Performing configuration checks  
      
        - default address-model    : 32-bit  
        - default architecture     : x86  
        - symlinks supported       : yes  
    ...found 62 targets...  
    ...updating 32 targets...  
    mklink-or-dir ..\..\..\boost  
    link.mklink ..\..\..\boost\config.hpp  
    symbolische Verknüpfung erstellt für ..\..\..\boost\config.hpp <<===>> ..\libs\config\include\boost\config.hpp  
    link.mklink ..\..\..\boost\crc.hpp  
    symbolische Verknüpfung erstellt für ..\..\..\boost\crc.hpp <<===>> ..\libs\crc\include\boost\crc.hpp  
    mklink-or-dir ..\..\..\boost\config  
    symbolische Verknüpfung erstellt für ..\..\..\boost\config <<===>> ..\libs\config\include\boost\config  
    link.mklink ..\..\..\boost\cstdint.hpp  
    symbolische Verknüpfung erstellt für ..\..\..\boost\cstdint.hpp <<===>> ..\libs\config\include\boost\cstdint.hpp  
    link.mklink ..\..\..\boost\array.hpp  
    symbolische Verknüpfung erstellt für ..\..\..\boost\array.hpp <<===>> ..\libs\array\include\boost\array.hpp  
    link.mklink ..\..\..\boost\limits.hpp  
    symbolische Verknüpfung erstellt für ..\..\..\boost\limits.hpp <<===>> ..\libs\config\include\boost\limits.hpp  
    mklink-or-dir ..\..\..\boost\detail  
    link.mklink ..\..\..\boost\assert.hpp  
    symbolische Verknüpfung erstellt für ..\..\..\boost\assert.hpp <<===>> ..\libs\assert\include\boost\assert.hpp  
    link.mklink ..\..\..\boost\static_assert.hpp  
    symbolische Verknüpfung erstellt für ..\..\..\boost\static_assert.hpp <<===>> ..\libs\static_assert\include\boost\static_assert.hpp  
    link.mklink ..\..\..\boost\detail\workaround.hpp  
    symbolische Verknüpfung erstellt für ..\..\..\boost\detail\workaround.hpp <<===>> ..\..\libs\config\include\boost\detail\workaround.hpp  
    link.mklink ..\..\..\boost\swap.hpp  
    symbolische Verknüpfung erstellt für ..\..\..\boost\swap.hpp <<===>> ..\libs\core\include\boost\swap.hpp  
    mklink-or-dir ..\..\..\boost\type_traits  
    symbolische Verknüpfung erstellt für ..\..\..\boost\type_traits <<===>> ..\libs\type_traits\include\boost\type_traits  
    link.mklink ..\..\..\boost\throw_exception.hpp  
    symbolische Verknüpfung erstellt für ..\..\..\boost\throw_exception.hpp <<===>> ..\libs\throw_exception\include\boost\throw_exception.hpp  
    link.mklink ..\..\..\boost\cstdlib.hpp  
    symbolische Verknüpfung erstellt für ..\..\..\boost\cstdlib.hpp <<===>> ..\libs\detail\include\boost\cstdlib.hpp  
    link.mklink ..\..\..\boost\integer.hpp  
    symbolische Verknüpfung erstellt für ..\..\..\boost\integer.hpp <<===>> ..\libs\integer\include\boost\integer.hpp  
    mklink-or-dir ..\..\..\boost\assert  
    symbolische Verknüpfung erstellt für ..\..\..\boost\assert <<===>> ..\libs\assert\include\boost\assert  
    mklink-or-dir ..\..\..\boost\exception  
    link.mklink ..\..\..\boost\integer_fwd.hpp  
    symbolische Verknüpfung erstellt für ..\..\..\boost\integer_fwd.hpp <<===>> ..\libs\integer\include\boost\integer_fwd.hpp  
    link.mklink ..\..\..\boost\integer_traits.hpp  
    symbolische Verknüpfung erstellt für ..\..\..\boost\integer_traits.hpp <<===>> ..\libs\integer\include\boost\integer_traits.hpp  
    link.mklink ..\..\..\boost\exception\exception.hpp  
    symbolische Verknüpfung erstellt für ..\..\..\boost\exception\exception.hpp <<===>> ..\..\libs\throw_exception\include\boost\exception\exception.hpp  
    mklink-or-dir ..\..\..\boost\random  
    symbolische Verknüpfung erstellt für ..\..\..\boost\random <<===>> ..\libs\random\include\boost\random  
    mklink-or-dir ..\..\..\boost\core  
    symbolische Verknüpfung erstellt für ..\..\..\boost\core <<===>> ..\libs\core\include\boost\core  
    link.mklink ..\..\..\boost\timer.hpp  
    symbolische Verknüpfung erstellt für ..\..\..\boost\timer.hpp <<===>> ..\libs\timer\include\boost\timer.hpp  
    ...patience...  
    mklink-or-dir ..\..\..\boost\integer  
    symbolische Verknüpfung erstellt für ..\..\..\boost\integer <<===>> ..\libs\integer\include\boost\integer  
    link.mklink ..\..\..\boost\version.hpp  
    symbolische Verknüpfung erstellt für ..\..\..\boost\version.hpp <<===>> ..\libs\config\include\boost\version.hpp  
    mklink-or-dir ..\..\..\boost\mpl  
    symbolische Verknüpfung erstellt für ..\..\..\boost\mpl <<===>> ..\libs\mpl\include\boost\mpl  
    mklink-or-dir ..\..\..\boost\utility  
    mklink-or-dir ..\..\..\boost\predef  
    symbolische Verknüpfung erstellt für ..\..\..\boost\predef <<===>> ..\libs\predef\include\boost\predef  
    link.mklink ..\..\..\boost\utility\enable_if.hpp  
    symbolische Verknüpfung erstellt für ..\..\..\boost\utility\enable_if.hpp <<===>> ..\..\libs\core\include\boost\utility\enable_if.hpp  
    mklink-or-dir ..\..\..\boost\preprocessor  
    symbolische Verknüpfung erstellt für ..\..\..\boost\preprocessor <<===>> ..\libs\preprocessor\include\boost\preprocessor  
    mklink-or-dir ..\..\..\boost\typeof  
    symbolische Verknüpfung erstellt für ..\..\..\boost\typeof <<===>> ..\libs\typeof\include\boost\typeof  
    link.mklink ..\..\..\boost\current_function.hpp  
    symbolische Verknüpfung erstellt für ..\..\..\boost\current_function.hpp <<===>> ..\libs\assert\include\boost\current_function.hpp  
    compile-c-c++ ..\..\..\bin.v2\libs\crc\test\crc_test.test\msvc-14.2\debug\threading-multi\crc_test.obj  
    crc_test.cpp  
    D:\repos\external\boost-repos\boost_ref\boost/timer.hpp(21): note: This header is deprecated. Use the facilities in <boost/timer/timer.hpp> instead.  
    compile-c-c++ ..\..\..\bin.v2\libs\crc\test\crc_test2.test\msvc-14.2\debug\threading-multi\crc_test2.obj  
    crc_test2.cpp  
    msvc.link ..\..\..\bin.v2\libs\crc\test\crc_test.test\msvc-14.2\debug\threading-multi\crc_test.exe  
    msvc.link ..\..\..\bin.v2\libs\crc\test\crc_test2.test\msvc-14.2\debug\threading-multi\crc_test2.exe  
    testing.capture-output ..\..\..\bin.v2\libs\crc\test\crc_test2.test\msvc-14.2\debug\threading-multi\crc_test2.run  
    ====== BEGIN OUTPUT ======  
    crc_test2.cpp(258): test 'crc_f.checksum() == CRCPolicy::standard_test_data_CRC_c::value' ('58879' == '47933') failed in function 'void __thiscall computation_comparison_test::operator ()<class `anonymous-namespace'::my_crc_test_traits<16,32773,0,1,1,0,47933>>(class `anonymous-namespace'::my_crc_test_traits<16,32773,0,1,1,0,47933>)'  
    crc_test2.cpp(262): test 'CRCPolicy::standard_test_data_CRC_c::value == func_result' ('47933' == '58879') failed in function 'void __thiscall computation_comparison_test::operator ()<class `anonymous-namespace'::my_crc_test_traits<16,32773,0,1,1,0,47933>>(class `anonymous-namespace'::my_crc_test_traits<16,32773,0,1,1,0,47933>)'  
    crc_test2.cpp(258): test 'crc_f.checksum() == CRCPolicy::standard_test_data_CRC_c::value' ('1775' == '8585') failed in function 'void __thiscall computation_comparison_test::operator ()<class `anonymous-namespace'::my_crc_test_traits<16,4129,0,1,1,0,8585>>(class `anonymous-namespace'::my_crc_test_traits<16,4129,0,1,1,0,8585>)'  
    crc_test2.cpp(262): test 'CRCPolicy::standard_test_data_CRC_c::value == func_result' ('8585' == '1775') failed in function 'void __thiscall computation_comparison_test::operator ()<class `anonymous-namespace'::my_crc_test_traits<16,4129,0,1,1,0,8585>>(class `anonymous-namespace'::my_crc_test_traits<16,4129,0,1,1,0,8585>)'  
    crc_test2.cpp(288): test 'faster_crc1.checksum() == slower_crc1.checksum()' ('11121' == '5306') failed in function 'void __thiscall accessor_and_split_run_test::operator ()<class `anonymous-namespace'::my_crc_test_traits<16,32773,0,1,1,0,47933>>(class `anonymous-namespace'::my_crc_test_traits<16,32773,0,1,1,0,47933>)'  
    crc_test2.cpp(305): test 'slower_crc2.checksum() == faster_crc2.checksum()' ('47933' == '50105') failed in function 'void __thiscall accessor_and_split_run_test::operator ()<class `anonymous-namespace'::my_crc_test_traits<16,32773,0,1,1,0,47933>>(class `anonymous-namespace'::my_crc_test_traits<16,32773,0,1,1,0,47933>)'  
    crc_test2.cpp(307): test 'faster_crc2.checksum() == CRCPolicy::standard_test_data_CRC_c::value' ('50105' == '47933') failed in function 'void __thiscall accessor_and_split_run_test::operator ()<class `anonymous-namespace'::my_crc_test_traits<16,32773,0,1,1,0,47933>>(class `anonymous-namespace'::my_crc_test_traits<16,32773,0,1,1,0,47933>)'  
    crc_test2.cpp(288): test 'faster_crc1.checksum() == slower_crc1.checksum()' ('20483' == '34866') failed in function 'void __thiscall accessor_and_split_run_test::operator ()<class `anonymous-namespace'::my_crc_test_traits<16,4129,0,1,1,0,8585>>(class `anonymous-namespace'::my_crc_test_traits<16,4129,0,1,1,0,8585>)'  
    crc_test2.cpp(305): test 'slower_crc2.checksum() == faster_crc2.checksum()' ('8585' == '62350') failed in function 'void __thiscall accessor_and_split_run_test::operator ()<class `anonymous-namespace'::my_crc_test_traits<16,4129,0,1,1,0,8585>>(class `anonymous-namespace'::my_crc_test_traits<16,4129,0,1,1,0,8585>)'  
    crc_test2.cpp(307): test 'faster_crc2.checksum() == CRCPolicy::standard_test_data_CRC_c::value' ('62350' == '8585') failed in function 'void __thiscall accessor_and_split_run_test::operator ()<class `anonymous-namespace'::my_crc_test_traits<16,4129,0,1,1,0,8585>>(class `anonymous-namespace'::my_crc_test_traits<16,4129,0,1,1,0,8585>)'  
    crc_test2.cpp(340): test 'optimal_checksum == basic_checksum' ('22171' == '25804') failed in function 'void __thiscall reset_and_single_bit_error_test::operator ()<class `anonymous-namespace'::my_crc_test_traits<16,32773,0,1,1,0,47933>>(class `anonymous-namespace'::my_crc_test_traits<16,32773,0,1,1,0,47933>)'  
    crc_test2.cpp(350): test 'optimal_tester.checksum() == basic_tester.checksum()' ('22171' == '25804') failed in function 'void __thiscall reset_and_single_bit_error_test::operator ()<class `anonymous-namespace'::my_crc_test_traits<16,32773,0,1,1,0,47933>>(class `anonymous-namespace'::my_crc_test_traits<16,32773,0,1,1,0,47933>)'  
    crc_test2.cpp(365): test 'basic_tester.checksum() == optimal_tester.checksum()' ('8724' == '15977') failed in function 'void __thiscall reset_and_single_bit_error_test::operator ()<class `anonymous-namespace'::my_crc_test_traits<16,32773,0,1,1,0,47933>>(class `anonymous-namespace'::my_crc_test_traits<16,32773,0,1,1,0,47933>)'  
    crc_test2.cpp(340): test 'optimal_checksum == basic_checksum' ('31481' == '3296') failed in function 'void __thiscall reset_and_single_bit_error_test::operator ()<class `anonymous-namespace'::my_crc_test_traits<16,4129,0,1,1,0,8585>>(class `anonymous-namespace'::my_crc_test_traits<16,4129,0,1,1,0,8585>)'  
    crc_test2.cpp(350): test 'optimal_tester.checksum() == basic_tester.checksum()' ('31481' == '3296') failed in function 'void __thiscall reset_and_single_bit_error_test::operator ()<class `anonymous-namespace'::my_crc_test_traits<16,4129,0,1,1,0,8585>>(class `anonymous-namespace'::my_crc_test_traits<16,4129,0,1,1,0,8585>)'  
    crc_test2.cpp(365): test 'basic_tester.checksum() == optimal_tester.checksum()' ('61800' == '442') failed in function 'void __thiscall reset_and_single_bit_error_test::operator ()<class `anonymous-namespace'::my_crc_test_traits<16,4129,0,1,1,0,8585>>(class `anonymous-namespace'::my_crc_test_traits<16,4129,0,1,1,0,8585>)'  
    crc_test2.cpp(600): test 'crc_c() == CRCPolicy::standard_test_data_CRC_c::value' ('58879' == '47933') failed in function 'void __thiscall function_object_test::operator ()<class `anonymous-namespace'::my_crc_test_traits<16,32773,0,1,1,0,47933>>(class `anonymous-namespace'::my_crc_test_traits<16,32773,0,1,1,0,47933>)'  
    crc_test2.cpp(600): test 'crc_c() == CRCPolicy::standard_test_data_CRC_c::value' ('1775' == '8585') failed in function 'void __thiscall function_object_test::operator ()<class `anonymous-namespace'::my_crc_test_traits<16,4129,0,1,1,0,8585>>(class `anonymous-namespace'::my_crc_test_traits<16,4129,0,1,1,0,8585>)'  
    18 errors detected.  
      
    EXIT STATUS: 18  
    ====== END OUTPUT ======  
      
      
        set status=0  
        if %status% NEQ 0 (  
            echo Skipping test execution due to testing.execute=off  
            exit 0  
        )  
         "..\..\..\bin.v2\libs\crc\test\crc_test2.test\msvc-14.2\debug\threading-multi\crc_test2.exe"   > "..\..\..\bin.v2\libs\crc\test\crc_test2.test\msvc-14.2\debug\threading-multi\crc_test2.output" 2>&1  
        set status=%ERRORLEVEL%  
        echo. >> "..\..\..\bin.v2\libs\crc\test\crc_test2.test\msvc-14.2\debug\threading-multi\crc_test2.output"  
        echo EXIT STATUS: %status% >> "..\..\..\bin.v2\libs\crc\test\crc_test2.test\msvc-14.2\debug\threading-multi\crc_test2.output"  
        if %status% EQU 0 (  
            copy "..\..\..\bin.v2\libs\crc\test\crc_test2.test\msvc-14.2\debug\threading-multi\crc_test2.output" "..\..\..\bin.v2\libs\crc\test\crc_test2.test\msvc-14.2\debug\threading-multi\crc_test2.run"  
        )  
        set verbose=0  
        if %status% NEQ 0 (  
            set verbose=1  
        )  
        if %verbose% EQU 1 (  
            echo ====== BEGIN OUTPUT ======  
            type "..\..\..\bin.v2\libs\crc\test\crc_test2.test\msvc-14.2\debug\threading-multi\crc_test2.output"  
            echo ====== END OUTPUT ======  
        )  
        exit %status%  
      
    ...failed testing.capture-output ..\..\..\bin.v2\libs\crc\test\crc_test2.test\msvc-14.2\debug\threading-multi\crc_test2.run...  
    testing.capture-output ..\..\..\bin.v2\libs\crc\test\crc_test.test\msvc-14.2\debug\threading-multi\crc_test.run  
    ====== BEGIN OUTPUT ======  
    Doing test suite for CRC-CCITT.  
            Doing computation tests.  
            Doing interrupt tests.  
            Doing error tests.  
    Doing test suite for CRC-16.  
            Doing computation tests.  
    crc_test.cpp(123): test 'fast_crc.checksum() == expected' ('58879' == '47933') failed in function 'void __cdecl crc_tester<16,32773,0,0,1,1>::compute_test(unsigned int)'  
    crc_test.cpp(125): test 'func_result == expected' ('58879' == '47933') failed in function 'void __cdecl crc_tester<16,32773,0,0,1,1>::compute_test(unsigned int)'  
            Doing interrupt tests.  
    crc_test.cpp(153): test 'fast_crc1.checksum() == slow_crc1.checksum()' ('11121' == '5306') failed in function 'void __cdecl crc_tester<16,32773,0,0,1,1>::interrupt_test(unsigned int)'  
    crc_test.cpp(168): test 'fast_crc2.checksum() == slow_crc2.checksum()' ('50105' == '47933') failed in function 'void __cdecl crc_tester<16,32773,0,0,1,1>::interrupt_test(unsigned int)'  
    crc_test.cpp(169): test 'fast_crc2.checksum() == expected' ('50105' == '47933') failed in function 'void __cdecl crc_tester<16,32773,0,0,1,1>::interrupt_test(unsigned int)'  
            Doing error tests.  
    crc_test.cpp(211): test 'fast_checksum == slow_checksum' ('22171' == '25804') failed in function 'void __cdecl crc_tester<16,32773,0,0,1,1>::error_test(void)'  
    crc_test.cpp(218): test 'fast_tester.checksum() == slow_tester.checksum()' ('22171' == '25804') failed in function 'void __cdecl crc_tester<16,32773,0,0,1,1>::error_test(void)'  
    crc_test.cpp(231): test 'fast_tester.checksum() == slow_tester.checksum()' ('15977' == '8724') failed in function 'void __cdecl crc_tester<16,32773,0,0,1,1>::error_test(void)'  
    Doing test suite for CRC-32.  
            Doing computation tests.  
            Doing interrupt tests.  
            Doing error tests.  
    Doing timing tests.  
            Boost-Basic CRC-32: 9697 runs, 3.142 s, 3086.25 run/s  
            Boost-Optimal CRC-32: 65536 runs, 0.393 s, 166758 run/s  
            Reference CRC-32: 65536 runs, 0.368 s, 178087 run/s  
            The optimal Boost version has 93.6387% the speed of the reference version.  
            The basic Boost version has 1.733% the speed of the reference version.  
            The basic Boost version has 1.85073% the speed of the optimal Boost version.  
    Doing CRC-augmented message tests.  
    Doing short-CRC (3-bit augmented) message tests.  
    Doing short-CRC (7-bit augmented) message tests.  
    Doing one-bit polynominal CRC test.  
    Doing functional object interface test.  
    crc_test.cpp(753): test 'crc_16() == std_crc_16_result' ('58879' == '47933') failed in function 'int __cdecl main(void)'  
    9 errors detected.  
      
    EXIT STATUS: 9  
    ====== END OUTPUT ======  
      
      
        set status=0  
        if %status% NEQ 0 (  
            echo Skipping test execution due to testing.execute=off  
            exit 0  
        )  
         "..\..\..\bin.v2\libs\crc\test\crc_test.test\msvc-14.2\debug\threading-multi\crc_test.exe"   > "..\..\..\bin.v2\libs\crc\test\crc_test.test\msvc-14.2\debug\threading-multi\crc_test.output" 2>&1  
        set status=%ERRORLEVEL%  
        echo. >> "..\..\..\bin.v2\libs\crc\test\crc_test.test\msvc-14.2\debug\threading-multi\crc_test.output"  
        echo EXIT STATUS: %status% >> "..\..\..\bin.v2\libs\crc\test\crc_test.test\msvc-14.2\debug\threading-multi\crc_test.output"  
        if %status% EQU 0 (  
            copy "..\..\..\bin.v2\libs\crc\test\crc_test.test\msvc-14.2\debug\threading-multi\crc_test.output" "..\..\..\bin.v2\libs\crc\test\crc_test.test\msvc-14.2\debug\threading-multi\crc_test.run"  
        )  
        set verbose=0  
        if %status% NEQ 0 (  
            set verbose=1  
        )  
        if %verbose% EQU 1 (  
            echo ====== BEGIN OUTPUT ======  
            type "..\..\..\bin.v2\libs\crc\test\crc_test.test\msvc-14.2\debug\threading-multi\crc_test.output"  
            echo ====== END OUTPUT ======  
        )  
        exit %status%  
      
    ...failed testing.capture-output ..\..\..\bin.v2\libs\crc\test\crc_test.test\msvc-14.2\debug\threading-multi\crc_test.run...  
    ...failed updating 2 targets...  
    ...skipped 2 targets...  
    ...updated 50 targets...
