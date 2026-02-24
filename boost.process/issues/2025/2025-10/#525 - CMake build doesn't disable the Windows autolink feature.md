# #525 - CMake build doesn't disable the Windows autolink feature [Open]

> Username: jtytgat  
> Created at: 2025-10-23 12:30:46 UTC  
> Updated at: 2025-10-23 14:09:27 UTC  
> Url: https://github.com/boostorg/process/issues/525  

When using the CMake build for the process component of Boost on Windows, the autolink feature is still active (using process v2 API), resulting in a link error:  
  
`LINK : fatal error LNK1104: cannot open file 'boost_process-vc143-mt-gd-x64-1_89.lib'`  
  
Using boost 1.89 release but I believe the problem is still present currently, i.e. autolink is active for CMake builds.  
  
When comparing the CMakeLists.txt of Boost process with e.g. filesystem or url we don't have a BOOST_&lt;COMPONENT&gt;_NO_LIB define in Boost process:  
  
url:  
--8<--  
function(boost_url_setup_properties target)  
    target_compile_features(${target} PUBLIC cxx_constexpr)  
    target_compile_definitions(${target} PUBLIC BOOST_URL_NO_LIB=1)  
--8<--  
  
filesystem:  
--8<--  
target_compile_definitions(boost_filesystem  
    PUBLIC  
        # NOTE:  
        # We deactivate autolinking, because cmake based builds don't need it  
        # and we don't implement name mangling for the library file anyway.  
        # Ususally the parent CMakeLists.txt file should already have globally defined BOOST_ALL_NO_LIB  
        BOOST_FILESYSTEM_NO_LIB  
        $&lt;$&lt;STREQUAL:$&lt;TARGET_PROPERTY:boost_filesystem,TYPE&gt;,SHARED_LIBRARY&gt;:BOOST_FILESYSTEM_DYN_LINK=1&gt;  
        $&lt;$&lt;STREQUAL:$&lt;TARGET_PROPERTY:boost_filesystem,TYPE&gt;,STATIC_LIBRARY&gt;:BOOST_FILESYSTEM_STATIC_LINK=1&gt;  
    PRIVATE  
        BOOST_FILESYSTEM_SOURCE  
)  
--8<--  
  
Changing in process' CMakeLists.txt from...  
  
--8<--  
target_compile_definitions(boost_process  
        PRIVATE BOOST_PROCESS_SOURCE=1  
)  
--8<--  
to...  
--8<--  
target_compile_definitions(boost_process  
        PUBLIC BOOST_PROCESS_NO_LIB  
        PRIVATE BOOST_PROCESS_SOURCE=1  
)  
--8<--  
  
...solves the problem as it disables the autolink features.

---

## Comment 1

> Username: klemens-morgenstern  
> Created at: 2025-10-23 14:09:27 UTC  
> Url: https://github.com/boostorg/process/issues/525#issuecomment-3437240913  

Can you submit a PR?
