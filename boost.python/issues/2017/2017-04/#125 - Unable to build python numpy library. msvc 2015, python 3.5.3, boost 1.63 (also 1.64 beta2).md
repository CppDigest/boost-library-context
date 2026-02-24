# #125 - Unable to build python numpy library. msvc 2015, python 3.5.3, boost 1.63 (also 1.64 beta2) [Closed]

> Username: macdew  
> Created at: 2017-04-13 17:02:48 UTC  
> Updated at: 2017-04-17 13:30:49 UTC  
> Closed at: 2017-04-17 13:30:49 UTC  
> Url: https://github.com/boostorg/python/issues/125  

I am unable to get the new python numpy library to build.  I'm seeing a .dll, but no corresponding .lib   
  
Snippets from build log to show that it found numpy and is building it:  
compile-c-c++ C:\pSource\Build\boost_1_63_0\build\x86\boost\bin.v2\libs\python\build\msvc-14.0\release\threading-multi\numpy\numpy.obj  
numpy.cpp  
c:\psource\thirdparty\components\general\python\python-3.5.3\bin\lib\site-packages\numpy-1.12.1-py3.5-win32.egg\numpy\core\include\numpy\npy_1_7_deprecated_api.h(12) : Warning Msg: Using deprecated NumPy API, disable it by #defining NPY_NO_DEPRECATED_API NPY_1_7_API_VERSION  
  
msvc.link.dll C:\pSource\Build\boost_1_63_0\build\x86\boost\bin.v2\libs\python\build\msvc-14.0\release\threading-multi\boost_numpy-vc140-mt-1_63.dll  
msvc.manifest.dll C:\pSource\Build\boost_1_63_0\build\x86\boost\bin.v2\libs\python\build\msvc-14.0\release\threading-multi\boost_numpy-vc140-mt-1_63.dll  
common.copy C:\pSource\thirdparty\Components\General\boost\1.63.0\lib32_msvc2015\boost_numpy-vc140-mt-1_63.lib  
The system cannot find the file specified.  
  
    copy /b "C:\pSource\Build\boost_1_63_0\build\x86\boost\bin.v2\libs\python\build\msvc-14.0\release\threading-multi\boost_numpy-vc140-mt-1_63.lib" + this-file-does-not-exist-A698EE7806899E69 "C:\pSource\thirdparty\Components\General\boost\1.63.0\lib32_msvc2015\boost_numpy-vc140-mt-1_63.lib"  
  
...failed common.copy C:\pSource\thirdparty\Components\General\boost\1.63.0\lib32_msvc2015\boost_numpy-vc140-mt-1_63.lib...  
msvc.link.dll C:\pSource\Build\boost_1_63_0\build\x86\boost\bin.v2\libs\python\build\msvc-14.0\release\threading-multi\boost_numpy3-vc140-mt-1_63.dll

---

## Comment 1

> Username: stefanseefeld  
> Created at: 2017-04-13 17:07:04 UTC  
> Url: https://github.com/boostorg/python/issues/125#issuecomment-293962553  

What version of Boost.Python are you using ? (There was a problem in the 1.63 release, which however has been fixed in the repo.

---

## Comment 2

> Username: macdew  
> Created at: 2017-04-13 19:29:19 UTC  
> Url: https://github.com/boostorg/python/issues/125#issuecomment-293999055  

Ah, I'm using the December source for 1.63.  I can have a go at the repo version and see if I have more luck with that.

---

## Comment 3

> Username: macdew  
> Created at: 2017-04-14 08:51:22 UTC  
> Updated at: 2017-04-14 10:18:53 UTC  
> Url: https://github.com/boostorg/python/issues/125#issuecomment-294118943  

Using 1.64 beta 2, the situation is improved but not entirely fixed.  Numpy library builds, 1 export is still not resolved:  
  
```  
error LNK2001: unresolved external symbol "class boost::python::numpy::dtype __cdecl boost::python::numpy::detail::get_float_dtype<32>(void)" (??$get_float_dtype@$0CA@@detail@numpy@python@boost@@YA?AVdtype@123@XZ)  
  
error LNK2019: unresolved external symbol "class boost::python::numpy::dtype __cdecl boost::python::numpy::detail::get_float_dtype<32>(void)" (??$get_float_dtype@$0CA@@detail@numpy@python@boost@@YA?AVdtype@123@XZ) referenced in function "public: static class boost::python::numpy::dtype __cdecl boost::python::numpy::detail::builtin_dtype<float,0>::get(void)" (?get@?$builtin_dtype@M$0A@@detail@numpy@python@boost@@SA?AVdtype@345@XZ)  
```

---

## Comment 4

> Username: macdew  
> Created at: 2017-04-14 11:35:11 UTC  
> Url: https://github.com/boostorg/python/issues/125#issuecomment-294139660  

Here is an example of the code that triggers this link error:  
  
```  
    // create a single-dimensional array of floats, zero-initialized  
    namespace np = boost::python::numpy;  
    Py_intptr_t shape[1] = { 24 };  
    np::dtype dt1 = np::dtype::get_builtin<float>();  
    np::ndarray results = np::zeros(1, shape, dt1);  
```

---

## Comment 5

> Username: stefanseefeld  
> Created at: 2017-04-14 17:16:50 UTC  
> Url: https://github.com/boostorg/python/issues/125#issuecomment-294194605  

I just pushed a commit to the 'develop' branch that should fix this: https://github.com/boostorg/python/commit/3844c4fc5fa44dcc2b74deb3b2cb1935175ef407  
Can you please report whether that works for you ? Unfortunately it's too late for the 1.64 release.

---

## Comment 6

> Username: macdew  
> Created at: 2017-04-17 11:06:12 UTC  
> Url: https://github.com/boostorg/python/issues/125#issuecomment-294454493  

Hi Stefan,  
  
Yes, that change allowed my code to build!  I can't test it running yet as I have more code to port to VS 2015 first, but I will let you know if I run into any further issues returning numpy arrays.

---

## Comment 7

> Username: stefanseefeld  
> Created at: 2017-04-17 13:30:49 UTC  
> Url: https://github.com/boostorg/python/issues/125#issuecomment-294485838  

Thanks, that's good to know. I'll make sure to merge this fix at the soonest possible time to master (i.e. if we get another bug-fix period to the 1.64 release, else for the next one).
