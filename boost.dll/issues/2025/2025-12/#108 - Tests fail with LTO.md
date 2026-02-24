# #108 - Tests fail with LTO [Open]

> Username: eli-schwartz  
> Created at: 2025-12-07 21:29:37 UTC  
> Updated at: 2025-12-07 21:29:37 UTC  
> Url: https://github.com/boostorg/dll/issues/108  

I tried to build with the following *FLAGS to optimize the build: `-flto=4 -Werror=odr -Werror=lto-type-mismatch -Werror=strict-aliasing`  
  
I got this error:  
```  
testing.capture-output ../../../bin.v2/libs/dll/test/library_info_test.test/gcc-14.3/gentoorelease/x86_64/pch-off/threading-multi/visibility-hidden/library_info_test.run  
  
    LD_LIBRARY_PATH="/usr/lib/ccache/bin:/usr/lib/ccache/lib:/usr/lib/ccache/lib32:/usr/lib/ccache/lib64:/var/tmp/portage/dev-libs/boost-1.89.0/work/boost_1_89_0-abi_x86_64.amd64/bin.v2/libs/atomic/build/gcc-14.3/gentoorelease/x86_64/pch-off/threading-multi/visibility-hidden:/var/tmp/portage/dev-libs/boost-1.89.0/work/boost_1_89_0-abi_x86_64.amd64/bin.v2/libs/dll/test/gcc-14.3/gentoorelease/x86_64/pch-off/threading-multi/visibility-hidden:/var/tmp/portage/dev-libs/boost-1.89.0/work/boost_1_89_0-abi_x86_64.amd64/bin.v2/libs/filesystem/build/gcc-14.3/gentoorelease/x86_64/pch-off/threading-multi/visibility-hidden:$LD_LIBRARY_PATH"  
export LD_LIBRARY_PATH  
  
     "../../../bin.v2/libs/dll/test/library_info_test.test/gcc-14.3/gentoorelease/x86_64/pch-off/threading-multi/visibility-hidden/library_info_test"  "../../../bin.v2/libs/dll/test/gcc-14.3/gentoorelease/x86_64/pch-off/threading-multi/visibility-hidden/libtest_library.so.1.89.0" > "../../../bin.v2/libs/dll/test/library_info_test.test/gcc-14.3/gentoorelease/x86_64/pch-off/threading-multi/visibility-hidden/library_info_test.output" 2>&1 < /dev/null  
    status=$?  
    echo >> "../../../bin.v2/libs/dll/test/library_info_test.test/gcc-14.3/gentoorelease/x86_64/pch-off/threading-multi/visibility-hidden/library_info_test.output"  
    echo EXIT STATUS: $status >> "../../../bin.v2/libs/dll/test/library_info_test.test/gcc-14.3/gentoorelease/x86_64/pch-off/threading-multi/visibility-hidden/library_info_test.output"  
    if test $status -eq 0 ; then  
        cp "../../../bin.v2/libs/dll/test/library_info_test.test/gcc-14.3/gentoorelease/x86_64/pch-off/threading-multi/visibility-hidden/library_info_test.output" "../../../bin.v2/libs/dll/test/library_info_test.test/gcc-14.3/gentoorelease/x86_64/pch-off/threading-multi/visibility-hidden/library_info_test.run" >/dev/null  
    fi  
    verbose=0  
    if test $status -ne 0 ; then  
        verbose=1  
    fi  
    if test $verbose -eq 1 ; then  
        echo ====== BEGIN OUTPUT ======  
        cat "../../../bin.v2/libs/dll/test/library_info_test.test/gcc-14.3/gentoorelease/x86_64/pch-off/threading-multi/visibility-hidden/library_info_test.output"  
        echo ====== END OUTPUT ======  
    fi  
    exit $status  
  
====== BEGIN OUTPUT ======  
.symtab,  .strtab,  .shstrtab,  .gnu.hash,  .dynsym,  .dynstr,  .gnu.version,  .gnu.version_r,  .rela.dyn,  .rela.plt,  .relr.dyn,  .init,  .plt.got,  .plt.sec,  .text,  .fini,  .rodata,  .note.gnu.property,  .eh_frame_hdr,  .eh_frame,  .gcc_except_table,  .init_array,  .fini_array,  .data.rel.ro,  .dynamic,  .data,  boostdll,  .bss,  .comment,  .GCC.command.line,  .debug_aranges,  .debug_info,  .debug_abbrev,  .debug_line,  .debug_str,  .debug_line_str,  .debug_loclists,  .debug_rnglists,    
  
  
_ZTIN5boost6system6detail12std_categoryE  
_ZTIN5boost16exception_detail10clone_baseE  
_ZTVN5boost6system6detail22generic_error_categoryE  
_ZTSN5boost6system6detail21system_error_categoryE  
say_hello  
do_share  
_ZTSN5boost6system6detail22generic_error_categoryE  
_ZTSSt16_Sp_counted_baseILN9__gnu_cxx12_Lock_policyE2EE  
lib_version  
_ZTVN5boost6system6detail21system_error_categoryE  
rvalue_reference_to_internal_integer  
_ZTISt16_Sp_counted_baseILN9__gnu_cxx12_Lock_policyE2EE  
_ZTSN5boost6system6detail12std_categoryE  
increment  
_ZTSN5boost16exception_detail10clone_baseE  
_ZTSN5boost10wrapexceptINS_6system12system_errorEEE  
foo_variable  
_ZTIN5boost6system6detail22generic_error_categoryE  
_ZTIN5boost9exceptionE  
_ZTSSt19_Sp_make_shared_tag  
_ZTIN5boost10wrapexceptINS_6system12system_errorEEE  
info  
_ZTVN5boost6system14error_categoryE  
_ZTSN5boost6system6detail22interop_error_categoryE  
_ZTISt11_Mutex_baseILN9__gnu_cxx12_Lock_policyE2EE  
_ZTSSt11_Mutex_baseILN9__gnu_cxx12_Lock_policyE2EE  
protected_function  
integer_g  
ref_returning_function  
_ZTIN5boost6system6detail22interop_error_categoryE  
_ZZNSt19_Sp_make_shared_tag5_S_tiEvE5__tag  
foo_bar  
_ZTIN5boost6system12system_errorE  
module_location_from_itself  
_ZTVN5boost6system6detail22interop_error_categoryE  
const_integer_g_alias  
_ZTIN5boost6system14error_categoryE  
_ZTSN5boost6system12system_errorE  
reference_to_internal_integer  
_ZTSN5boost9exceptionE  
_ZTIN5boost6system6detail21system_error_categoryE  
_ZTSN5boost6system14error_categoryE  
const_integer_g  
  
  
'boostdll' symbols:  
do_share  
foo_variable  
info  
ref_returning_function  
foo_bar  
module_location_from_itself  
const_integer_g_alias  
  
  
'empty' symbols:  
Self: ../../../bin.v2/libs/dll/test/library_info_test.test/gcc-14.3/gentoorelease/x86_64/pch-off/threading-multi/visibility-hidden/library_info_testlibrary_info_test.cpp(64): test 'std::find(sec.begin(), sec.end(), "boostdll") != sec.end()' failed in function 'int main(int, char**)'  
library_info_test.cpp(68): test 'std::find(symb.begin(), symb.end(), "create_plugin") != symb.end()' failed in function 'int main(int, char**)'  
2 errors detected.  
  
EXIT STATUS: 2  
====== END OUTPUT ======  
```  
  
Originally reported downstream: https://bugs.gentoo.org/956660  
Full build log:  [build.log](https://github.com/user-attachments/files/24019879/build.log)
