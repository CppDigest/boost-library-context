# #80 - UBSAN issue in 1.68.0 [Closed]

> Username: jeking3  
> Created at: 2018-07-23 21:00:28 UTC  
> Updated at: 2018-11-24 16:53:21 UTC  
> Closed at: 2018-11-24 16:53:21 UTC  
> Url: https://github.com/boostorg/filesystem/issues/80  

Using the Ubuntu Bionic docker container from https://github.com/boostorg/boost/pull/184, this issue emerged:  
  
'''  
boost@a7d451ec0735:/boost/libs/filesystem/test$ UBSAN_OPTIONS=print_stacktrace=1 ../../../b2 toolset=gcc-7 cxxstd=03 cxxflags=-fno-omit-frame-pointer cxxflags=-fsanitize=undefined cxxflags=-fno-sanitize-recover=undefined linkflags=-fsanitize=undefined linkflags=-fno-sanitize-recover=undefined linkflags=-fuse-ld=gold variant=debug -q -a  
  
...  
  
testing.capture-output ../../../bin.v2/libs/filesystem/test/convenience_test.test/gcc-7/debug/cxxstd-03-iso/convenience_test.run  
====== BEGIN OUTPUT ======  
../../../libs/filesystem/src/operations.cpp:2346:28: runtime error: reference binding to null pointer of type 'struct error_code'  
    #0 0x7f11d4e00e67 in boost::filesystem::detail::directory_iterator_construct(boost::filesystem::directory_iterator&, boost::filesystem::path const&, boost::system::error_code*) ../../../libs/filesystem/src/operations.cpp:2346  
    #1 0x562749a68bba in boost::filesystem::directory_iterator::directory_iterator(boost::filesystem::path const&) ../../../boost/filesystem/operations.hpp:903  
    #2 0x562749a6a43b in boost::filesystem::recursive_directory_iterator::recursive_directory_iterator(boost::filesystem::path const&) ../../../boost/filesystem/operations.hpp:1158  
    #3 0x562749a64323 in cpp_main(int, char**) /boost/libs/filesystem/test/convenience_test.cpp:143  
    #4 0x562749a635ca in main ../../../boost/detail/lightweight_main.hpp:23  
    #5 0x7f11d357eb96 in __libc_start_main (/lib/x86_64-linux-gnu/libc.so.6+0x21b96)  
    #6 0x562749a634c9 in _start (/boost/bin.v2/libs/filesystem/test/convenience_test.test/gcc-7/debug/cxxstd-03-iso/convenience_test+0x114c9)  
  
  
EXIT STATUS: 1  
====== END OUTPUT ======  
  
    LD_LIBRARY_PATH="/boost/bin.v2/libs/filesystem/build/gcc-7/debug/cxxstd-03-iso:/boost/bin.v2/libs/system/build/gcc-7/debug/cxxstd-03-iso:/usr/bin:/usr/lib:/usr/lib32:/usr/lib64:$LD_LIBRARY_PATH"  
export LD_LIBRARY_PATH  
  
    status=0  
    if test $status -ne 0 ; then  
        echo Skipping test execution due to testing.execute=off  
        exit 0  
    fi  
     "../../../bin.v2/libs/filesystem/test/convenience_test.test/gcc-7/debug/cxxstd-03-iso/convenience_test"   > "../../../bin.v2/libs/filesystem/test/convenience_test.test/gcc-7/debug/cxxstd-03-iso/convenience_test.output" 2>&1 < /dev/null  
    status=$?  
    echo >> "../../../bin.v2/libs/filesystem/test/convenience_test.test/gcc-7/debug/cxxstd-03-iso/convenience_test.output"  
    echo EXIT STATUS: $status >> "../../../bin.v2/libs/filesystem/test/convenience_test.test/gcc-7/debug/cxxstd-03-iso/convenience_test.output"  
    if test $status -eq 0 ; then  
        cp "../../../bin.v2/libs/filesystem/test/convenience_test.test/gcc-7/debug/cxxstd-03-iso/convenience_test.output" "../../../bin.v2/libs/filesystem/test/convenience_test.test/gcc-7/debug/cxxstd-03-iso/convenience_test.run"  
    fi  
    verbose=0  
    if test $status -ne 0 ; then  
        verbose=1  
    fi  
    if test $verbose -eq 1 ; then  
        echo ====== BEGIN OUTPUT ======  
        cat "../../../bin.v2/libs/filesystem/test/convenience_test.test/gcc-7/debug/cxxstd-03-iso/convenience_test.output"  
        echo ====== END OUTPUT ======  
    fi  
    exit $status  
  
...failed testing.capture-output ../../../bin.v2/libs/filesystem/test/convenience_test.test/gcc-7/debug/cxxstd-03-iso/convenience_test.run...  
...failed updating 1 target...  
'''

---

## Comment 1

> Username: jeking3  
> Created at: 2018-07-23 21:11:07 UTC  
> Url: https://github.com/boostorg/filesystem/issues/80#issuecomment-407202467  

This seems to fix it:  
```  
boost@a7d451ec0735:/boost/libs/filesystem/test$ git diff HEAD  
diff --git a/include/boost/filesystem/operations.hpp b/include/boost/filesystem/operations.hpp  
index b0ea42a..ab76943 100644  
--- a/include/boost/filesystem/operations.hpp  
+++ b/include/boost/filesystem/operations.hpp  
@@ -900,7 +900,7 @@ namespace detail  
     // separate translation unit dll's, so forward to detail functions  
     explicit directory_iterator(const path& p)  
         : m_imp(new detail::dir_itr_imp)  
-          { detail::directory_iterator_construct(*this, p, 0); }  
+          { system::error_code ec; detail::directory_iterator_construct(*this, p, &ec); }  
  
     directory_iterator(const path& p, system::error_code& ec) BOOST_NOEXCEPT  
         : m_imp(new detail::dir_itr_imp)  
```  
  
I don't know if it's the right fix though.  Ignoring ec's result seems unwise.

---

## Comment 2

> Username: Lastique  
> Created at: 2018-11-24 16:53:21 UTC  
> Url: https://github.com/boostorg/filesystem/issues/80#issuecomment-441380734  

This should have been fixed by b8d259fb889e6946939bbb75bb63cb39a5f54929.
