# #126 - Compilation errors using C/C++ conformant preprocessor switch (/Zc:preprocessor) in MSVC [Closed]

> Username: paszklar  
> Created at: 2020-09-02 20:01:43 UTC  
> Updated at: 2020-09-04 13:50:04 UTC  
> Closed at: 2020-09-02 20:18:52 UTC  
> Url: https://github.com/boostorg/log/issues/126  

I'm using Visual Studio 2019 version 16.7.2 (cl.exe version 19.27.29111) and boost-log version 1.73.0 (from vcpkg). When compiling the following:  
  
    #include <boost/log/trivial.hpp>  
    int main()  
    {  
        BOOST_LOG_TRIVIAL(warning) << "Test warning";  
    }  
  
using the `/Zc:preprocessor` switch (more info [here](https://devblogs.microsoft.com/cppblog/announcing-full-support-for-a-c-c-conformant-preprocessor-in-msvc/)):  
  
    cl /EHsc /std:c++latest /Zc:preprocessor /I"../../microsoft/vcpkg/installed/x64-windows/include" /c main.cpp`  
  
I receive the following errors:  
  
    boost\log\sources\severity_logger.hpp(56,1): error C2061: syntax error: identifier 'BOOST_PP_REPEAT_1'  
    boost\log\sources\severity_logger.hpp(61): message : see reference to class template instantiation 'boost::log::v2_mt_nt6::sources::severity_logger<LevelT>' being compiled  
    boost\log\sources\severity_logger.hpp(56,1): error C2061: syntax error: identifier 'BOOST_PP_REPEAT_1'  
    boost\log\sources\severity_logger.hpp(56,1): error C2061: syntax error: identifier 'BOOST_PP_REPEAT_1'  
    boost\log\sources\severity_logger.hpp(56,1): error C2061: syntax error: identifier 'BOOST_PP_REPEAT_1'  
    boost\log\sources\severity_logger.hpp(56,1): error C2061: syntax error: identifier 'BOOST_PP_REPEAT_1'  
    boost\log\sources\severity_logger.hpp(56,1): error C2061: syntax error: identifier 'BOOST_PP_REPEAT_1'  
    boost\log\sources\severity_logger.hpp(56,1): error C2061: syntax error: identifier 'BOOST_PP_REPEAT_1'  
    boost\log\sources\severity_logger.hpp(56,1): error C2061: syntax error: identifier 'BOOST_PP_REPEAT_1'  
    boost\log\sources\severity_logger.hpp(56,1): error C2061: syntax error: identifier 'BOOST_PP_REPEAT_1'  
    boost\log\sources\severity_logger.hpp(56,1): error C2061: syntax error: identifier 'BOOST_PP_REPEAT_1'  
    boost\log\sources\severity_logger.hpp(56,1): error C2061: syntax error: identifier 'BOOST_PP_REPEAT_1'  
    boost\log\sources\severity_logger.hpp(56,1): error C2061: syntax error: identifier 'BOOST_PP_REPEAT_1'  
    boost\log\sources\severity_logger.hpp(56,1): error C2061: syntax error: identifier 'BOOST_PP_REPEAT_1'  
    boost\log\sources\severity_logger.hpp(56,1): error C2061: syntax error: identifier 'BOOST_PP_REPEAT_1'  
    boost\log\sources\severity_logger.hpp(56,1): error C3861: 'BOOST_PP_ENUM_PARAMS_M': identifier not found  
    boost\log\sources\severity_logger.hpp(56,1): message : This diagnostic occurred in the compiler generated function 'boost::log::v2_mt_nt6::sources::severity_logger<LevelT>::severity_logger(const T0 &,boost::log::aux::enable_if_named_parameters<T0,boost::log::v2_mt_nt6::aux::sfinae_dummy>::type)'  
    boost\log\sources\severity_logger.hpp(56,1): error C3861: 'arg': identifier not found  
    boost\log\sources\severity_logger.hpp(56,1): message : This diagnostic occurred in the compiler generated function 'boost::log::v2_mt_nt6::sources::severity_logger<LevelT>::severity_logger(const T0 &,boost::log::aux::enable_if_named_parameters<T0,boost::log::v2_mt_nt6::aux::sfinae_dummy>::type)'  
    boost\log\sources\severity_logger.hpp(78,1): error C2061: syntax error: identifier 'BOOST_PP_REPEAT_1'  
    boost\log\sources\severity_logger.hpp(83): message : see reference to class template instantiation 'boost::log::v2_mt_nt6::sources::severity_logger_mt<LevelT>' being compiled  
    boost\log\sources\severity_logger.hpp(78,1): error C2061: syntax error: identifier 'BOOST_PP_REPEAT_1'  
    boost\log\sources\severity_logger.hpp(78,1): error C2061: syntax error: identifier 'BOOST_PP_REPEAT_1'  
    boost\log\sources\severity_logger.hpp(78,1): error C2061: syntax error: identifier 'BOOST_PP_REPEAT_1'  
    boost\log\sources\severity_logger.hpp(78,1): error C2061: syntax error: identifier 'BOOST_PP_REPEAT_1'  
    boost\log\sources\severity_logger.hpp(78,1): error C2061: syntax error: identifier 'BOOST_PP_REPEAT_1'  
    boost\log\sources\severity_logger.hpp(78,1): error C2061: syntax error: identifier 'BOOST_PP_REPEAT_1'  
    boost\log\sources\severity_logger.hpp(78,1): error C2061: syntax error: identifier 'BOOST_PP_REPEAT_1'  
    boost\log\sources\severity_logger.hpp(78,1): error C2061: syntax error: identifier 'BOOST_PP_REPEAT_1'  
    boost\log\sources\severity_logger.hpp(78,1): error C2061: syntax error: identifier 'BOOST_PP_REPEAT_1'  
    boost\log\sources\severity_logger.hpp(78,1): error C2061: syntax error: identifier 'BOOST_PP_REPEAT_1'  
    boost\log\sources\severity_logger.hpp(78,1): error C2061: syntax error: identifier 'BOOST_PP_REPEAT_1'  
    boost\log\sources\severity_logger.hpp(78,1): error C2061: syntax error: identifier 'BOOST_PP_REPEAT_1'  
    boost\log\sources\severity_logger.hpp(78,1): error C2061: syntax error: identifier 'BOOST_PP_REPEAT_1'  
    boost\log\sources\severity_logger.hpp(78,1): error C3861: 'BOOST_PP_ENUM_PARAMS_M': identifier not found  
    boost\log\sources\severity_logger.hpp(78,1): message : This diagnostic occurred in the compiler generated function 'boost::log::v2_mt_nt6::sources::severity_logger_mt<LevelT>::severity_logger_mt(const T0 &,boost::log::aux::enable_if_named_parameters<T0,boost::log::v2_mt_nt6::aux::sfinae_dummy>::type)'  
    boost\log\sources\severity_logger.hpp(78,1): error C3861: 'arg': identifier not found  
    boost\log\sources\severity_logger.hpp(78,1): message : This diagnostic occurred in the compiler generated function 'boost::log::v2_mt_nt6::sources::severity_logger_mt<LevelT>::severity_logger_mt(const T0 &,boost::log::aux::enable_if_named_parameters<T0,boost::log::v2_mt_nt6::aux::sfinae_dummy>::type)'  
    boost\log\sources\severity_logger.hpp(104,1): error C2061: syntax error: identifier 'BOOST_PP_REPEAT_1'  
    boost\log\sources\severity_logger.hpp(109): message : see reference to class template instantiation 'boost::log::v2_mt_nt6::sources::wseverity_logger<LevelT>' being compiled  
    boost\log\sources\severity_logger.hpp(104,1): error C2061: syntax error: identifier 'BOOST_PP_REPEAT_1'  
    boost\log\sources\severity_logger.hpp(104,1): error C2061: syntax error: identifier 'BOOST_PP_REPEAT_1'  
    boost\log\sources\severity_logger.hpp(104,1): error C2061: syntax error: identifier 'BOOST_PP_REPEAT_1'  
    boost\log\sources\severity_logger.hpp(104,1): error C2061: syntax error: identifier 'BOOST_PP_REPEAT_1'  
    boost\log\sources\severity_logger.hpp(104,1): error C2061: syntax error: identifier 'BOOST_PP_REPEAT_1'  
    boost\log\sources\severity_logger.hpp(104,1): error C2061: syntax error: identifier 'BOOST_PP_REPEAT_1'  
    boost\log\sources\severity_logger.hpp(104,1): error C2061: syntax error: identifier 'BOOST_PP_REPEAT_1'  
    boost\log\sources\severity_logger.hpp(104,1): error C2061: syntax error: identifier 'BOOST_PP_REPEAT_1'  
    boost\log\sources\severity_logger.hpp(104,1): error C2061: syntax error: identifier 'BOOST_PP_REPEAT_1'  
    boost\log\sources\severity_logger.hpp(104,1): error C2061: syntax error: identifier 'BOOST_PP_REPEAT_1'  
    boost\log\sources\severity_logger.hpp(104,1): error C2061: syntax error: identifier 'BOOST_PP_REPEAT_1'  
    boost\log\sources\severity_logger.hpp(104,1): error C2061: syntax error: identifier 'BOOST_PP_REPEAT_1'  
    boost\log\sources\severity_logger.hpp(104,1): error C2061: syntax error: identifier 'BOOST_PP_REPEAT_1'  
    boost\log\sources\severity_logger.hpp(104,1): error C3861: 'BOOST_PP_ENUM_PARAMS_M': identifier not found  
    boost\log\sources\severity_logger.hpp(104,1): message : This diagnostic occurred in the compiler generated function 'boost::log::v2_mt_nt6::sources::wseverity_logger<LevelT>::wseverity_logger(const T0 &,boost::log::aux::enable_if_named_parameters<T0,boost::log::v2_mt_nt6::aux::sfinae_dummy>::type)'  
    boost\log\sources\severity_logger.hpp(104,1): error C3861: 'arg': identifier not found  
    boost\log\sources\severity_logger.hpp(104,1): message : This diagnostic occurred in the compiler generated function 'boost::log::v2_mt_nt6::sources::wseverity_logger<LevelT>::wseverity_logger(const T0 &,boost::log::aux::enable_if_named_parameters<T0,boost::log::v2_mt_nt6::aux::sfinae_dummy>::type)'  
    boost\log\sources\severity_logger.hpp(126,1): error C2061: syntax error: identifier 'BOOST_PP_REPEAT_1'  
    boost\log\sources\severity_logger.hpp(131): message : see reference to class template instantiation 'boost::log::v2_mt_nt6::sources::wseverity_logger_mt<LevelT>' being compiled  
    boost\log\sources\severity_logger.hpp(126,1): error C2061: syntax error: identifier 'BOOST_PP_REPEAT_1'  
    boost\log\sources\severity_logger.hpp(126,1): error C2061: syntax error: identifier 'BOOST_PP_REPEAT_1'  
    boost\log\sources\severity_logger.hpp(126,1): error C2061: syntax error: identifier 'BOOST_PP_REPEAT_1'  
    boost\log\sources\severity_logger.hpp(126,1): error C2061: syntax error: identifier 'BOOST_PP_REPEAT_1'  
    boost\log\sources\severity_logger.hpp(126,1): error C2061: syntax error: identifier 'BOOST_PP_REPEAT_1'  
    boost\log\sources\severity_logger.hpp(126,1): error C2061: syntax error: identifier 'BOOST_PP_REPEAT_1'  
    boost\log\sources\severity_logger.hpp(126,1): error C2061: syntax error: identifier 'BOOST_PP_REPEAT_1'  
    boost\log\sources\severity_logger.hpp(126,1): error C2061: syntax error: identifier 'BOOST_PP_REPEAT_1'  
    boost\log\sources\severity_logger.hpp(126,1): error C2061: syntax error: identifier 'BOOST_PP_REPEAT_1'  
    boost\log\sources\severity_logger.hpp(126,1): error C2061: syntax error: identifier 'BOOST_PP_REPEAT_1'  
    boost\log\sources\severity_logger.hpp(126,1): error C2061: syntax error: identifier 'BOOST_PP_REPEAT_1'  
    boost\log\sources\severity_logger.hpp(126,1): error C2061: syntax error: identifier 'BOOST_PP_REPEAT_1'  
    boost\log\sources\severity_logger.hpp(126,1): error C2061: syntax error: identifier 'BOOST_PP_REPEAT_1'  
    boost\log\sources\severity_logger.hpp(126,1): error C3861: 'BOOST_PP_ENUM_PARAMS_M': identifier not found  
    boost\log\sources\severity_logger.hpp(126,1): message : This diagnostic occurred in the compiler generated function 'boost::log::v2_mt_nt6::sources::wseverity_logger_mt<LevelT>::wseverity_logger_mt(const T0 &,boost::log::aux::enable_if_named_parameters<T0,boost::log::v2_mt_nt6::aux::sfinae_dummy>::type)'  
    boost\log\sources\severity_logger.hpp(126,1): error C3861: 'arg': identifier not found  
    boost\log\sources\severity_logger.hpp(126,1): message : This diagnostic occurred in the compiler generated function 'boost::log::v2_mt_nt6::sources::wseverity_logger_mt<LevelT>::wseverity_logger_mt(const T0 &,boost::log::aux::enable_if_named_parameters<T0,boost::log::v2_mt_nt6::aux::sfinae_dummy>::type)'  
    boost\log\sources\severity_logger.hpp(78,1): error C2912: explicit specialization 'boost::log::v2_mt_nt6::sources::severity_logger_mt<boost::log::v2_mt_nt6::trivial::severity_level>::severity_logger_mt(void)' is not a specialization of a function template  
    boost\log\sources\severity_logger.hpp(78,1): error C2912: explicit specialization 'boost::log::v2_mt_nt6::sources::severity_logger_mt<boost::log::v2_mt_nt6::trivial::severity_level>::severity_logger_mt(void)' is not a specialization of a function template  
    boost\log\sources\severity_logger.hpp(78,1): error C2912: explicit specialization 'boost::log::v2_mt_nt6::sources::severity_logger_mt<boost::log::v2_mt_nt6::trivial::severity_level>::severity_logger_mt(void)' is not a specialization of a function template  
    boost\log\sources\severity_logger.hpp(78,1): error C2912: explicit specialization 'boost::log::v2_mt_nt6::sources::severity_logger_mt<boost::log::v2_mt_nt6::trivial::severity_level>::severity_logger_mt(void)' is not a specialization of a function template  
    boost\log\sources\severity_logger.hpp(78,1): error C2912: explicit specialization 'boost::log::v2_mt_nt6::sources::severity_logger_mt<boost::log::v2_mt_nt6::trivial::severity_level>::severity_logger_mt(void)' is not a specialization of a function template  
    boost\log\sources\severity_logger.hpp(78,1): error C2912: explicit specialization 'boost::log::v2_mt_nt6::sources::severity_logger_mt<boost::log::v2_mt_nt6::trivial::severity_level>::severity_logger_mt(void)' is not a specialization of a function template  
    boost\log\sources\severity_logger.hpp(78,1): error C2912: explicit specialization 'boost::log::v2_mt_nt6::sources::severity_logger_mt<boost::log::v2_mt_nt6::trivial::severity_level>::severity_logger_mt(void)' is not a specialization of a function template  
    boost\log\sources\severity_logger.hpp(78,1): error C2912: explicit specialization 'boost::log::v2_mt_nt6::sources::severity_logger_mt<boost::log::v2_mt_nt6::trivial::severity_level>::severity_logger_mt(void)' is not a specialization of a function template  
    boost\log\sources\severity_logger.hpp(78,1): error C2912: explicit specialization 'boost::log::v2_mt_nt6::sources::severity_logger_mt<boost::log::v2_mt_nt6::trivial::severity_level>::severity_logger_mt(void)' is not a specialization of a function template  
    boost\log\sources\severity_logger.hpp(78,1): error C2912: explicit specialization 'boost::log::v2_mt_nt6::sources::severity_logger_mt<boost::log::v2_mt_nt6::trivial::severity_level>::severity_logger_mt(void)' is not a specialization of a function template  
    boost\log\sources\severity_logger.hpp(78,1): error C2912: explicit specialization 'boost::log::v2_mt_nt6::sources::severity_logger_mt<boost::log::v2_mt_nt6::trivial::severity_level>::severity_logger_mt(void)' is not a specialization of a function template  
    boost\log\sources\severity_logger.hpp(78,1): error C2912: explicit specialization 'boost::log::v2_mt_nt6::sources::severity_logger_mt<boost::log::v2_mt_nt6::trivial::severity_level>::severity_logger_mt(void)' is not a specialization of a function template  
    boost\log\sources\severity_logger.hpp(78,1): error C2912: explicit specialization 'boost::log::v2_mt_nt6::sources::severity_logger_mt<boost::log::v2_mt_nt6::trivial::severity_level>::severity_logger_mt(void)' is not a specialization of a function template  
    boost\log\sources\severity_logger.hpp(78,1): error C2912: explicit specialization 'boost::log::v2_mt_nt6::sources::severity_logger_mt<boost::log::v2_mt_nt6::trivial::severity_level>::severity_logger_mt(void)' is not a specialization of a function template  
  
The problem persists even if `#define BOOST_PP_VARIADICS 0` or `#define BOOST_PP_VARIADICS 1` is added before including the boost-log header file.

---

## Comment 1

> Username: Lastique  
> Created at: 2020-09-02 20:18:52 UTC  
> Url: https://github.com/boostorg/log/issues/126#issuecomment-685973644  

I don't think [Boost.Preprocessor](https://github.com/boostorg/preprocessor) supports the new MSVC preprocessor (yet). You can report the problem there.

---

## Comment 2

> Username: paszklar  
> Created at: 2020-09-03 07:01:40 UTC  
> Url: https://github.com/boostorg/log/issues/126#issuecomment-686297214  

According to [this](https://github.com/boostorg/preprocessor/issues/25#issuecomment-631741229) /Zc:preprocessor is supported by Boost.Preprocessor. I'm not sure what Boost.Log is doing to trip it up.

---

## Comment 3

> Username: Lastique  
> Created at: 2020-09-03 09:23:58 UTC  
> Url: https://github.com/boostorg/log/issues/126#issuecomment-686367353  

I still think it's a Boost.Preprocessor issue, given that the code compiles with other compilers and MSVC with legacy PP. I won't be able to debug this myself for a few days.

---

## Comment 4

> Username: Lastique  
> Created at: 2020-09-03 09:26:28 UTC  
> Url: https://github.com/boostorg/log/issues/126#issuecomment-686368626  

Please, double check that the compiler doesn't give any errors saying that a header is not found. Also check that your Boost installation is intact.

---

## Comment 5

> Username: paszklar  
> Created at: 2020-09-03 12:29:14 UTC  
> Url: https://github.com/boostorg/log/issues/126#issuecomment-686455175  

I've just tried reinstalling boost from vcpkg which didn't change anything. I also downloaded boost 1.74 and build it manually. Pointing the compiler to it didn't help either. I don't get any missing header files errors in any of the cases. I'll try poking around the macros in parameter_tools.hpp (which seem to be causing the issues) and see if I can get an MRE. I'll update if I find anything.

---

## Comment 6

> Username: paszklar  
> Created at: 2020-09-04 13:50:04 UTC  
> Url: https://github.com/boostorg/log/issues/126#issuecomment-687158000  

As far as I was able to gather reentering BOOST_PP_REPEAT fails with `/Zc:preprocessor`. I've reported this [issue](https://github.com/boostorg/preprocessor/issues/33#issue-693182622) with Boost.Preprocessor.
