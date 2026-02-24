# #414 [OpenBSD] Add Missing Semicolons [Merged]

> Username: ghost  
> Created at: 2024-10-29 00:16:03 UTC  
> Updated at: 2024-10-29 00:34:57 UTC  
> Merged at: 2024-10-29 00:33:06 UTC  
> Closed at: 2024-10-29 00:33:06 UTC  
> Url: https://github.com/boostorg/process/pull/414  

Stuff not passing can be safely ignored as it is features not implemented by OpenBSD OS-level.  
```  
openbsd$ '/home/openbsd/boost/b2' visibility=global  
Performing configuration checks  
  
    - default address-model    : 64-bit (cached) [1]  
    - default architecture     : x86 (cached) [1]  
    - symlinks supported       : yes (cached)  
    - has -Wl,--no-undefined   : no  (cached) [2]  
    - has -Wl,-undefined,error : no  (cached) [2]  
    - has statx                : no  (cached) [2]  
    - has statx syscall        : no  (cached) [2]  
    - cxx11_rvalue_references  : yes (cached) [2]  
    - cxx11_scoped_enums       : yes (cached) [2]  
    - cxx11_noexcept           : yes (cached) [2]  
    - cxx11_nullptr            : yes (cached) [2]  
    - cxx11_defaulted_functions : yes (cached) [2]  
    - cxx11_defaulted_moves    : yes (cached) [2]  
    - cxx11_deleted_functions  : yes (cached) [2]  
    - cxx11_function_template_default_args : yes (cached) [2]  
    - cxx11_final              : yes (cached) [2]  
    - cxx11_override           : yes (cached) [2]  
    - has init_priority attribute : yes (cached) [2]  
    - has stat::st_blksize     : yes (cached) [2]  
    - has stat::st_mtim        : yes (cached) [2]  
    - has stat::st_mtimensec   : yes (cached) [2]  
    - has stat::st_mtimespec   : yes (cached) [2]  
    - has stat::st_birthtim    : no  (cached) [2]  
    - has stat::st_birthtimensec : no  (cached) [2]  
    - has stat::st_birthtimespec : no  (cached) [2]  
    - has fdopendir(O_NOFOLLOW) : yes (cached) [2]  
    - has dirent::d_type       : yes (cached) [2]  
    - has POSIX *at APIs       : no  (cached) [2]  
    - has fallocate            : no  (cached) [2]  
    - BOOST_COMP_GNUC >= 4.3.0 : no  (cached) [2]  
  
[1] clang-13  
[2] clang-linux-13/debug/x86_64/boost.process.fs-boost/visibility-global  
...patience...  
...patience...  
...found 5415 targets...  
...updating 25 targets...  
clang-linux.compile.c++ ../../../../bin.v2/libs/process/build/clang-linux-13/debug/x86_64/boost.process.fs-boost/visibility-global/ext/cmd.o  
clang-linux.compile.c++ ../../../../bin.v2/libs/process/build/clang-linux-13/debug/x86_64/boost.process.fs-boost/visibility-global/ext/env.o  
clang-linux.link.dll ../../../../bin.v2/libs/process/build/clang-linux-13/debug/x86_64/boost.process.fs-boost/visibility-global/libboost_process.so.1.87.0  
clang-linux.link ../../../../bin.v2/libs/process/test/v2/utf8.test/clang-linux-13/debug/x86_64/boost.process.fs-boost/visibility-global/utf8  
clang-linux.link ../../../../bin.v2/libs/process/test/v2/clang-linux-13/debug/x86_64/boost.process.fs-boost/visibility-global/target  
clang-linux.link ../../../../bin.v2/libs/process/test/v2/cstring_ref.test/clang-linux-13/debug/x86_64/boost.process.fs-boost/visibility-global/cstring_ref  
clang-linux.link ../../../../bin.v2/libs/process/test/v2/environment.test/clang-linux-13/debug/x86_64/boost.process.fs-boost/visibility-global/environment  
testing.capture-output ../../../../bin.v2/libs/process/test/v2/utf8.test/clang-linux-13/debug/x86_64/boost.process.fs-boost/visibility-global/utf8.run  
**passed** ../../../../bin.v2/libs/process/test/v2/utf8.test/clang-linux-13/debug/x86_64/boost.process.fs-boost/visibility-global/utf8.test  
testing.capture-output ../../../../bin.v2/libs/process/test/v2/cstring_ref.test/clang-linux-13/debug/x86_64/boost.process.fs-boost/visibility-global/cstring_ref.run  
**passed** ../../../../bin.v2/libs/process/test/v2/cstring_ref.test/clang-linux-13/debug/x86_64/boost.process.fs-boost/visibility-global/cstring_ref.test  
testing.capture-output ../../../../bin.v2/libs/process/test/v2/environment.test/clang-linux-13/debug/x86_64/boost.process.fs-boost/visibility-global/environment.run  
**passed** ../../../../bin.v2/libs/process/test/v2/environment.test/clang-linux-13/debug/x86_64/boost.process.fs-boost/visibility-global/environment.test  
clang-linux.link ../../../../bin.v2/libs/process/test/v2/shell.test/clang-linux-13/debug/x86_64/boost.process.fs-boost/visibility-global/shell  
testing.capture-output ../../../../bin.v2/libs/process/test/v2/shell.test/clang-linux-13/debug/x86_64/boost.process.fs-boost/visibility-global/shell.run  
====== BEGIN OUTPUT ======  
Running 1 test case...  
unknown location(0): fatal error: in "test_shell_parser": boost::system::system_error: shell::parse: Not supported [system:91 at ../../src/shell.cpp:150:62 in function 'parse_']  
shell.cpp(32): last checkpoint  
  
*** 1 failure is detected in the test module "process_v2_test"  
  
EXIT STATUS: 201  
====== END OUTPUT ======  
  
    LD_LIBRARY_PATH="/home/openbsd/boost/bin.v2/libs/filesystem/build/clang-linux-13/debug/x86_64/visibility-global:/home/openbsd/boost/bin.v2/libs/process/build/clang-linux-13/debug/x86_64/boost.process.fs-boost/visibility-global:/home/openbsd/boost/bin.v2/libs/system/build/clang-linux-13/debug/x86_64/visibility-global:/home/openbsd/boost/bin.v2/libs/test/build/clang-linux-13/debug/x86_64/visibility-global:$LD_LIBRARY_PATH"  
export LD_LIBRARY_PATH  
  
     "../../../../bin.v2/libs/process/test/v2/shell.test/clang-linux-13/debug/x86_64/boost.process.fs-boost/visibility-global/shell"   > "../../../../bin.v2/libs/process/test/v2/shell.test/clang-linux-13/debug/x86_64/boost.process.fs-boost/visibility-global/shell.output" 2>&1 < /dev/null  
    status=$?  
    echo >> "../../../../bin.v2/libs/process/test/v2/shell.test/clang-linux-13/debug/x86_64/boost.process.fs-boost/visibility-global/shell.output"  
    echo EXIT STATUS: $status >> "../../../../bin.v2/libs/process/test/v2/shell.test/clang-linux-13/debug/x86_64/boost.process.fs-boost/visibility-global/shell.output"  
    if test $status -eq 0 ; then  
        cp "../../../../bin.v2/libs/process/test/v2/shell.test/clang-linux-13/debug/x86_64/boost.process.fs-boost/visibility-global/shell.output" "../../../../bin.v2/libs/process/test/v2/shell.test/clang-linux-13/debug/x86_64/boost.process.fs-boost/visibility-global/shell.run" >/dev/null  
    fi  
    verbose=0  
    if test $status -ne 0 ; then  
        verbose=1  
    fi  
    if test $verbose -eq 1 ; then  
        echo ====== BEGIN OUTPUT ======  
        cat "../../../../bin.v2/libs/process/test/v2/shell.test/clang-linux-13/debug/x86_64/boost.process.fs-boost/visibility-global/shell.output"  
        echo ====== END OUTPUT ======  
    fi  
    exit $status  
  
...failed testing.capture-output ../../../../bin.v2/libs/process/test/v2/shell.test/clang-linux-13/debug/x86_64/boost.process.fs-boost/visibility-global/shell.run...  
clang-linux.link ../../../../bin.v2/libs/process/test/v2/pid.test/clang-linux-13/debug/x86_64/boost.process.fs-boost/visibility-global/pid  
clang-linux.link ../../../../bin.v2/libs/process/test/v2/ext.test/clang-linux-13/debug/x86_64/boost.process.fs-boost/visibility-global/ext  
clang-linux.link ../../../../bin.v2/libs/process/test/v2/process.test/clang-linux-13/debug/x86_64/boost.process.fs-boost/visibility-global/process  
testing.capture-output ../../../../bin.v2/libs/process/test/v2/ext.test/clang-linux-13/debug/x86_64/boost.process.fs-boost/visibility-global/ext.run  
====== BEGIN OUTPUT ======  
Running 8 test cases...  
Entering test module "process_v2_test"  
ext.cpp(16): Entering test suite "ext"  
ext.cpp(18): Entering test case "test_exe"  
unknown location(0): fatal error: in "ext/test_exe": Throw location unknown (consider using BOOST_THROW_EXCEPTION)  
Dynamic exception type: boost::wrapexcept<boost::system::system_error>  
std::exception::what: exe: Not supported [system:91 at ../../src/ext/exe.cpp:176:62 in function 'exe']  
  
ext.cpp(18): last checkpoint: "test_exe" test entry  
ext.cpp(18): Leaving test case "test_exe"; testing time: 7332us  
ext.cpp(30): Entering test case "test_child_exe"  
unknown location(0): fatal error: in "ext/test_child_exe": Throw location unknown (consider using BOOST_THROW_EXCEPTION)  
Dynamic exception type: boost::wrapexcept<boost::system::system_error>  
std::exception::what: exe: Not supported [system:91 at ../../src/ext/exe.cpp:176:62 in function 'exe']  
  
ext.cpp(38): last checkpoint  
ext.cpp(30): Leaving test case "test_child_exe"; testing time: 13158us  
ext.cpp(41): Entering test case "cmd"  
ext.cpp(53): info: check bp2::detail::conv_string<char>( ref.data(), ref.size() ) == master_test_suite().argv[0] has passed  
ext.cpp(61): info: check bp2::detail::conv_string<char>(ref.data(), ref.size()) == master_test_suite().argv[i] has passed  
ext.cpp(41): Leaving test case "cmd"; testing time: 619us  
ext.cpp(67): Entering test case "cmd_exe"  
ext.cpp(80): info: check bp2::detail::conv_string<char>(ref.data(), ref.size()) == pth has passed  
ext.cpp(82): info: check cm.argc() == args.size() + 1u has passed  
ext.cpp(87): info: check bp2::detail::conv_string<char>(ref.data(), ref.size()) == args[i] has passed  
ext.cpp(87): info: check bp2::detail::conv_string<char>(ref.data(), ref.size()) == args[i] has passed  
ext.cpp(87): info: check bp2::detail::conv_string<char>(ref.data(), ref.size()) == args[i] has passed  
ext.cpp(87): info: check bp2::detail::conv_string<char>(ref.data(), ref.size()) == args[i] has passed  
ext.cpp(87): info: check bp2::detail::conv_string<char>(ref.data(), ref.size()) == args[i] has passed  
ext.cpp(67): Leaving test case "cmd_exe"; testing time: 3572us  
ext.cpp(93): Entering test case "test_cwd"  
ext.cpp(99): info: check pth == bp2::filesystem::current_path() has passed  
ext.cpp(93): Leaving test case "test_cwd"; testing time: 1177us  
ext.cpp(102): Entering test case "test_cwd_exe"  
ext.cpp(116): info: check tt == tmp has passed  
ext.cpp(102): Leaving test case "test_cwd_exe"; testing time: 13043us  
ext.cpp(121): Entering test case "test_env"  
ext.cpp(137): info: check kp.value() == (*itr).value() has passed  
ext.cpp(137): info: check kp.value() == (*itr).value() has passed  
ext.cpp(137): info: check kp.value() == (*itr).value() has passed  
ext.cpp(137): info: check kp.value() == (*itr).value() has passed  
ext.cpp(137): info: check kp.value() == (*itr).value() has passed  
ext.cpp(137): info: check kp.value() == (*itr).value() has passed  
ext.cpp(137): info: check kp.value() == (*itr).value() has passed  
ext.cpp(137): info: check kp.value() == (*itr).value() has passed  
ext.cpp(137): info: check kp.value() == (*itr).value() has passed  
ext.cpp(137): info: check kp.value() == (*itr).value() has passed  
ext.cpp(137): info: check kp.value() == (*itr).value() has passed  
ext.cpp(137): info: check kp.value() == (*itr).value() has passed  
ext.cpp(137): info: check kp.value() == (*itr).value() has passed  
ext.cpp(137): info: check kp.value() == (*itr).value() has passed  
ext.cpp(137): info: check kp.value() == (*itr).value() has passed  
ext.cpp(137): info: check kp.value() == (*itr).value() has passed  
ext.cpp(137): info: check kp.value() == (*itr).value() has passed  
ext.cpp(137): info: check kp.value() == (*itr).value() has passed  
ext.cpp(137): info: check kp.value() == (*itr).value() has passed  
ext.cpp(137): info: check kp.value() == (*itr).value() has passed  
ext.cpp(137): info: check kp.value() == (*itr).value() has passed  
ext.cpp(142): info: check e > 0u has passed  
ext.cpp(121): Leaving test case "test_env"; testing time: 2638us  
ext.cpp(145): Entering test case "test_env_exe"  
ext.cpp(175): info: check itr != env.end() has passed  
ext.cpp(176): info: check kp.value() == (*itr).value() has passed  
ext.cpp(175): info: check itr != env.end() has passed  
ext.cpp(176): info: check kp.value() == (*itr).value() has passed  
ext.cpp(175): info: check itr != env.end() has passed  
ext.cpp(176): info: check kp.value() == (*itr).value() has passed  
ext.cpp(175): info: check itr != env.end() has passed  
ext.cpp(176): info: check kp.value() == (*itr).value() has passed  
ext.cpp(175): info: check itr != env.end() has passed  
ext.cpp(176): info: check kp.value() == (*itr).value() has passed  
ext.cpp(175): info: check itr != env.end() has passed  
ext.cpp(176): info: check kp.value() == (*itr).value() has passed  
ext.cpp(175): info: check itr != env.end() has passed  
ext.cpp(176): info: check kp.value() == (*itr).value() has passed  
ext.cpp(175): info: check itr != env.end() has passed  
ext.cpp(176): info: check kp.value() == (*itr).value() has passed  
ext.cpp(175): info: check itr != env.end() has passed  
ext.cpp(176): info: check kp.value() == (*itr).value() has passed  
ext.cpp(175): info: check itr != env.end() has passed  
ext.cpp(176): info: check kp.value() == (*itr).value() has passed  
ext.cpp(175): info: check itr != env.end() has passed  
ext.cpp(176): info: check kp.value() == (*itr).value() has passed  
ext.cpp(175): info: check itr != env.end() has passed  
ext.cpp(176): info: check kp.value() == (*itr).value() has passed  
ext.cpp(175): info: check itr != env.end() has passed  
ext.cpp(176): info: check kp.value() == (*itr).value() has passed  
ext.cpp(175): info: check itr != env.end() has passed  
ext.cpp(176): info: check kp.value() == (*itr).value() has passed  
ext.cpp(175): info: check itr != env.end() has passed  
ext.cpp(176): info: check kp.value() == (*itr).value() has passed  
ext.cpp(175): info: check itr != env.end() has passed  
ext.cpp(176): info: check kp.value() == (*itr).value() has passed  
ext.cpp(175): info: check itr != env.end() has passed  
ext.cpp(176): info: check kp.value() == (*itr).value() has passed  
ext.cpp(175): info: check itr != env.end() has passed  
ext.cpp(176): info: check kp.value() == (*itr).value() has passed  
ext.cpp(175): info: check itr != env.end() has passed  
ext.cpp(176): info: check kp.value() == (*itr).value() has passed  
ext.cpp(175): info: check itr != env.end() has passed  
ext.cpp(176): info: check kp.value() == (*itr).value() has passed  
ext.cpp(175): info: check itr != env.end() has passed  
ext.cpp(176): info: check kp.value() == (*itr).value() has passed  
ext.cpp(175): info: check itr != env.end() has passed  
ext.cpp(176): info: check kp.value() == (*itr).value() has passed  
ext.cpp(175): info: check itr != env.end() has passed  
ext.cpp(176): info: check kp.value() == (*itr).value() has passed  
ext.cpp(145): Leaving test case "test_env_exe"; testing time: 5962us  
ext.cpp(16): Leaving test suite "ext"; testing time: 48060us  
Leaving test module "process_v2_test"; testing time: 48412us  
  
*** 2 failures are detected in the test module "process_v2_test"  
  
EXIT STATUS: 201  
====== END OUTPUT ======  
  
    LD_LIBRARY_PATH="/home/openbsd/boost/bin.v2/libs/filesystem/build/clang-linux-13/debug/x86_64/visibility-global:/home/openbsd/boost/bin.v2/libs/process/build/clang-linux-13/debug/x86_64/boost.process.fs-boost/visibility-global:/home/openbsd/boost/bin.v2/libs/system/build/clang-linux-13/debug/x86_64/visibility-global:/home/openbsd/boost/bin.v2/libs/test/build/clang-linux-13/debug/x86_64/visibility-global:$LD_LIBRARY_PATH"  
export LD_LIBRARY_PATH  
  
     "../../../../bin.v2/libs/process/test/v2/ext.test/clang-linux-13/debug/x86_64/boost.process.fs-boost/visibility-global/ext" --log_level=all --catch_system_errors=no -- "../../../../bin.v2/libs/process/test/v2/clang-linux-13/debug/x86_64/boost.process.fs-boost/visibility-global/target" > "../../../../bin.v2/libs/process/test/v2/ext.test/clang-linux-13/debug/x86_64/boost.process.fs-boost/visibility-global/ext.output" 2>&1 < /dev/null  
    status=$?  
    echo >> "../../../../bin.v2/libs/process/test/v2/ext.test/clang-linux-13/debug/x86_64/boost.process.fs-boost/visibility-global/ext.output"  
    echo EXIT STATUS: $status >> "../../../../bin.v2/libs/process/test/v2/ext.test/clang-linux-13/debug/x86_64/boost.process.fs-boost/visibility-global/ext.output"  
    if test $status -eq 0 ; then  
        cp "../../../../bin.v2/libs/process/test/v2/ext.test/clang-linux-13/debug/x86_64/boost.process.fs-boost/visibility-global/ext.output" "../../../../bin.v2/libs/process/test/v2/ext.test/clang-linux-13/debug/x86_64/boost.process.fs-boost/visibility-global/ext.run" >/dev/null  
    fi  
    verbose=0  
    if test $status -ne 0 ; then  
        verbose=1  
    fi  
    if test $verbose -eq 1 ; then  
        echo ====== BEGIN OUTPUT ======  
        cat "../../../../bin.v2/libs/process/test/v2/ext.test/clang-linux-13/debug/x86_64/boost.process.fs-boost/visibility-global/ext.output"  
        echo ====== END OUTPUT ======  
    fi  
    exit $status  
  
...failed testing.capture-output ../../../../bin.v2/libs/process/test/v2/ext.test/clang-linux-13/debug/x86_64/boost.process.fs-boost/visibility-global/ext.run...  
testing.capture-output ../../../../bin.v2/libs/process/test/v2/pid.test/clang-linux-13/debug/x86_64/boost.process.fs-boost/visibility-global/pid.run  
**passed** ../../../../bin.v2/libs/process/test/v2/pid.test/clang-linux-13/debug/x86_64/boost.process.fs-boost/visibility-global/pid.test  
testing.capture-output ../../../../bin.v2/libs/process/test/v2/process.test/clang-linux-13/debug/x86_64/boost.process.fs-boost/visibility-global/process.run  
**passed** ../../../../bin.v2/libs/process/test/v2/process.test/clang-linux-13/debug/x86_64/boost.process.fs-boost/visibility-global/process.test  
  
...updated 21 targets...  
  
...skipped 2 targets...  
   <p../../../../bin.v2/libs/process/test/v2/ext.test/clang-linux-13/debug/x86_64/boost.process.fs-boost/visibility-global>ext.test  
   <p../../../../bin.v2/libs/process/test/v2/shell.test/clang-linux-13/debug/x86_64/boost.process.fs-boost/visibility-global>shell.test  
  
...failed updating 2 targets...  
   testing.capture-output ../../../../bin.v2/libs/process/test/v2/ext.test/clang-linux-13/debug/x86_64/boost.process.fs-boost/visibility-global/ext.run  
   testing.capture-output ../../../../bin.v2/libs/process/test/v2/shell.test/clang-linux-13/debug/x86_64/boost.process.fs-boost/visibility-global/shell.run  
openbsd$   
```

---
