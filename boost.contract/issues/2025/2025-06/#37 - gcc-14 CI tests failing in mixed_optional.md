# #37 - gcc-14 CI tests failing in mixed_optional [Open]

> Username: jeking3  
> Created at: 2025-06-01 21:42:08 UTC  
> Updated at: 2025-06-01 21:42:08 UTC  
> Url: https://github.com/boostorg/contract/issues/37  

testing.capture-output bin.v2/libs/contract/test/result-mixed_optional.test/gcc-14/release/x86_64/cxxstd-11-iso/threading-multi/visibility-hidden/result-mixed_optional.run  
====== BEGIN OUTPUT ======  
d::static_inv  
d::inv  
c::static_inv  
c::inv  
b::static_inv  
b::inv  
a::static_inv  
a::inv  
d::f::pre  
c::f::pre  
b::f::pre  
a::f::pre  
d::f::old  
c::f::old  
b::f::old  
a::f::old  
a::f::body  
d::static_inv  
d::inv  
c::static_inv  
c::inv  
b::static_inv  
b::inv  
a::static_inv  
a::inv  
d::f::old  
d::f::post  
c::f::old  
c::f::post  
b::f::old  
b::f::post  
a::f::post  
libs/contract/test/result/mixed_optional.hpp(271): test 'ch_type::ctors() == ch_type::dtors() + ch_extras' ('9' == '3') failed in function 'int main()'  
d::static_inv  
d::inv  
c::static_inv  
c::inv  
b::static_inv  
b::inv  
d::f::pre  
c::f::pre  
b::f::pre  
d::f::old  
c::f::old  
b::f::old  
b::f::body  
d::static_inv  
d::inv  
c::static_inv  
c::inv  
b::static_inv  
b::inv  
d::f::old  
d::f::post  
c::f::old  
c::f::post  
b::f::post  
libs/contract/test/result/mixed_optional.hpp(316): test 'ch_type::ctors() == ch_type::dtors() + ch_extras' ('11' == '5') failed in function 'int main()'  
d::static_inv  
d::inv  
c::static_inv  
c::inv  
d::f::pre  
c::f::pre  
d::f::old  
c::f::old  
c::f::body  
d::static_inv  
d::inv  
c::static_inv  
c::inv  
d::f::old  
d::f::post  
c::f::post  
libs/contract/test/result/mixed_optional.hpp(353): test 'ch_type::ctors() == ch_type::dtors() + ch_extras' ('13' == '7') failed in function 'int main()'  
d::static_inv  
d::inv  
c::static_inv  
c::inv  
b::static_inv  
b::inv  
e::static_inv  
e::inv  
d::f::pre  
c::f::pre  
b::f::pre  
e::f::pre  
d::f::old  
c::f::old  
b::f::old  
e::f::old  
e::f::body  
d::static_inv  
d::inv  
c::static_inv  
c::inv  
b::static_inv  
b::inv  
e::static_inv  
e::inv  
d::f::old  
d::f::post  
c::f::old  
c::f::post  
b::f::old  
b::f::post  
e::f::post  
libs/contract/test/result/mixed_optional.hpp(406): test 'ch_type::ctors() == ch_type::dtors() + ch_extras' ('21' == '9') failed in function 'int main()'  
4 errors detected.  
  
EXIT STATUS: 4  
====== END OUTPUT ======  
  
    LD_LIBRARY_PATH="/home/runner/work/contract/boost-root/bin.v2/libs/atomic/build/gcc-14/release/x86_64/cxxstd-11-iso/threading-multi/visibility-hidden:/home/runner/work/contract/boost-root/bin.v2/libs/chrono/build/gcc-14/release/x86_64/cxxstd-11-iso/threading-multi/visibility-hidden:/home/runner/work/contract/boost-root/bin.v2/libs/container/build/gcc-14/release/x86_64/cxxstd-11-iso/threading-multi/visibility-hidden:/home/runner/work/contract/boost-root/bin.v2/libs/contract/build/gcc-14/release/x86_64/cxxstd-11-iso/threading-multi/visibility-hidden:/home/runner/work/contract/boost-root/bin.v2/libs/date_time/build/gcc-14/release/x86_64/cxxstd-11-iso/threading-multi/visibility-hidden:/home/runner/work/contract/boost-root/bin.v2/libs/iostreams/build/gcc-14/release/x86_64/cxxstd-11-iso/threading-multi/visibility-hidden:/home/runner/work/contract/boost-root/bin.v2/libs/random/build/gcc-14/release/x86_64/cxxstd-11-iso/threading-multi/visibility-hidden:/home/runner/work/contract/boost-root/bin.v2/libs/regex/build/gcc-14/release/x86_64/cxxstd-11-iso/threading-multi/visibility-hidden:/home/runner/work/contract/boost-root/bin.v2/libs/system/build/gcc-14/release/x86_64/cxxstd-11-iso/threading-multi/visibility-hidden:/home/runner/work/contract/boost-root/bin.v2/libs/thread/build/gcc-14/release/x86_64/cxxstd-11-iso/threading-multi/visibility-hidden:/usr/bin:/usr/lib:/usr/lib32:/usr/lib64:$LD_LIBRARY_PATH"  
export LD_LIBRARY_PATH  
  
     "bin.v2/libs/contract/test/result-mixed_optional.test/gcc-14/release/x86_64/cxxstd-11-iso/threading-multi/visibility-hidden/result-mixed_optional"   > "bin.v2/libs/contract/test/result-mixed_optional.test/gcc-14/release/x86_64/cxxstd-11-iso/threading-multi/visibility-hidden/result-mixed_optional.output" 2>&1 < /dev/null  
    status=$?  
    echo >> "bin.v2/libs/contract/test/result-mixed_optional.test/gcc-14/release/x86_64/cxxstd-11-iso/threading-multi/visibility-hidden/result-mixed_optional.output"  
    echo EXIT STATUS: $status >> "bin.v2/libs/contract/test/result-mixed_optional.test/gcc-14/release/x86_64/cxxstd-11-iso/threading-multi/visibility-hidden/result-mixed_optional.output"  
    if test $status -eq 0 ; then  
        cp "bin.v2/libs/contract/test/result-mixed_optional.test/gcc-14/release/x86_64/cxxstd-11-iso/threading-multi/visibility-hidden/result-mixed_optional.output" "bin.v2/libs/contract/test/result-mixed_optional.test/gcc-14/release/x86_64/cxxstd-11-iso/threading-multi/visibility-hidden/result-mixed_optional.run" >/dev/null  
    fi  
    verbose=0  
    if test $status -ne 0 ; then  
        verbose=1  
    fi  
    if test $verbose -eq 1 ; then  
        echo ====== BEGIN OUTPUT ======  
        cat "bin.v2/libs/contract/test/result-mixed_optional.test/gcc-14/release/x86_64/cxxstd-11-iso/threading-multi/visibility-hidden/result-mixed_optional.output"  
        echo ====== END OUTPUT ======  
    fi  
    exit $status  
  
...failed testing.capture-output bin.v2/libs/contract/test/result-mixed_optional.test/gcc-14/release/x86_64/cxxstd-11-iso/threading-multi/visibility-hidden/result-mixed_optional.run...
