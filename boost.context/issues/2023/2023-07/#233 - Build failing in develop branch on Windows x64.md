# #233 - Build failing in develop branch on Windows x64 [Closed]

> Username: anarthal  
> Created at: 2023-07-07 15:15:58 UTC  
> Updated at: 2023-08-18 04:57:17 UTC  
> Closed at: 2023-08-18 04:57:17 UTC  
> Url: https://github.com/boostorg/context/issues/233  

Steps to reproduce:  
  
```  
git clone -b develop --depth 1 https://github.com/boostorg/boost.git .  
git submodule update --init .\libs\context .\tools\boostdep\  
python .\tools\boostdep\depinst\depinst.py context  
.\bootstrap.bat  
.\b2 --with-context  
```  
  
Caught in Boost.MySQL CI.  
  
Build log:  
```  
Performing configuration checks  
  
    - default address-model    : 64-bit [1]  
    - default architecture     : x86 [1]  
  
Building the Boost C++ Libraries.  
  
  
    - symlinks supported       : no  
    - junctions supported      : yes  
    - hardlinks supported      : yes  
    - is ml assembler          : yes [2]  
    - is ml assembler          : yes [3]  
    - is ml assembler          : no [4]  
    - is ml assembler          : no [5]  
  
[1] msvc-14.3  
[2] msvc-14.3/debug/address-model-32/link-static/threadapi-win32/threading-multi/visibility-hidden  
[3] msvc-14.3/release/address-model-32/link-static/threadapi-win32/threading-multi/visibility-hidden  
[4] msvc-14.3/debug/link-static/threadapi-win32/threading-multi/visibility-hidden  
[5] msvc-14.3/release/link-static/threadapi-win32/threading-multi/visibility-hidden  
  
Component configuration:  
  
    - container                : not building  
    - context                  : building  
    - headers                  : not building  
    - regex                    : not building  
  
...found 548 targets...  
...updating 272 targets...  
mklink-or-dir boost  
common.copy C:\Users\ruben\boost3\stage\lib\cmake\BoostDetectToolset-1.83.0.cmake  
C:\Users\ruben\boost3\tools\boost_install\BoostDetectToolset.cmake  
        1 file(s) copied.  
mklink-or-dir boost\predef  
Junction created for boost\predef <<===>> libs\predef\include\boost\predef  
mklink-or-dir boost\context  
Junction created for boost\context <<===>> libs\context\include\boost\context  
mklink-or-dir boost\config  
Junction created for boost\config <<===>> libs\config\include\boost\config  
common.copy C:\Users\ruben\boost3\stage\lib\cmake\Boost-1.83.0\BoostConfig.cmake  
C:\Users\ruben\boost3\tools\boost_install\BoostConfig.cmake  
        1 file(s) copied.  
link.hardlink boost\assert.hpp  
Hardlink created for boost\assert.hpp <<===>> libs\assert\include\boost\assert.hpp  
link.hardlink boost\config.hpp  
Hardlink created for boost\config.hpp <<===>> libs\config\include\boost\config.hpp  
...patience...  
link.hardlink boost\cstdint.hpp  
Hardlink created for boost\cstdint.hpp <<===>> libs\config\include\boost\cstdint.hpp  
link.hardlink boost\current_function.hpp  
Hardlink created for boost\current_function.hpp <<===>> libs\assert\include\boost\current_function.hpp  
mklink-or-dir boost\detail  
link.hardlink boost\limits.hpp  
Hardlink created for boost\limits.hpp <<===>> libs\config\include\boost\limits.hpp  
link.hardlink boost\version.hpp  
Hardlink created for boost\version.hpp <<===>> libs\config\include\boost\version.hpp  
boost-install.generate-cmake-config- bin.v2\libs\context\build\stage\boost_context-config.cmake  
link.hardlink boost\detail\workaround.hpp  
Hardlink created for boost\detail\workaround.hpp <<===>> libs\config\include\boost\detail\workaround.hpp  
preprocess-c-c++ bin.v2\libs\context\build\msvc-14.3\link-static\asm\make.asm  
make.S  
boost-install.generate-cmake-config-version- bin.v2\tools\boost_install\BoostConfigVersion.cmake  
common.copy C:\Users\ruben\boost3\stage\lib\cmake\boost_context-1.83.0\boost_context-config.cmake  
bin.v2\libs\context\build\stage\boost_context-config.cmake  
        1 file(s) copied.  
boost-install.generate-cmake-config-version- bin.v2\libs\context\build\stage\boost_context-config-version.cmake  
boost-install.generate-cmake-config- bin.v2\libs\headers\build\stage\boost_headers-config.cmake  
common.copy C:\Users\ruben\boost3\stage\lib\cmake\Boost-1.83.0\BoostConfigVersion.cmake  
bin.v2\tools\boost_install\BoostConfigVersion.cmake  
        1 file(s) copied.  
boost-install.generate-cmake-config-version- bin.v2\libs\headers\build\stage\boost_headers-config-version.cmake  
preprocess-c-c++ bin.v2\libs\context\build\msvc-14.3\link-static\asm\jump.asm  
jump.S  
preprocess-c-c++ bin.v2\libs\context\build\msvc-14.3\link-static\asm\ontop.asm  
ontop.S  
common.copy C:\Users\ruben\boost3\stage\lib\cmake\boost_context-1.83.0\boost_context-config-version.cmake  
bin.v2\libs\context\build\stage\boost_context-config-version.cmake  
        1 file(s) copied.  
common.copy C:\Users\ruben\boost3\stage\lib\cmake\boost_headers-1.83.0\boost_headers-config.cmake  
bin.v2\libs\headers\build\stage\boost_headers-config.cmake  
        1 file(s) copied.  
msvc.compile.asm bin.v2\libs\context\build\msvc-14.3\debug\address-model-32\link-static\threading-multi\asm\make.obj  
 Assembling: bin.v2\libs\context\build\msvc-14.3\link-static\asm\make.asm  
common.copy C:\Users\ruben\boost3\stage\lib\cmake\boost_headers-1.83.0\boost_headers-config-version.cmake  
bin.v2\libs\headers\build\stage\boost_headers-config-version.cmake  
        1 file(s) copied.  
msvc.compile.asm bin.v2\libs\context\build\msvc-14.3\debug\address-model-32\link-static\threading-multi\asm\ontop.obj  
 Assembling: bin.v2\libs\context\build\msvc-14.3\link-static\asm\ontop.asm  
msvc.compile.asm bin.v2\libs\context\build\msvc-14.3\debug\address-model-32\link-static\threading-multi\asm\jump.obj  
 Assembling: bin.v2\libs\context\build\msvc-14.3\link-static\asm\jump.asm  
msvc.compile.asm bin.v2\libs\context\build\msvc-14.3\release\address-model-32\link-static\threading-multi\asm\make.obj  
 Assembling: bin.v2\libs\context\build\msvc-14.3\link-static\asm\make.asm  
compile-c-c++ bin.v2\libs\context\build\msvc-14.3\debug\address-model-32\link-static\threading-multi\asm\tail_ontop_ppc32_sysv.obj  
tail_ontop_ppc32_sysv.cpp  
msvc.compile.asm bin.v2\libs\context\build\msvc-14.3\debug\link-static\threading-multi\asm\ontop.obj  
 Assembling: bin.v2\libs\context\build\msvc-14.3\link-static\asm\ontop.asm  
bin.v2\libs\context\build\msvc-14.3\link-static\asm\ontop.asm(14080) : error A2008:syntax error : .  
bin.v2\libs\context\build\msvc-14.3\link-static\asm\ontop.asm(14081) : error A2008:syntax error : .  
bin.v2\libs\context\build\msvc-14.3\link-static\asm\ontop.asm(14082) : error A2008:syntax error : .  
bin.v2\libs\context\build\msvc-14.3\link-static\asm\ontop.asm(14096) : error A2008:syntax error : fs  
bin.v2\libs\context\build\msvc-14.3\link-static\asm\ontop.asm(14099) : error A2008:syntax error : fs  
bin.v2\libs\context\build\msvc-14.3\link-static\asm\ontop.asm(14149) : error A2008:syntax error : fs  
bin.v2\libs\context\build\msvc-14.3\link-static\asm\ontop.asm(14152) : error A2008:syntax error : fs  
bin.v2\libs\context\build\msvc-14.3\link-static\asm\ontop.asm(14180) : error A2077:instruction does not allow NEAR indirect addressing  
  
    call "bin.v2\standalone\msvc\msvc-14.3\msvc-setup.bat"  >nul  
 ml64 -c -Zp4 -Cp -Cx -nologo -DBOOST_ALL_NO_LIB=1 -DBOOST_CONTEXT_EXPORT= -DBOOST_CONTEXT_SOURCE -D_WIN32_WINNT=0x0601 /Zi /Zd /W3  -Fo "bin.v2\libs\context\build\msvc-14.3\debug\link-static\threading-multi\asm\ontop.obj" "bin.v2\libs\context\build\msvc-14.3\link-static\asm\ontop.asm"  
  
...failed msvc.compile.asm bin.v2\libs\context\build\msvc-14.3\debug\link-static\threading-multi\asm\ontop.obj...  
msvc.compile.asm bin.v2\libs\context\build\msvc-14.3\debug\link-static\threading-multi\asm\jump.obj  
 Assembling: bin.v2\libs\context\build\msvc-14.3\link-static\asm\jump.asm  
bin.v2\libs\context\build\msvc-14.3\link-static\asm\jump.asm(14080) : error A2008:syntax error : .  
bin.v2\libs\context\build\msvc-14.3\link-static\asm\jump.asm(14081) : error A2008:syntax error : .  
bin.v2\libs\context\build\msvc-14.3\link-static\asm\jump.asm(14082) : error A2008:syntax error : .  
bin.v2\libs\context\build\msvc-14.3\link-static\asm\jump.asm(14096) : error A2008:syntax error : fs  
bin.v2\libs\context\build\msvc-14.3\link-static\asm\jump.asm(14099) : error A2008:syntax error : fs  
bin.v2\libs\context\build\msvc-14.3\link-static\asm\jump.asm(14137) : error A2008:syntax error : fs  
bin.v2\libs\context\build\msvc-14.3\link-static\asm\jump.asm(14140) : error A2008:syntax error : fs  
bin.v2\libs\context\build\msvc-14.3\link-static\asm\jump.asm(14172) : error A2077:instruction does not allow NEAR indirect addressing  
  
    call "bin.v2\standalone\msvc\msvc-14.3\msvc-setup.bat"  >nul  
 ml64 -c -Zp4 -Cp -Cx -nologo -DBOOST_ALL_NO_LIB=1 -DBOOST_CONTEXT_EXPORT= -DBOOST_CONTEXT_SOURCE -D_WIN32_WINNT=0x0601 /Zi /Zd /W3  -Fo "bin.v2\libs\context\build\msvc-14.3\debug\link-static\threading-multi\asm\jump.obj" "bin.v2\libs\context\build\msvc-14.3\link-static\asm\jump.asm"  
  
...failed msvc.compile.asm bin.v2\libs\context\build\msvc-14.3\debug\link-static\threading-multi\asm\jump.obj...  
msvc.compile.asm bin.v2\libs\context\build\msvc-14.3\debug\link-static\threading-multi\asm\make.obj  
 Assembling: bin.v2\libs\context\build\msvc-14.3\link-static\asm\make.asm  
bin.v2\libs\context\build\msvc-14.3\link-static\asm\make.asm(14080) : error A2008:syntax error : .  
bin.v2\libs\context\build\msvc-14.3\link-static\asm\make.asm(14081) : error A2008:syntax error : .  
bin.v2\libs\context\build\msvc-14.3\link-static\asm\make.asm(14082) : error A2008:syntax error : .  
bin.v2\libs\context\build\msvc-14.3\link-static\asm\make.asm(14084) : error A2008:syntax error : ,  
bin.v2\libs\context\build\msvc-14.3\link-static\asm\make.asm(14148) : error A2008:syntax error : fs  
bin.v2\libs\context\build\msvc-14.3\link-static\asm\make.asm(14151) : error A2008:syntax error : fs  
bin.v2\libs\context\build\msvc-14.3\link-static\asm\make.asm(14186) : error A2070:invalid instruction operands  
bin.v2\libs\context\build\msvc-14.3\link-static\asm\make.asm(14188) : error A2077:instruction does not allow NEAR indirect addressing  
bin.v2\libs\context\build\msvc-14.3\link-static\asm\make.asm(14195) : error A2006:undefined symbol : _exit  
  
    call "bin.v2\standalone\msvc\msvc-14.3\msvc-setup.bat"  >nul  
 ml64 -c -Zp4 -Cp -Cx -nologo -DBOOST_ALL_NO_LIB=1 -DBOOST_CONTEXT_EXPORT= -DBOOST_CONTEXT_SOURCE -D_WIN32_WINNT=0x0601 /Zi /Zd /W3  -Fo "bin.v2\libs\context\build\msvc-14.3\debug\link-static\threading-multi\asm\make.obj" "bin.v2\libs\context\build\msvc-14.3\link-static\asm\make.asm"  
  
...failed msvc.compile.asm bin.v2\libs\context\build\msvc-14.3\debug\link-static\threading-multi\asm\make.obj...  
msvc.compile.asm bin.v2\libs\context\build\msvc-14.3\release\address-model-32\link-static\threading-multi\asm\jump.obj  
 Assembling: bin.v2\libs\context\build\msvc-14.3\link-static\asm\jump.asm  
link.hardlink boost\array.hpp  
Hardlink created for boost\array.hpp <<===>> libs\array\include\boost\array.hpp  
msvc.compile.asm bin.v2\libs\context\build\msvc-14.3\release\link-static\threading-multi\asm\make.obj  
 Assembling: bin.v2\libs\context\build\msvc-14.3\link-static\asm\make.asm  
bin.v2\libs\context\build\msvc-14.3\link-static\asm\make.asm(14080) : error A2008:syntax error : .  
bin.v2\libs\context\build\msvc-14.3\link-static\asm\make.asm(14081) : error A2008:syntax error : .  
bin.v2\libs\context\build\msvc-14.3\link-static\asm\make.asm(14082) : error A2008:syntax error : .  
bin.v2\libs\context\build\msvc-14.3\link-static\asm\make.asm(14084) : error A2008:syntax error : ,  
bin.v2\libs\context\build\msvc-14.3\link-static\asm\make.asm(14148) : error A2008:syntax error : fs  
bin.v2\libs\context\build\msvc-14.3\link-static\asm\make.asm(14151) : error A2008:syntax error : fs  
bin.v2\libs\context\build\msvc-14.3\link-static\asm\make.asm(14186) : error A2070:invalid instruction operands  
bin.v2\libs\context\build\msvc-14.3\link-static\asm\make.asm(14188) : error A2077:instruction does not allow NEAR indirect addressing  
bin.v2\libs\context\build\msvc-14.3\link-static\asm\make.asm(14195) : error A2006:undefined symbol : _exit  
  
    call "bin.v2\standalone\msvc\msvc-14.3\msvc-setup.bat"  >nul  
 ml64 -c -Zp4 -Cp -Cx -nologo -DBOOST_ALL_NO_LIB=1 -DBOOST_CONTEXT_EXPORT= -DBOOST_CONTEXT_SOURCE -DBOOST_DISABLE_ASSERTS -DNDEBUG -D_WIN32_WINNT=0x0601 /W3  -Fo "bin.v2\libs\context\build\msvc-14.3\release\link-static\threading-multi\asm\make.obj" "bin.v2\libs\context\build\msvc-14.3\link-static\asm\make.asm"  
  
...failed msvc.compile.asm bin.v2\libs\context\build\msvc-14.3\release\link-static\threading-multi\asm\make.obj...  
msvc.compile.asm bin.v2\libs\context\build\msvc-14.3\release\address-model-32\link-static\threading-multi\asm\ontop.obj  
 Assembling: bin.v2\libs\context\build\msvc-14.3\link-static\asm\ontop.asm  
compile-c-c++ bin.v2\libs\context\build\msvc-14.3\debug\link-static\threading-multi\asm\tail_ontop_ppc32_sysv.obj  
tail_ontop_ppc32_sysv.cpp  
msvc.compile.asm bin.v2\libs\context\build\msvc-14.3\release\link-static\threading-multi\asm\ontop.obj  
 Assembling: bin.v2\libs\context\build\msvc-14.3\link-static\asm\ontop.asm  
bin.v2\libs\context\build\msvc-14.3\link-static\asm\ontop.asm(14080) : error A2008:syntax error : .  
bin.v2\libs\context\build\msvc-14.3\link-static\asm\ontop.asm(14081) : error A2008:syntax error : .  
bin.v2\libs\context\build\msvc-14.3\link-static\asm\ontop.asm(14082) : error A2008:syntax error : .  
bin.v2\libs\context\build\msvc-14.3\link-static\asm\ontop.asm(14096) : error A2008:syntax error : fs  
bin.v2\libs\context\build\msvc-14.3\link-static\asm\ontop.asm(14099) : error A2008:syntax error : fs  
bin.v2\libs\context\build\msvc-14.3\link-static\asm\ontop.asm(14149) : error A2008:syntax error : fs  
bin.v2\libs\context\build\msvc-14.3\link-static\asm\ontop.asm(14152) : error A2008:syntax error : fs  
bin.v2\libs\context\build\msvc-14.3\link-static\asm\ontop.asm(14180) : error A2077:instruction does not allow NEAR indirect addressing  
  
    call "bin.v2\standalone\msvc\msvc-14.3\msvc-setup.bat"  >nul  
 ml64 -c -Zp4 -Cp -Cx -nologo -DBOOST_ALL_NO_LIB=1 -DBOOST_CONTEXT_EXPORT= -DBOOST_CONTEXT_SOURCE -DBOOST_DISABLE_ASSERTS -DNDEBUG -D_WIN32_WINNT=0x0601 /W3  -Fo "bin.v2\libs\context\build\msvc-14.3\release\link-static\threading-multi\asm\ontop.obj" "bin.v2\libs\context\build\msvc-14.3\link-static\asm\ontop.asm"  
  
...failed msvc.compile.asm bin.v2\libs\context\build\msvc-14.3\release\link-static\threading-multi\asm\ontop.obj...  
msvc.compile.asm bin.v2\libs\context\build\msvc-14.3\release\link-static\threading-multi\asm\jump.obj  
 Assembling: bin.v2\libs\context\build\msvc-14.3\link-static\asm\jump.asm  
bin.v2\libs\context\build\msvc-14.3\link-static\asm\jump.asm(14080) : error A2008:syntax error : .  
bin.v2\libs\context\build\msvc-14.3\link-static\asm\jump.asm(14081) : error A2008:syntax error : .  
bin.v2\libs\context\build\msvc-14.3\link-static\asm\jump.asm(14082) : error A2008:syntax error : .  
bin.v2\libs\context\build\msvc-14.3\link-static\asm\jump.asm(14096) : error A2008:syntax error : fs  
bin.v2\libs\context\build\msvc-14.3\link-static\asm\jump.asm(14099) : error A2008:syntax error : fs  
bin.v2\libs\context\build\msvc-14.3\link-static\asm\jump.asm(14137) : error A2008:syntax error : fs  
bin.v2\libs\context\build\msvc-14.3\link-static\asm\jump.asm(14140) : error A2008:syntax error : fs  
bin.v2\libs\context\build\msvc-14.3\link-static\asm\jump.asm(14172) : error A2077:instruction does not allow NEAR indirect addressing  
  
    call "bin.v2\standalone\msvc\msvc-14.3\msvc-setup.bat"  >nul  
 ml64 -c -Zp4 -Cp -Cx -nologo -DBOOST_ALL_NO_LIB=1 -DBOOST_CONTEXT_EXPORT= -DBOOST_CONTEXT_SOURCE -DBOOST_DISABLE_ASSERTS -DNDEBUG -D_WIN32_WINNT=0x0601 /W3  -Fo "bin.v2\libs\context\build\msvc-14.3\release\link-static\threading-multi\asm\jump.obj" "bin.v2\libs\context\build\msvc-14.3\link-static\asm\jump.asm"  
  
...failed msvc.compile.asm bin.v2\libs\context\build\msvc-14.3\release\link-static\threading-multi\asm\jump.obj...  
mklink-or-dir boost\assert  
Junction created for boost\assert <<===>> libs\assert\include\boost\assert  
mklink-or-dir boost\bind  
Junction created for boost\bind <<===>> libs\bind\include\boost\bind  
link.hardlink boost\bind.hpp  
Hardlink created for boost\bind.hpp <<===>> libs\bind\include\boost\bind.hpp  
link.hardlink boost\is_placeholder.hpp  
Hardlink created for boost\is_placeholder.hpp <<===>> libs\bind\include\boost\is_placeholder.hpp  
link.hardlink boost\mem_fn.hpp  
Hardlink created for boost\mem_fn.hpp <<===>> libs\bind\include\boost\mem_fn.hpp  
compile-c-c++ bin.v2\libs\context\build\msvc-14.3\release\address-model-32\link-static\threading-multi\asm\tail_ontop_ppc32_sysv.obj  
tail_ontop_ppc32_sysv.cpp  
mklink-or-dir boost\concept  
Junction created for boost\concept <<===>> libs\concept_check\include\boost\concept  
mklink-or-dir boost\concept_check  
Junction created for boost\concept_check <<===>> libs\concept_check\include\boost\concept_check  
link.hardlink boost\concept_archetype.hpp  
Hardlink created for boost\concept_archetype.hpp <<===>> libs\concept_check\include\boost\concept_archetype.hpp  
link.hardlink boost\concept_check.hpp  
Hardlink created for boost\concept_check.hpp <<===>> libs\concept_check\include\boost\concept_check.hpp  
link.hardlink boost\cxx11_char_types.hpp  
Hardlink created for boost\cxx11_char_types.hpp <<===>> libs\config\include\boost\cxx11_char_types.hpp  
mklink-or-dir boost\container  
Junction created for boost\container <<===>> libs\container\include\boost\container  
mklink-or-dir boost\functional  
link.hardlink boost\implicit_cast.hpp  
Hardlink created for boost\implicit_cast.hpp <<===>> libs\conversion\include\boost\implicit_cast.hpp  
mklink-or-dir boost\container_hash  
Junction created for boost\container_hash <<===>> libs\container_hash\include\boost\container_hash  
mklink-or-dir boost\functional\hash  
Junction created for boost\functional\hash <<===>> libs\container_hash\include\boost\functional\hash  
link.hardlink boost\functional\hash.hpp  
Hardlink created for boost\functional\hash.hpp <<===>> libs\container_hash\include\boost\functional\hash.hpp  
compile-c-c++ bin.v2\libs\context\build\msvc-14.3\release\link-static\threading-multi\asm\tail_ontop_ppc32_sysv.obj  
tail_ontop_ppc32_sysv.cpp  
link.hardlink boost\functional\hash_fwd.hpp  
Hardlink created for boost\functional\hash_fwd.hpp <<===>> libs\container_hash\include\boost\functional\hash_fwd.hpp  
link.hardlink boost\polymorphic_cast.hpp  
Hardlink created for boost\polymorphic_cast.hpp <<===>> libs\conversion\include\boost\polymorphic_cast.hpp  
...on 100th target...  
link.hardlink boost\polymorphic_pointer_cast.hpp  
Hardlink created for boost\polymorphic_pointer_cast.hpp <<===>> libs\conversion\include\boost\polymorphic_pointer_cast.hpp  
link.hardlink boost\checked_delete.hpp  
Hardlink created for boost\checked_delete.hpp <<===>> libs\core\include\boost\checked_delete.hpp  
mklink-or-dir boost\core  
Junction created for boost\core <<===>> libs\core\include\boost\core  
link.hardlink boost\detail\lightweight_test.hpp  
Hardlink created for boost\detail\lightweight_test.hpp <<===>> libs\core\include\boost\detail\lightweight_test.hpp  
link.hardlink boost\detail\iterator.hpp  
Hardlink created for boost\detail\iterator.hpp <<===>> libs\core\include\boost\detail\iterator.hpp  
link.hardlink boost\detail\no_exceptions_support.hpp  
Hardlink created for boost\detail\no_exceptions_support.hpp <<===>> libs\core\include\boost\detail\no_exceptions_support.hpp  
link.hardlink boost\detail\scoped_enum_emulation.hpp  
Hardlink created for boost\detail\scoped_enum_emulation.hpp <<===>> libs\core\include\boost\detail\scoped_enum_emulation.hpp  
link.hardlink boost\detail\sp_typeinfo.hpp  
Hardlink created for boost\detail\sp_typeinfo.hpp <<===>> libs\core\include\boost\detail\sp_typeinfo.hpp  
link.hardlink boost\get_pointer.hpp  
Hardlink created for boost\get_pointer.hpp <<===>> libs\core\include\boost\get_pointer.hpp  
link.hardlink boost\iterator.hpp  
Hardlink created for boost\iterator.hpp <<===>> libs\core\include\boost\iterator.hpp  
link.hardlink boost\noncopyable.hpp  
Hardlink created for boost\noncopyable.hpp <<===>> libs\core\include\boost\noncopyable.hpp  
link.hardlink boost\non_type.hpp  
Hardlink created for boost\non_type.hpp <<===>> libs\core\include\boost\non_type.hpp  
link.hardlink boost\ref.hpp  
Hardlink created for boost\ref.hpp <<===>> libs\core\include\boost\ref.hpp  
link.hardlink boost\swap.hpp  
Hardlink created for boost\swap.hpp <<===>> libs\core\include\boost\swap.hpp  
link.hardlink boost\type.hpp  
Hardlink created for boost\type.hpp <<===>> libs\core\include\boost\type.hpp  
mklink-or-dir boost\utility  
mklink-or-dir boost\describe  
Junction created for boost\describe <<===>> libs\describe\include\boost\describe  
link.hardlink boost\describe.hpp  
Hardlink created for boost\describe.hpp <<===>> libs\describe\include\boost\describe.hpp  
link.hardlink boost\visit_each.hpp  
Hardlink created for boost\visit_each.hpp <<===>> libs\core\include\boost\visit_each.hpp  
link.hardlink boost\utility\addressof.hpp  
Hardlink created for boost\utility\addressof.hpp <<===>> libs\core\include\boost\utility\addressof.hpp  
link.hardlink boost\utility\enable_if.hpp  
Hardlink created for boost\utility\enable_if.hpp <<===>> libs\core\include\boost\utility\enable_if.hpp  
link.hardlink boost\utility\explicit_operator_bool.hpp  
Hardlink created for boost\utility\explicit_operator_bool.hpp <<===>> libs\core\include\boost\utility\explicit_operator_bool.hpp  
link.hardlink boost\blank.hpp  
Hardlink created for boost\blank.hpp <<===>> libs\detail\include\boost\blank.hpp  
link.hardlink boost\utility\swap.hpp  
Hardlink created for boost\utility\swap.hpp <<===>> libs\core\include\boost\utility\swap.hpp  
link.hardlink boost\blank_fwd.hpp  
Hardlink created for boost\blank_fwd.hpp <<===>> libs\detail\include\boost\blank_fwd.hpp  
link.hardlink boost\cstdlib.hpp  
Hardlink created for boost\cstdlib.hpp <<===>> libs\detail\include\boost\cstdlib.hpp  
link.hardlink boost\detail\allocator_utilities.hpp  
Hardlink created for boost\detail\allocator_utilities.hpp <<===>> libs\detail\include\boost\detail\allocator_utilities.hpp  
link.hardlink boost\detail\binary_search.hpp  
Hardlink created for boost\detail\binary_search.hpp <<===>> libs\detail\include\boost\detail\binary_search.hpp  
link.hardlink boost\detail\bitmask.hpp  
Hardlink created for boost\detail\bitmask.hpp <<===>> libs\detail\include\boost\detail\bitmask.hpp  
link.hardlink boost\detail\container_fwd.hpp  
Hardlink created for boost\detail\container_fwd.hpp <<===>> libs\detail\include\boost\detail\container_fwd.hpp  
link.hardlink boost\detail\fenv.hpp  
Hardlink created for boost\detail\fenv.hpp <<===>> libs\detail\include\boost\detail\fenv.hpp  
link.hardlink boost\detail\catch_exceptions.hpp  
Hardlink created for boost\detail\catch_exceptions.hpp <<===>> libs\detail\include\boost\detail\catch_exceptions.hpp  
link.hardlink boost\detail\has_default_constructor.hpp  
Hardlink created for boost\detail\has_default_constructor.hpp <<===>> libs\detail\include\boost\detail\has_default_constructor.hpp  
link.hardlink boost\detail\is_incrementable.hpp  
Hardlink created for boost\detail\is_incrementable.hpp <<===>> libs\detail\include\boost\detail\is_incrementable.hpp  
link.hardlink boost\detail\indirect_traits.hpp  
Hardlink created for boost\detail\indirect_traits.hpp <<===>> libs\detail\include\boost\detail\indirect_traits.hpp  
link.hardlink boost\detail\identifier.hpp  
Hardlink created for boost\detail\identifier.hpp <<===>> libs\detail\include\boost\detail\identifier.hpp  
link.hardlink boost\detail\is_sorted.hpp  
Hardlink created for boost\detail\is_sorted.hpp <<===>> libs\detail\include\boost\detail\is_sorted.hpp  
link.hardlink boost\detail\lightweight_main.hpp  
Hardlink created for boost\detail\lightweight_main.hpp <<===>> libs\detail\include\boost\detail\lightweight_main.hpp  
link.hardlink boost\detail\lightweight_test_report.hpp  
Hardlink created for boost\detail\lightweight_test_report.hpp <<===>> libs\detail\include\boost\detail\lightweight_test_report.hpp  
link.hardlink boost\detail\named_template_params.hpp  
Hardlink created for boost\detail\named_template_params.hpp <<===>> libs\detail\include\boost\detail\named_template_params.hpp  
link.hardlink boost\detail\is_xxx.hpp  
Hardlink created for boost\detail\is_xxx.hpp <<===>> libs\detail\include\boost\detail\is_xxx.hpp  
link.hardlink boost\detail\reference_content.hpp  
Hardlink created for boost\detail\reference_content.hpp <<===>> libs\detail\include\boost\detail\reference_content.hpp  
link.hardlink boost\detail\numeric_traits.hpp  
Hardlink created for boost\detail\numeric_traits.hpp <<===>> libs\detail\include\boost\detail\numeric_traits.hpp  
link.hardlink boost\detail\templated_streams.hpp  
Hardlink created for boost\detail\templated_streams.hpp <<===>> libs\detail\include\boost\detail\templated_streams.hpp  
link.hardlink boost\detail\select_type.hpp  
Hardlink created for boost\detail\select_type.hpp <<===>> libs\detail\include\boost\detail\select_type.hpp  
mklink-or-dir boost\function  
Junction created for boost\function <<===>> libs\function\include\boost\function  
link.hardlink boost\detail\utf8_codecvt_facet.hpp  
Hardlink created for boost\detail\utf8_codecvt_facet.hpp <<===>> libs\detail\include\boost\detail\utf8_codecvt_facet.hpp  
link.hardlink boost\detail\utf8_codecvt_facet.ipp  
Hardlink created for boost\detail\utf8_codecvt_facet.ipp <<===>> libs\detail\include\boost\detail\utf8_codecvt_facet.ipp  
link.hardlink boost\function.hpp  
Hardlink created for boost\function.hpp <<===>> libs\function\include\boost\function.hpp  
compile-c-c++ bin.v2\libs\context\build\msvc-14.3\release\address-model-32\link-static\threading-multi\windows\stack_traits.obj  
stack_traits.cpp  
link.hardlink boost\function_equal.hpp  
Hardlink created for boost\function_equal.hpp <<===>> libs\function\include\boost\function_equal.hpp  
link.hardlink boost\functional\factory.hpp  
Hardlink created for boost\functional\factory.hpp <<===>> libs\functional\include\boost\functional\factory.hpp  
compile-c-c++ bin.v2\libs\context\build\msvc-14.3\debug\address-model-32\link-static\threading-multi\windows\stack_traits.obj  
stack_traits.cpp  
link.hardlink boost\functional\forward_adapter.hpp  
Hardlink created for boost\functional\forward_adapter.hpp <<===>> libs\functional\include\boost\functional\forward_adapter.hpp  
link.hardlink boost\functional\identity.hpp  
Hardlink created for boost\functional\identity.hpp <<===>> libs\functional\include\boost\functional\identity.hpp  
link.hardlink boost\functional\lightweight_forward_adapter.hpp  
Hardlink created for boost\functional\lightweight_forward_adapter.hpp <<===>> libs\functional\include\boost\functional\lightweight_forward_adapter.hpp  
mklink-or-dir boost\functional\overloaded_function  
Junction created for boost\functional\overloaded_function <<===>> libs\functional\include\boost\functional\overloaded_function  
link.hardlink boost\functional\overloaded_function.hpp  
Hardlink created for boost\functional\overloaded_function.hpp <<===>> libs\functional\include\boost\functional\overloaded_function.hpp  
link.hardlink boost\functional\value_factory.hpp  
Hardlink created for boost\functional\value_factory.hpp <<===>> libs\functional\include\boost\functional\value_factory.hpp  
mklink-or-dir boost\function_types  
Junction created for boost\function_types <<===>> libs\function_types\include\boost\function_types  
link.hardlink boost\functional.hpp  
Hardlink created for boost\functional.hpp <<===>> libs\functional\include\boost\functional.hpp  
mklink-or-dir boost\fusion  
Junction created for boost\fusion <<===>> libs\fusion\include\boost\fusion  
mklink-or-dir boost\integer  
Junction created for boost\integer <<===>> libs\integer\include\boost\integer  
mklink-or-dir boost\headers  
Junction created for boost\headers <<===>> libs\headers\include\boost\headers  
compile-c-c++ bin.v2\libs\context\build\msvc-14.3\release\link-static\threading-multi\windows\stack_traits.obj  
stack_traits.cpp  
...skipped <pbin.v2\libs\context\build\msvc-14.3\release\link-static\threading-multi>libboost_context-vc143-mt-x64-1_83.lib for lack of <pbin.v2\libs\context\build\msvc-14.3\release\link-static\threading-multi>asm\make.obj...  
...skipped <pC:\Users\ruben\boost3\stage\lib>libboost_context-vc143-mt-x64-1_83.lib for lack of <pbin.v2\libs\context\build\msvc-14.3\release\link-static\threading-multi>libboost_context-vc143-mt-x64-1_83.lib...  
...skipped <pbin.v2\libs\context\build\msvc-14.3\release\link-static\threading-multi>libboost_context-variant-vc143-mt-x64-1_83-static.cmake for lack of <pbin.v2\libs\context\build\msvc-14.3\release\link-static\threading-multi>libboost_context-vc143-mt-x64-1_83.lib...  
...skipped <pC:\Users\ruben\boost3\stage\lib\cmake\boost_context-1.83.0>libboost_context-variant-vc143-mt-x64-1_83-static.cmake for lack of <pbin.v2\libs\context\build\msvc-14.3\release\link-static\threading-multi>libboost_context-variant-vc143-mt-x64-1_83-static.cmake...  
link.hardlink boost\integer.hpp  
Hardlink created for boost\integer.hpp <<===>> libs\integer\include\boost\integer.hpp  
link.hardlink boost\integer_fwd.hpp  
Hardlink created for boost\integer_fwd.hpp <<===>> libs\integer\include\boost\integer_fwd.hpp  
link.hardlink boost\integer_traits.hpp  
Hardlink created for boost\integer_traits.hpp <<===>> libs\integer\include\boost\integer_traits.hpp  
mklink-or-dir boost\pending  
mklink-or-dir boost\io  
Junction created for boost\io <<===>> libs\io\include\boost\io  
mklink-or-dir boost\intrusive  
Junction created for boost\intrusive <<===>> libs\intrusive\include\boost\intrusive  
compile-c-c++ bin.v2\libs\context\build\msvc-14.3\debug\link-static\threading-multi\windows\stack_traits.obj  
stack_traits.cpp  
...skipped <pbin.v2\libs\context\build\msvc-14.3\debug\link-static\threading-multi>libboost_context-vc143-mt-gd-x64-1_83.lib for lack of <pbin.v2\libs\context\build\msvc-14.3\debug\link-static\threading-multi>asm\make.obj...  
...skipped <pC:\Users\ruben\boost3\stage\lib>libboost_context-vc143-mt-gd-x64-1_83.lib for lack of <pbin.v2\libs\context\build\msvc-14.3\debug\link-static\threading-multi>libboost_context-vc143-mt-gd-x64-1_83.lib...  
...skipped <pbin.v2\libs\context\build\msvc-14.3\debug\link-static\threading-multi>libboost_context-variant-vc143-mt-gd-x64-1_83-static.cmake for lack of <pbin.v2\libs\context\build\msvc-14.3\debug\link-static\threading-multi>libboost_context-vc143-mt-gd-x64-1_83.lib...  
...skipped <pC:\Users\ruben\boost3\stage\lib\cmake\boost_context-1.83.0>libboost_context-variant-vc143-mt-gd-x64-1_83-static.cmake for lack of <pbin.v2\libs\context\build\msvc-14.3\debug\link-static\threading-multi>libboost_context-variant-vc143-mt-gd-x64-1_83-static.cmake...  
msvc.archive bin.v2\libs\context\build\msvc-14.3\release\address-model-32\link-static\threading-multi\libboost_context-vc143-mt-x32-1_83.lib  
link.hardlink boost\function_output_iterator.hpp  
Hardlink created for boost\function_output_iterator.hpp <<===>> libs\iterator\include\boost\function_output_iterator.hpp  
boost-install.generate-cmake-variant- bin.v2\libs\context\build\msvc-14.3\release\address-model-32\link-static\threading-multi\libboost_context-variant-vc143-mt-x32-1_83-static.cmake  
link.hardlink boost\io_fwd.hpp  
Hardlink created for boost\io_fwd.hpp <<===>> libs\io\include\boost\io_fwd.hpp  
mklink-or-dir boost\iterator  
Junction created for boost\iterator <<===>> libs\iterator\include\boost\iterator  
link.hardlink boost\pending\integer_log2.hpp  
Hardlink created for boost\pending\integer_log2.hpp <<===>> libs\integer\include\boost\pending\integer_log2.hpp  
link.hardlink boost\indirect_reference.hpp  
Hardlink created for boost\indirect_reference.hpp <<===>> libs\iterator\include\boost\indirect_reference.hpp  
link.hardlink boost\next_prior.hpp  
Hardlink created for boost\next_prior.hpp <<===>> libs\iterator\include\boost\next_prior.hpp  
common.copy C:\Users\ruben\boost3\stage\lib\libboost_context-vc143-mt-x32-1_83.lib  
bin.v2\libs\context\build\msvc-14.3\release\address-model-32\link-static\threading-multi\libboost_context-vc143-mt-x32-1_83.lib  
        1 file(s) copied.  
common.copy C:\Users\ruben\boost3\stage\lib\cmake\boost_context-1.83.0\libboost_context-variant-vc143-mt-x32-1_83-static.cmake  
bin.v2\libs\context\build\msvc-14.3\release\address-model-32\link-static\threading-multi\libboost_context-variant-vc143-mt-x32-1_83-static.cmake  
        1 file(s) copied.  
link.hardlink boost\pending\iterator_tests.hpp  
Hardlink created for boost\pending\iterator_tests.hpp <<===>> libs\iterator\include\boost\pending\iterator_tests.hpp  
link.hardlink boost\pointee.hpp  
Hardlink created for boost\pointee.hpp <<===>> libs\iterator\include\boost\pointee.hpp  
link.hardlink boost\shared_container_iterator.hpp  
Hardlink created for boost\shared_container_iterator.hpp <<===>> libs\iterator\include\boost\shared_container_iterator.hpp  
link.hardlink boost\detail\basic_pointerbuf.hpp  
Hardlink created for boost\detail\basic_pointerbuf.hpp <<===>> libs\lexical_cast\include\boost\detail\basic_pointerbuf.hpp  
link.hardlink boost\iterator_adaptors.hpp  
Hardlink created for boost\iterator_adaptors.hpp <<===>> libs\iterator\include\boost\iterator_adaptors.hpp  
link.hardlink boost\detail\lcast_precision.hpp  
Hardlink created for boost\detail\lcast_precision.hpp <<===>> libs\lexical_cast\include\boost\detail\lcast_precision.hpp  
link.hardlink boost\lexical_cast.hpp  
Hardlink created for boost\lexical_cast.hpp <<===>> libs\lexical_cast\include\boost\lexical_cast.hpp  
mklink-or-dir boost\lexical_cast  
Junction created for boost\lexical_cast <<===>> libs\lexical_cast\include\boost\lexical_cast  
mklink-or-dir boost\mp11  
Junction created for boost\mp11 <<===>> libs\mp11\include\boost\mp11  
mklink-or-dir boost\move  
Junction created for boost\move <<===>> libs\move\include\boost\move  
link.hardlink boost\mp11.hpp  
Hardlink created for boost\mp11.hpp <<===>> libs\mp11\include\boost\mp11.hpp  
mklink-or-dir boost\mpl  
Junction created for boost\mpl <<===>> libs\mpl\include\boost\mpl  
link.hardlink boost\none.hpp  
Hardlink created for boost\none.hpp <<===>> libs\optional\include\boost\none.hpp  
mklink-or-dir boost\optional  
Junction created for boost\optional <<===>> libs\optional\include\boost\optional  
link.hardlink boost\none_t.hpp  
Hardlink created for boost\none_t.hpp <<===>> libs\optional\include\boost\none_t.hpp  
link.hardlink boost\optional.hpp  
Hardlink created for boost\optional.hpp <<===>> libs\optional\include\boost\optional.hpp  
link.hardlink boost\predef.h  
Hardlink created for boost\predef.h <<===>> libs\predef\include\boost\predef.h  
mklink-or-dir boost\pool  
Junction created for boost\pool <<===>> libs\pool\include\boost\pool  
...on 200th target...  
mklink-or-dir boost\preprocessor  
Junction created for boost\preprocessor <<===>> libs\preprocessor\include\boost\preprocessor  
mklink-or-dir boost\range  
Junction created for boost\range <<===>> libs\range\include\boost\range  
link.hardlink boost\preprocessor.hpp  
Hardlink created for boost\preprocessor.hpp <<===>> libs\preprocessor\include\boost\preprocessor.hpp  
link.hardlink boost\range.hpp  
Hardlink created for boost\range.hpp <<===>> libs\range\include\boost\range.hpp  
mklink-or-dir boost\regex  
Junction created for boost\regex <<===>> libs\regex\include\boost\regex  
link.hardlink boost\cregex.hpp  
Hardlink created for boost\cregex.hpp <<===>> libs\regex\include\boost\cregex.hpp  
link.hardlink boost\regex.hpp  
Hardlink created for boost\regex.hpp <<===>> libs\regex\include\boost\regex.hpp  
link.hardlink boost\regex_fwd.hpp  
Hardlink created for boost\regex_fwd.hpp <<===>> libs\regex\include\boost\regex_fwd.hpp  
link.hardlink boost\regex.h  
Hardlink created for boost\regex.h <<===>> libs\regex\include\boost\regex.h  
link.hardlink boost\detail\lightweight_mutex.hpp  
Hardlink created for boost\detail\lightweight_mutex.hpp <<===>> libs\smart_ptr\include\boost\detail\lightweight_mutex.hpp  
link.hardlink boost\detail\lightweight_thread.hpp  
Hardlink created for boost\detail\lightweight_thread.hpp <<===>> libs\smart_ptr\include\boost\detail\lightweight_thread.hpp  
link.hardlink boost\detail\quick_allocator.hpp  
Hardlink created for boost\detail\quick_allocator.hpp <<===>> libs\smart_ptr\include\boost\detail\quick_allocator.hpp  
link.hardlink boost\detail\atomic_count.hpp  
Hardlink created for boost\detail\atomic_count.hpp <<===>> libs\smart_ptr\include\boost\detail\atomic_count.hpp  
link.hardlink boost\enable_shared_from_this.hpp  
Hardlink created for boost\enable_shared_from_this.hpp <<===>> libs\smart_ptr\include\boost\enable_shared_from_this.hpp  
link.hardlink boost\intrusive_ptr.hpp  
Hardlink created for boost\intrusive_ptr.hpp <<===>> libs\smart_ptr\include\boost\intrusive_ptr.hpp  
link.hardlink boost\make_shared.hpp  
Hardlink created for boost\make_shared.hpp <<===>> libs\smart_ptr\include\boost\make_shared.hpp  
link.hardlink boost\pointer_cast.hpp  
Hardlink created for boost\pointer_cast.hpp <<===>> libs\smart_ptr\include\boost\pointer_cast.hpp  
link.hardlink boost\pointer_to_other.hpp  
Hardlink created for boost\pointer_to_other.hpp <<===>> libs\smart_ptr\include\boost\pointer_to_other.hpp  
link.hardlink boost\scoped_array.hpp  
Hardlink created for boost\scoped_array.hpp <<===>> libs\smart_ptr\include\boost\scoped_array.hpp  
link.hardlink boost\make_unique.hpp  
Hardlink created for boost\make_unique.hpp <<===>> libs\smart_ptr\include\boost\make_unique.hpp  
link.hardlink boost\shared_array.hpp  
Hardlink created for boost\shared_array.hpp <<===>> libs\smart_ptr\include\boost\shared_array.hpp  
link.hardlink boost\scoped_ptr.hpp  
Hardlink created for boost\scoped_ptr.hpp <<===>> libs\smart_ptr\include\boost\scoped_ptr.hpp  
link.hardlink boost\shared_ptr.hpp  
Hardlink created for boost\shared_ptr.hpp <<===>> libs\smart_ptr\include\boost\shared_ptr.hpp  
mklink-or-dir boost\smart_ptr  
Junction created for boost\smart_ptr <<===>> libs\smart_ptr\include\boost\smart_ptr  
link.hardlink boost\smart_ptr.hpp  
Hardlink created for boost\smart_ptr.hpp <<===>> libs\smart_ptr\include\boost\smart_ptr.hpp  
link.hardlink boost\weak_ptr.hpp  
Hardlink created for boost\weak_ptr.hpp <<===>> libs\smart_ptr\include\boost\weak_ptr.hpp  
link.hardlink boost\static_assert.hpp  
Hardlink created for boost\static_assert.hpp <<===>> libs\static_assert\include\boost\static_assert.hpp  
mklink-or-dir boost\exception  
Junction created for boost\exception <<===>> libs\throw_exception\include\boost\exception  
link.hardlink boost\throw_exception.hpp  
Hardlink created for boost\throw_exception.hpp <<===>> libs\throw_exception\include\boost\throw_exception.hpp  
mklink-or-dir boost\tuple  
Junction created for boost\tuple <<===>> libs\tuple\include\boost\tuple  
mklink-or-dir boost\typeof  
Junction created for boost\typeof <<===>> libs\typeof\include\boost\typeof  
mklink-or-dir boost\type_index  
Junction created for boost\type_index <<===>> libs\type_index\include\boost\type_index  
link.hardlink boost\type_index.hpp  
Hardlink created for boost\type_index.hpp <<===>> libs\type_index\include\boost\type_index.hpp  
link.hardlink boost\aligned_storage.hpp  
Hardlink created for boost\aligned_storage.hpp <<===>> libs\type_traits\include\boost\aligned_storage.hpp  
mklink-or-dir boost\type_traits  
Junction created for boost\type_traits <<===>> libs\type_traits\include\boost\type_traits  
link.hardlink boost\type_traits.hpp  
Hardlink created for boost\type_traits.hpp <<===>> libs\type_traits\include\boost\type_traits.hpp  
link.hardlink boost\utility\declval.hpp  
Hardlink created for boost\utility\declval.hpp <<===>> libs\type_traits\include\boost\utility\declval.hpp  
link.hardlink boost\call_traits.hpp  
Hardlink created for boost\call_traits.hpp <<===>> libs\utility\include\boost\call_traits.hpp  
link.hardlink boost\compressed_pair.hpp  
Hardlink created for boost\compressed_pair.hpp <<===>> libs\utility\include\boost\compressed_pair.hpp  
link.hardlink boost\detail\call_traits.hpp  
Hardlink created for boost\detail\call_traits.hpp <<===>> libs\utility\include\boost\detail\call_traits.hpp  
link.hardlink boost\detail\compressed_pair.hpp  
Hardlink created for boost\detail\compressed_pair.hpp <<===>> libs\utility\include\boost\detail\compressed_pair.hpp  
link.hardlink boost\detail\ob_compressed_pair.hpp  
Hardlink created for boost\detail\ob_compressed_pair.hpp <<===>> libs\utility\include\boost\detail\ob_compressed_pair.hpp  
link.hardlink boost\operators.hpp  
Hardlink created for boost\operators.hpp <<===>> libs\utility\include\boost\operators.hpp  
link.hardlink boost\utility\base_from_member.hpp  
Hardlink created for boost\utility\base_from_member.hpp <<===>> libs\utility\include\boost\utility\base_from_member.hpp  
link.hardlink boost\utility\binary.hpp  
Hardlink created for boost\utility\binary.hpp <<===>> libs\utility\include\boost\utility\binary.hpp  
link.hardlink boost\operators_v1.hpp  
Hardlink created for boost\operators_v1.hpp <<===>> libs\utility\include\boost\operators_v1.hpp  
mklink-or-dir boost\utility\detail  
Junction created for boost\utility\detail <<===>> libs\utility\include\boost\utility\detail  
link.hardlink boost\utility\compare_pointees.hpp  
Hardlink created for boost\utility\compare_pointees.hpp <<===>> libs\utility\include\boost\utility\compare_pointees.hpp  
link.hardlink boost\utility\identity_type.hpp  
Hardlink created for boost\utility\identity_type.hpp <<===>> libs\utility\include\boost\utility\identity_type.hpp  
link.hardlink boost\utility\in_place_factory.hpp  
Hardlink created for boost\utility\in_place_factory.hpp <<===>> libs\utility\include\boost\utility\in_place_factory.hpp  
link.hardlink boost\utility\result_of.hpp  
Hardlink created for boost\utility\result_of.hpp <<===>> libs\utility\include\boost\utility\result_of.hpp  
link.hardlink boost\utility\string_ref.hpp  
Hardlink created for boost\utility\string_ref.hpp <<===>> libs\utility\include\boost\utility\string_ref.hpp  
link.hardlink boost\utility\string_ref_fwd.hpp  
Hardlink created for boost\utility\string_ref_fwd.hpp <<===>> libs\utility\include\boost\utility\string_ref_fwd.hpp  
link.hardlink boost\utility\string_view.hpp  
Hardlink created for boost\utility\string_view.hpp <<===>> libs\utility\include\boost\utility\string_view.hpp  
link.hardlink boost\utility\string_view_fwd.hpp  
Hardlink created for boost\utility\string_view_fwd.hpp <<===>> libs\utility\include\boost\utility\string_view_fwd.hpp  
link.hardlink boost\utility\value_init.hpp  
Hardlink created for boost\utility\value_init.hpp <<===>> libs\utility\include\boost\utility\value_init.hpp  
link.hardlink boost\utility\typed_in_place_factory.hpp  
Hardlink created for boost\utility\typed_in_place_factory.hpp <<===>> libs\utility\include\boost\utility\typed_in_place_factory.hpp  
mklink-or-dir boost\variant  
Junction created for boost\variant <<===>> libs\variant\include\boost\variant  
link.hardlink boost\utility.hpp  
Hardlink created for boost\utility.hpp <<===>> libs\utility\include\boost\utility.hpp  
link.hardlink boost\variant.hpp  
Hardlink created for boost\variant.hpp <<===>> libs\variant\include\boost\variant.hpp  
link.hardlink boost\detail\interlocked.hpp  
Hardlink created for boost\detail\interlocked.hpp <<===>> libs\winapi\include\boost\detail\interlocked.hpp  
mklink-or-dir boost\detail\winapi  
Junction created for boost\detail\winapi <<===>> libs\winapi\include\boost\detail\winapi  
mklink-or-dir boost\winapi  
Junction created for boost\winapi <<===>> libs\winapi\include\boost\winapi  
mklink-or-dir boost\pending\detail  
Junction created for boost\pending\detail <<===>> libs\iterator\include\boost\pending\detail  
link.hardlink boost\pending\iterator_adaptors.hpp  
Hardlink created for boost\pending\iterator_adaptors.hpp <<===>> libs\iterator\include\boost\pending\iterator_adaptors.hpp  
link.hardlink boost\generator_iterator.hpp  
Hardlink created for boost\generator_iterator.hpp <<===>> libs\iterator\include\boost\generator_iterator.hpp  
msvc.archive bin.v2\libs\context\build\msvc-14.3\debug\address-model-32\link-static\threading-multi\libboost_context-vc143-mt-gd-x32-1_83.lib  
boost-install.generate-cmake-variant- bin.v2\libs\context\build\msvc-14.3\debug\address-model-32\link-static\threading-multi\libboost_context-variant-vc143-mt-gd-x32-1_83-static.cmake  
link.hardlink boost\cast.hpp  
Hardlink created for boost\cast.hpp <<===>> libs\numeric\conversion\include\boost\cast.hpp  
mklink-or-dir boost\numeric  
Junction created for boost\numeric <<===>> libs\numeric\conversion\include\boost\numeric  
common.copy C:\Users\ruben\boost3\stage\lib\libboost_context-vc143-mt-gd-x32-1_83.lib  
bin.v2\libs\context\build\msvc-14.3\debug\address-model-32\link-static\threading-multi\libboost_context-vc143-mt-gd-x32-1_83.lib  
        1 file(s) copied.  
common.copy C:\Users\ruben\boost3\stage\lib\cmake\boost_context-1.83.0\libboost_context-variant-vc143-mt-gd-x32-1_83-static.cmake  
bin.v2\libs\context\build\msvc-14.3\debug\address-model-32\link-static\threading-multi\libboost_context-variant-vc143-mt-gd-x32-1_83-static.cmake  
        1 file(s) copied.  
...failed updating 6 targets...  
...skipped 8 targets...  
...updated 258 targets...  
```

---

## Comment 1

> Username: anarthal  
> Created at: 2023-07-17 10:10:06 UTC  
> Url: https://github.com/boostorg/context/issues/233#issuecomment-1637779265  

Doing the above with the current master branch yields a different error:  
  
```  
PS> .\b2 --with-context    
Performing configuration checks  
  
    - default address-model    : 64-bit (cached) [1]  
    - default architecture     : x86 (cached) [1]  
  
Building the Boost C++ Libraries.  
  
  
    - symlinks supported       : no  (cached)  
    - junctions supported      : yes (cached)  
    - hardlinks supported      : yes (cached)  
error: Name clash for '<pbin.v2\standalone\msvc\msvc-14.3>msvc-setup.bat'  
error:   
error: Tried to build the target twice, with property sets having  
error: these incompatible properties:  
error:  
error:     -  none  
error:     -  <address-model>32 <architecture>x86  
error:  
error: Please make sure to have consistent requirements for these  
error: properties everywhere in your project, especially for install  
error: targets.  
```

---

## Comment 2

> Username: olk  
> Created at: 2023-08-18 04:57:17 UTC  
> Url: https://github.com/boostorg/context/issues/233#issuecomment-1683353568  

should work now
