# #7 Disable tests for non-C++11 compilers [Merged]

> Username: pdimov  
> Created at: 2017-09-07 23:08:14 UTC  
> Updated at: 2017-09-08 10:27:09 UTC  
> Merged at: 2017-09-08 07:04:14 UTC  
> Closed at: 2017-09-08 07:04:14 UTC  
> Url: https://github.com/boostorg/poly_collection/pull/7  

Something like that should work, as msvc-12.0 doesn't have `noexcept`. I haven't looked at the code to see what other C++11 features are needed as this one suffices, but you could add more requirements in principle if you feel like it.  
  
Your tests are brutal.

---

## Comment 1

> Username: joaquintides  
> Created_at: 2017-09-08 07:04:52 UTC  
> Updated_at: 2017-09-08 07:05:04 UTC  
> Url: https://github.com/boostorg/poly_collection/pull/7#issuecomment-328018546  

Merged.  
  
_Your tests are brutal._  
  
Pardon? Do you mean they are too taxing?

---

## Comment 2

> Username: pdimov  
> Created_at: 2017-09-08 08:04:57 UTC  
> Url: https://github.com/boostorg/poly_collection/pull/7#issuecomment-328032476  

I meant that as a compliment. :-)

---

## Comment 3

> Username: pdimov  
> Created_at: 2017-09-08 08:07:00 UTC  
> Url: https://github.com/boostorg/poly_collection/pull/7#issuecomment-328032967  

```  
...patience...  
...found 1938 targets...  
...updating 6 targets...  
gcc.compile.c++ ..\..\bin.v2\libs\poly_collection\test\test_algorithm.test\gcc-g  
nu-cxx11-6.3.0\debug\test_algorithm1.o  
/usr/lib/gcc/i686-pc-cygwin/6.3.0/../../../../i686-pc-cygwin/bin/as: ..\..\bin.v  
2\libs\poly_collection\test\test_algorithm.test\gcc-gnu-cxx11-6.3.0\debug\test_a  
lgorithm1.o: section .eh_frame$_ZN5boost15poly_collection6detail9algorithm14remo  
ve_copy_ifIJN9any_types14incrementable1EdNS4_14incrementable3EiNS_12type_erasure  
3anyINS_3mpl7vector4INS7_18copy_constructibleINS7_5_selfEEENS7_10assignableISC_R  
KSC_EENS7_13incrementableISC_EENS4_18convertible_to_intISC_EEEESC_EEENS1_13itera  
tor_implINS0_11common_impl15poly_collectionINS1_9any_modelISJ_EESaINS8_INS9_7vec  
tor2ISJ_NS7_7typeid_ISC_EEEERSC_EEEEELb1EEENS_9iterators24function_output_iterat  
orISt17reference_wrapperIN14test_utilities13compose_classINS4_6to_intEZ19test_co  
py_algorithmI18std_remove_copy_ifIJEE19poly_remove_copy_ifIJS5_dS6_iSN_EES17_KNS  
0_14any_collectionISJ_SZ_EEJRNS16_IS17_Z14test_algorithmIS1E_NS15_21jammed_auto_  
incrementES17_JS5_dS6_iSN_EEvvEUliE12_EEEEvT1_RT2_DpOT3_EUliE_EEEEES1J_LPv0EEES1  
L_RKT0_S1Y_S1L_S1M_: string table overflow at offset 10000113  
/cygdrive/c/Users/PETERD~1/AppData/Local/Temp/cc5IWgbq.s: Assembler messages:  
/cygdrive/c/Users/PETERD~1/AppData/Local/Temp/cc5IWgbq.s: Fatal error: can't clo  
se ..\..\bin.v2\libs\poly_collection\test\test_algorithm.test\gcc-gnu-cxx11-6.3.  
0\debug\test_algorithm1.o: File too big  
  
    "g++"   -O0 -fno-inline -Wall -g -march=i686 -m32 -std=c++11 -DBOOST_ALL_NO_  
LIB=1  -I"..\.." -c -o "..\..\bin.v2\libs\poly_collection\test\test_algorithm.te  
st\gcc-gnu-cxx11-6.3.0\debug\test_algorithm1.o" "test\test_algorithm1.cpp"  
  
...failed gcc.compile.c++ ..\..\bin.v2\libs\poly_collection\test\test_algorithm.  
test\gcc-gnu-cxx11-6.3.0\debug\test_algorithm1.o...  
gcc.compile.c++ ..\..\bin.v2\libs\poly_collection\test\test_algorithm.test\gcc-g  
nu-cxx11-6.3.0\debug\test_algorithm2.o  
/usr/lib/gcc/i686-pc-cygwin/6.3.0/../../../../i686-pc-cygwin/bin/as: ..\..\bin.v  
2\libs\poly_collection\test\test_algorithm.test\gcc-gnu-cxx11-6.3.0\debug\test_a  
lgorithm2.o: section .eh_frame$_ZN5boost15poly_collection6detail18tail_closure_c  
lassINS1_17cast_return_classINS1_19local_iterator_implINS0_11common_impl15poly_c  
ollectionINS1_10base_modelIN10base_types4baseEEESaIS9_EEENS1_15stride_iteratorIK  
S9_EEEENS1_9algorithm11std_find_ifEEESt5tupleIJRNS1_18head_closure_classINS1_24r  
estitute_iterator_classINS2_INS1_18deref_1st_to_classIN14test_utilities17compose  
_all_classINS8_6to_intESt8equal_toIiEEEEESK_IJEEEEJNS8_8derived1ENS8_8derived2EN  
S8_8derived3ENS8_8derived4ENS8_8derived5EEEESK_IJRKSt9type_infoRSG_EEEEEEE4callI  
JNS4_ISC_PKSX_EES1F_EJLj0EEEEDTclcl7declvalISJ_EEspcl7forwardIT_Efp0_Espcl3getIX  
T0_EEcl7declvalIS1A_EEEEENS1_16integer_sequenceIjJXspT0_EEEEDpOS1G_: string tabl  
e overflow at offset 10000021  
/cygdrive/c/Users/PETERD~1/AppData/Local/Temp/ccSC8LJj.s: Assembler messages:  
/cygdrive/c/Users/PETERD~1/AppData/Local/Temp/ccSC8LJj.s: Fatal error: can't clo  
se ..\..\bin.v2\libs\poly_collection\test\test_algorithm.test\gcc-gnu-cxx11-6.3.  
0\debug\test_algorithm2.o: File too big  
  
    "g++"   -O0 -fno-inline -Wall -g -march=i686 -m32 -std=c++11 -DBOOST_ALL_NO_  
LIB=1  -I"..\.." -c -o "..\..\bin.v2\libs\poly_collection\test\test_algorithm.te  
st\gcc-gnu-cxx11-6.3.0\debug\test_algorithm2.o" "test\test_algorithm2.cpp"  
  
...failed gcc.compile.c++ ..\..\bin.v2\libs\poly_collection\test\test_algorithm.  
test\gcc-gnu-cxx11-6.3.0\debug\test_algorithm2.o...  
```

---

## Comment 4

> Username: joaquintides  
> Created_at: 2017-09-08 09:05:55 UTC  
> Url: https://github.com/boostorg/poly_collection/pull/7#issuecomment-328047463  

_I meant that as a compliment. :-)_  
  
Oh, well, thank you very much :-)  
  
But the tests are indeed very heavy, particulary `test_algorithms`, which I had to split into three object files. And even that does not seem enough for the scenario you're showing.  
  
A cursory look on Internet shows that one of `-Og`, `-O1` ir `-O2` should help reduce the object file. Any possibility you could test this locally before adding it to the Jamfile?

---

## Comment 5

> Username: pdimov  
> Created_at: 2017-09-08 09:24:19 UTC  
> Url: https://github.com/boostorg/poly_collection/pull/7#issuecomment-328052183  

`cxxflags=-O1`, `cxxflags=-O2` don't work by themselves, probably because `inlining` is `off` and passes `-fno-inline`. `inlining=on optimization=space` seems to work (slowly).

---

## Comment 6

> Username: pdimov  
> Created_at: 2017-09-08 09:32:05 UTC  
> Url: https://github.com/boostorg/poly_collection/pull/7#issuecomment-328054311  

`b2 inlining=on optimization=space` confirmed to work for both g++ and clang under Cygwin.

---

## Comment 7

> Username: joaquintides  
> Created_at: 2017-09-08 09:50:31 UTC  
> Url: https://github.com/boostorg/poly_collection/pull/7#issuecomment-328059166  

This is great. I have to admit I know Greek better than Jamfile syntax, would you care to tell me if this is how the thing should be written?  
  
    [ run test_algorithm.cpp      test_algorithm1.cpp  
          test_algorithm2.cpp     test_algorithm3.cpp  
          test_algorithm_main.cpp  
        :  
        :                              
        : <toolset>msvc:<cxxflags>/bigobj  
          <toolset>gcc:<inlining>on  
          <toolset>gcc:<optimization>space  
          <toolset>clang:<inlining>on  
          <toolset>clang:<optimization>space                 ]  
    [ run test_construction.cpp   test_construction_main.cpp  
        :  
        :                              
        : <toolset>msvc:<cxxflags>/bigobj  
          <toolset>gcc:<inlining>on  
          <toolset>gcc:<optimization>space  
          <toolset>clang:<inlining>on  
          <toolset>clang:<optimization>space                 ]  
  
In particular, I'm curious about whether the two options can be specifed in a single `<toolset>` line.

---

## Comment 8

> Username: pdimov  
> Created_at: 2017-09-08 10:19:14 UTC  
> Url: https://github.com/boostorg/poly_collection/pull/7#issuecomment-328066426  

This is the correct way to write it, as far as I know, not being a bjam expert myself. I don't think that the two options can be combined.  
  
I tested these changes locally, they work for me.

---

## Comment 9

> Username: joaquintides  
> Created_at: 2017-09-08 10:27:09 UTC  
> Url: https://github.com/boostorg/poly_collection/pull/7#issuecomment-328067970  

Committed and pushed. Thanks for your help!  
Working on parallel on GCC 4.8 support, but it's beginning to look like a lost cause.

---
