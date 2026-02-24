# #176 - std::auto_ptr is deprecated warning at boost/python/detail/is_auto_ptr.hpp:17 [Closed]

> Username: jeking3  
> Created at: 2017-11-13 16:51:51 UTC  
> Updated at: 2017-11-13 19:16:15 UTC  
> Closed at: 2017-11-13 19:16:15 UTC  
> Url: https://github.com/boostorg/python/issues/176  

Brand new clone of master on Ubuntu Trusty with gcc-7.2 installed, this warning appeared in the build:  
  
Command: ./b2 address-model=64 link=shared threading=multi toolset=gcc-7 variant=release -q -j5  
Output:  
```  
In file included from ./boost/python/detail/is_xxx.hpp:8:0,  
                 from ./boost/python/detail/is_auto_ptr.hpp:9,  
                 from ./boost/python/detail/copy_ctor_mutates_rhs.hpp:8,  
                 from ./boost/python/detail/value_arg.hpp:7,  
                 from ./boost/python/object/forward.hpp:10,  
                 from ./boost/python/object/pointer_holder.hpp:16,  
                 from ./boost/python/to_python_indirect.hpp:10,  
                 from ./boost/python/converter/arg_to_python.hpp:10,  
                 from ./boost/python/call.hpp:15,  
                 from ./boost/python/object_core.hpp:14,  
                 from ./boost/python/object/function.hpp:12,  
                 from ./boost/python/object/function_doc_signature.hpp:8,  
                 from libs/python/src/object/function_doc_signature.cpp:10:  
./boost/python/detail/is_auto_ptr.hpp:17:40: warning: â€˜template<class> class std::auto_ptrâ€™ is deprecated [-Wdeprecated-declarations]  
 BOOST_PYTHON_IS_XXX_DEF(auto_ptr, std::auto_ptr, 1)  
                                        ^  
./boost/detail/is_xxx.hpp:20:4: note: in definition of macro â€˜BOOST_DETAIL_IS_XXX_DEFâ€™  
    qualified_name< BOOST_PP_ENUM_PARAMS_Z(1, nargs, T) >        \  
    ^~~~~~~~~~~~~~  
./boost/python/detail/is_auto_ptr.hpp:17:1: note: in expansion of macro â€˜BOOST_PYTHON_IS_XXX_DEFâ€™  
 BOOST_PYTHON_IS_XXX_DEF(auto_ptr, std::auto_ptr, 1)  
 ^~~~~~~~~~~~~~~~~~~~~~~  
```

---

## Comment 1

> Username: stefanseefeld  
> Created at: 2017-11-13 17:04:32 UTC  
> Url: https://github.com/boostorg/python/issues/176#issuecomment-343986528  

That's fully expected and not a bug. Boost.Python needs to support `std::auto_ptr` for backward-compatibility. We may need to mask the `is_auto_ptr<>` trait eventually, on platforms that don't have `std::auto_ptr` any longer, but until we get to that point (and have the machinery to conditionally enable / disable support for std::auto_ptr, I'm inclined to keep things the way they are.

---

## Comment 2

> Username: jeking3  
> Created at: 2017-11-13 17:15:36 UTC  
> Url: https://github.com/boostorg/python/issues/176#issuecomment-343989900  

I would respectfully request that you reconsider squelching this compiler warning where it is being emitted through the use of toolset specific build options in your project.  Builds should not have warnings.

---

## Comment 3

> Username: jeking3  
> Created at: 2017-11-13 17:23:08 UTC  
> Url: https://github.com/boostorg/python/issues/176#issuecomment-343992261  

Actually, it would be more of a pragma thing than a build option thing, but you get the idea... if the library requires it, but it is deprecated, the library could squelch the warning through compiler pragmas for controlling diagnostics to make a cleaner build.

---

## Comment 4

> Username: stefanseefeld  
> Created at: 2017-11-13 17:31:52 UTC  
> Url: https://github.com/boostorg/python/issues/176#issuecomment-343995157  

Fair enough. I'll see what I can do.

---

## Comment 5

> Username: jeking3  
> Created at: 2017-11-13 18:00:45 UTC  
> Url: https://github.com/boostorg/python/issues/176#issuecomment-344004169  

Thanks, if you want me to submit a PR for this issue let me know.

---

## Comment 6

> Username: stefanseefeld  
> Created at: 2017-11-13 18:09:20 UTC  
> Url: https://github.com/boostorg/python/issues/176#issuecomment-344006681  

Sure, that would be great. I'm not quite sure yet what the desired behaviour should be, though. Are you suggesting to merely disable the warning, or change actual code / behaviour ?  
We can discuss this with the PR in any case. Thanks !

---

## Comment 7

> Username: jeking3  
> Created at: 2017-11-13 18:22:30 UTC  
> Url: https://github.com/boostorg/python/issues/176#issuecomment-344010665  

I would say that given Boost.Python has a requirement to detect/support std::auto_ptr, and given newer compilers issue a warning on use of std:auto_ptr, we should disable those warnings around those specific cases if we can in order to achieve a warning-free build.  I often build my code with warnings as errors and -Wextra or /W4 enabled because often times these point out subtle bugs, and once a project can build warning free, keeping it warning-free is a good practice.  Boost in general has too many build warnings for my taste and I would like to help push us all towards having warning-free builds and turning on "warning-as-error" semantics for our own boost builds.  
  
Let me see what I can do and submit a PR for this.

---

## Comment 8

> Username: jeking3  
> Created at: 2017-11-13 18:48:23 UTC  
> Url: https://github.com/boostorg/python/issues/176#issuecomment-344018247  

Also seeing this.  Treat it separately?  
```  
In file included from libs/python/src/str.cpp:5:0:  
./boost/python/extract.hpp: In member function â€˜long int boost::python::detail::str_base::count(boost::python::api::object_operators<boost::python::api::object>::object_cref) constâ€™:  
./boost/python/extract.hpp:185:11: warning: â€˜*((void*)&<anonymous> +24)â€™ may be used uninitialized in this function [-Wmaybe-uninitialized]  
           );  
           ^  
./boost/python/extract.hpp: In member function â€˜long int boost::python::detail::str_base::count(boost::python::api::object_operators<boost::python::api::object>::object_cref, boost::python::api::object_operators<boost::python::api::object>::object_cref) constâ€™:  
./boost/python/extract.hpp:185:11: warning: â€˜*((void*)&<anonymous> +24)â€™ may be used uninitialized in this function [-Wmaybe-uninitialized]  
           );  
           ^  
./boost/python/extract.hpp: In member function â€˜long int boost::python::detail::str_base::count(boost::python::api::object_operators<boost::python::api::object>::object_cref, boost::python::api::object_operators<boost::python::api::object>::object_cref, boost::python::api::object_operators<boost::python::api::object>::object_cref) constâ€™:  
./boost/python/extract.hpp:185:11: warning: â€˜*((void*)&<anonymous> +24)â€™ may be used uninitialized in this function [-Wmaybe-uninitialized]  
           );  
           ^  
```

---

## Comment 9

> Username: stefanseefeld  
> Created at: 2017-11-13 18:54:47 UTC  
> Url: https://github.com/boostorg/python/issues/176#issuecomment-344020092  

Feel free to include it into the same PR, but make it a separate commit. Thanks.

---

## Comment 10

> Username: jeking3  
> Created at: 2017-11-13 19:16:15 UTC  
> Url: https://github.com/boostorg/python/issues/176#issuecomment-344026501  

It looks like if one builds with BOOST_NO_AUTO_PTR defined this goes away.  That seems like an acceptable solution to me.  As such, I am going to close this out.
