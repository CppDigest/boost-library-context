# #272 - Build failure with msvc + standalone [Closed]

> Username: Mike-Devel  
> Created at: 2020-09-05 09:44:27 UTC  
> Updated at: 2020-09-06 16:46:49 UTC  
> Closed at: 2020-09-06 14:37:57 UTC  
> Url: https://github.com/boostorg/json/issues/272  

Getting various build errors when trying to build json with VS2019 and Standalone configuration.  
  
Steps to reproduce:  
- Clone repository into a folder outside of boost (in my case boost-json)  
- From powershell:  
  - `mkdir __build__`  
  - `cd __build__`  
  - `cmake .. -DBOOST_JSON_STANDALONE=True`  
  - `cmake --build .`  
  
Full error log on my machine:  
  
```  
PS F:\xRepos\boost-json\__build__> cmake .. -DBOOST_JSON_STANDALONE=True  
-- Building for: Visual Studio 16 2019  
-- Selecting Windows SDK version 10.0.18362.0 to target Windows 10.0.19041.  
-- The CXX compiler identification is MSVC 19.27.29111.0  
-- Check for working CXX compiler: C:/Program Files (x86)/xDev/Microsoft Visual Studio/2019/Community/VC/Tools/MSVC/14.27.29110/bin/Hostx64/x64/cl.exe  
-- Check for working CXX compiler: C:/Program Files (x86)/xDev/Microsoft Visual Studio/2019/Community/VC/Tools/MSVC/14.27.29110/bin/Hostx64/x64/cl.exe - works  
-- Detecting CXX compiler ABI info  
-- Detecting CXX compiler ABI info - done  
-- Detecting CXX compile features  
-- Detecting CXX compile features - done  
-- Configuring done  
-- Generating done  
-- Build files have been written to: F:/xRepos/boost-json/__build__  
PS F:\xRepos\boost-json\__build__> cmake --build .  
Microsoft (R) Build Engine version 16.7.0+b89cb5fde for .NET Framework  
Copyright (C) Microsoft Corporation. All rights reserved.  
  
  Checking File Globs  
  Checking Build System  
  Building Custom Rule F:/xRepos/boost-json/CMakeLists.txt  
  Using triplet "x64-windows" from "C:\xDev\vcpkg\installed\x64-windows\"  
  src.cpp  
F:\xRepos\boost-json\include\boost/json/pilfer.hpp(106,26): error C2872: 'detail': ambiguous symbol [F:\xRepos\boost-json\__build__\boost_json.vcxproj]  
F:\xRepos\boost-json\include\boost/json/pilfer.hpp(79,11): message : could be 'boost::json::detail' [F:\xRepos\boost-json\__build__\boost_json.vcxproj]  
F:\xRepos\boost-json\include\boost/json/detail/config.hpp(217,11): message : or       'boost::json::standalone::detail' [F:\xRepos\boost-json\__build__\boost_json.vcxproj]  
F:\xRepos\boost-json\include\boost/json/pilfer.hpp(100): error C2872: 'detail': ambiguous symbol [F:\xRepos\boost-json\__build__\boost_json.vcxproj]  
F:\xRepos\boost-json\include\boost/json/pilfer.hpp(79,11): message : could be 'boost::json::detail' [F:\xRepos\boost-json\__build__\boost_json.vcxproj]  
F:\xRepos\boost-json\include\boost/json/detail/config.hpp(217,11): message : or       'boost::json::standalone::detail' [F:\xRepos\boost-json\__build__\boost_json.vcxproj]  
F:\xRepos\boost-json\include\boost/json/pilfer.hpp(110): message : see reference to class template instantiation 'boost::json::is_pilfer_constructible<T>' being compiled [F:\xRepos\boost-json\__build__\boost_json.vcxproj]  
F:\xRepos\boost-json\include\boost/json/pilfer.hpp(149,1): error C2872: 'detail': ambiguous symbol [F:\xRepos\boost-json\__build__\boost_json.vcxproj]  
F:\xRepos\boost-json\include\boost/json/pilfer.hpp(79,11): message : could be 'boost::json::detail' [F:\xRepos\boost-json\__build__\boost_json.vcxproj]  
F:\xRepos\boost-json\include\boost/json/detail/config.hpp(217,11): message : or       'boost::json::standalone::detail' [F:\xRepos\boost-json\__build__\boost_json.vcxproj]  
F:\xRepos\boost-json\include\boost/json/string.hpp(2935,1): error C2872: 'detail': ambiguous symbol [F:\xRepos\boost-json\__build__\boost_json.vcxproj]  
F:\xRepos\boost-json\include\boost/json/pilfer.hpp(79,11): message : could be 'boost::json::detail' [F:\xRepos\boost-json\__build__\boost_json.vcxproj]  
F:\xRepos\boost-json\include\boost/json/detail/value.hpp(19,11): message : or       'boost::json::standalone::detail' [F:\xRepos\boost-json\__build__\boost_json.vcxproj]  
F:\xRepos\boost-json\include\boost/json/string.hpp(2935,39): error C2039: 'digest': is not a member of 'boost::json::detail' [F:\xRepos\boost-json\__build__\boost_json.vcxproj]  
F:\xRepos\boost-json\include\boost/json/pilfer.hpp(79): message : see declaration of 'boost::json::detail' [F:\xRepos\boost-json\__build__\boost_json.vcxproj]  
F:\xRepos\boost-json\include\boost/json/string.hpp(2935,1): error C3861: 'digest': identifier not found [F:\xRepos\boost-json\__build__\boost_json.vcxproj]  
F:\xRepos\boost-json\include\boost/json/impl/array.ipp(211,23): error C2672: 'pilfer': no matching overloaded function found [F:\xRepos\boost-json\__build__\boost_json.vcxproj]  
F:\xRepos\boost-json\include\boost/json/impl/array.ipp(211,33): error C2893: Failed to specialize function template 'std::conditional<std::is_nothrow_constructible<std::remove_reference<_Ty>::type,boost::json::pilfered<std::remove_reference<_Ty>::type>>::value&  
&!std::is_nothrow_constructible<std::remove_reference<_Ty>::type,boost::json::detail::not_pilfered<std::remove_reference<_Ty>::type>>::value,boost::json::pilfered<std::remove_reference<_Ty>::type>,std::remove_reference<_Ty>::type&&>::type boost::json::pilfer(T  
&&) noexcept' [F:\xRepos\boost-json\__build__\boost_json.vcxproj]  
F:\xRepos\boost-json\include\boost/json/pilfer.hpp(142): message : see declaration of 'boost::json::pilfer' [F:\xRepos\boost-json\__build__\boost_json.vcxproj]  
F:\xRepos\boost-json\include\boost/json/impl/array.ipp(211,33): message : With the following template arguments: [F:\xRepos\boost-json\__build__\boost_json.vcxproj]  
F:\xRepos\boost-json\include\boost/json/impl/array.ipp(211,33): message : 'T=boost::json::standalone::array &' [F:\xRepos\boost-json\__build__\boost_json.vcxproj]  
F:\xRepos\boost-json\include\boost/json/impl/array.ipp(221,23): error C2672: 'pilfer': no matching overloaded function found [F:\xRepos\boost-json\__build__\boost_json.vcxproj]  
F:\xRepos\boost-json\include\boost/json/impl/array.ipp(221,33): error C2893: Failed to specialize function template 'std::conditional<std::is_nothrow_constructible<std::remove_reference<_Ty>::type,boost::json::pilfered<std::remove_reference<_Ty>::type>>::value&  
&!std::is_nothrow_constructible<std::remove_reference<_Ty>::type,boost::json::detail::not_pilfered<std::remove_reference<_Ty>::type>>::value,boost::json::pilfered<std::remove_reference<_Ty>::type>,std::remove_reference<_Ty>::type&&>::type boost::json::pilfer(T  
&&) noexcept' [F:\xRepos\boost-json\__build__\boost_json.vcxproj]  
F:\xRepos\boost-json\include\boost/json/pilfer.hpp(142): message : see declaration of 'boost::json::pilfer' [F:\xRepos\boost-json\__build__\boost_json.vcxproj]  
F:\xRepos\boost-json\include\boost/json/impl/array.ipp(221,33): message : With the following template arguments: [F:\xRepos\boost-json\__build__\boost_json.vcxproj]  
F:\xRepos\boost-json\include\boost/json/impl/array.ipp(221,33): message : 'T=boost::json::standalone::array &' [F:\xRepos\boost-json\__build__\boost_json.vcxproj]  
F:\xRepos\boost-json\include\boost/json/impl/array.ipp(232,23): error C2672: 'pilfer': no matching overloaded function found [F:\xRepos\boost-json\__build__\boost_json.vcxproj]  
F:\xRepos\boost-json\include\boost/json/impl/array.ipp(232,33): error C2893: Failed to specialize function template 'std::conditional<std::is_nothrow_constructible<std::remove_reference<_Ty>::type,boost::json::pilfered<std::remove_reference<_Ty>::type>>::value&  
&!std::is_nothrow_constructible<std::remove_reference<_Ty>::type,boost::json::detail::not_pilfered<std::remove_reference<_Ty>::type>>::value,boost::json::pilfered<std::remove_reference<_Ty>::type>,std::remove_reference<_Ty>::type&&>::type boost::json::pilfer(T  
&&) noexcept' [F:\xRepos\boost-json\__build__\boost_json.vcxproj]  
F:\xRepos\boost-json\include\boost/json/pilfer.hpp(142): message : see declaration of 'boost::json::pilfer' [F:\xRepos\boost-json\__build__\boost_json.vcxproj]  
F:\xRepos\boost-json\include\boost/json/impl/array.ipp(232,33): message : With the following template arguments: [F:\xRepos\boost-json\__build__\boost_json.vcxproj]  
F:\xRepos\boost-json\include\boost/json/impl/array.ipp(232,33): message : 'T=boost::json::standalone::array &' [F:\xRepos\boost-json\__build__\boost_json.vcxproj]  
F:\xRepos\boost-json\include\boost/json/impl/array.ipp(465,23): error C2672: 'pilfer': no matching overloaded function found [F:\xRepos\boost-json\__build__\boost_json.vcxproj]  
F:\xRepos\boost-json\include\boost/json/impl/array.ipp(465,35): error C2893: Failed to specialize function template 'std::conditional<std::is_nothrow_constructible<std::remove_reference<_Ty>::type,boost::json::pilfered<std::remove_reference<_Ty>::type>>::value&  
&!std::is_nothrow_constructible<std::remove_reference<_Ty>::type,boost::json::detail::not_pilfered<std::remove_reference<_Ty>::type>>::value,boost::json::pilfered<std::remove_reference<_Ty>::type>,std::remove_reference<_Ty>::type&&>::type boost::json::pilfer(T  
&&) noexcept' [F:\xRepos\boost-json\__build__\boost_json.vcxproj]  
F:\xRepos\boost-json\include\boost/json/pilfer.hpp(142): message : see declaration of 'boost::json::pilfer' [F:\xRepos\boost-json\__build__\boost_json.vcxproj]  
F:\xRepos\boost-json\include\boost/json/impl/array.ipp(465,35): message : With the following template arguments: [F:\xRepos\boost-json\__build__\boost_json.vcxproj]  
F:\xRepos\boost-json\include\boost/json/impl/array.ipp(465,35): message : 'T=boost::json::standalone::array &' [F:\xRepos\boost-json\__build__\boost_json.vcxproj]  
F:\xRepos\boost-json\include\boost/json/impl/array.ipp(467,25): error C2672: 'pilfer': no matching overloaded function found [F:\xRepos\boost-json\__build__\boost_json.vcxproj]  
F:\xRepos\boost-json\include\boost/json/impl/array.ipp(467,37): error C2893: Failed to specialize function template 'std::conditional<std::is_nothrow_constructible<std::remove_reference<_Ty>::type,boost::json::pilfered<std::remove_reference<_Ty>::type>>::value&  
&!std::is_nothrow_constructible<std::remove_reference<_Ty>::type,boost::json::detail::not_pilfered<std::remove_reference<_Ty>::type>>::value,boost::json::pilfered<std::remove_reference<_Ty>::type>,std::remove_reference<_Ty>::type&&>::type boost::json::pilfer(T  
&&) noexcept' [F:\xRepos\boost-json\__build__\boost_json.vcxproj]  
F:\xRepos\boost-json\include\boost/json/pilfer.hpp(142): message : see declaration of 'boost::json::pilfer' [F:\xRepos\boost-json\__build__\boost_json.vcxproj]  
F:\xRepos\boost-json\include\boost/json/impl/array.ipp(467,37): message : With the following template arguments: [F:\xRepos\boost-json\__build__\boost_json.vcxproj]  
F:\xRepos\boost-json\include\boost/json/impl/array.ipp(467,37): message : 'T=boost::json::standalone::array &' [F:\xRepos\boost-json\__build__\boost_json.vcxproj]  
F:\xRepos\boost-json\include\boost/json/impl/object.ipp(220,24): error C2672: 'pilfer': no matching overloaded function found [F:\xRepos\boost-json\__build__\boost_json.vcxproj]  
F:\xRepos\boost-json\include\boost/json/impl/object.ipp(220,34): error C2893: Failed to specialize function template 'std::conditional<std::is_nothrow_constructible<std::remove_reference<_Ty>::type,boost::json::pilfered<std::remove_reference<_Ty>::type>>::value  
&&!std::is_nothrow_constructible<std::remove_reference<_Ty>::type,boost::json::detail::not_pilfered<std::remove_reference<_Ty>::type>>::value,boost::json::pilfered<std::remove_reference<_Ty>::type>,std::remove_reference<_Ty>::type&&>::type boost::json::pilfer(T  
 &&) noexcept' [F:\xRepos\boost-json\__build__\boost_json.vcxproj]  
F:\xRepos\boost-json\include\boost/json/pilfer.hpp(142): message : see declaration of 'boost::json::pilfer' [F:\xRepos\boost-json\__build__\boost_json.vcxproj]  
F:\xRepos\boost-json\include\boost/json/impl/object.ipp(220,34): message : With the following template arguments: [F:\xRepos\boost-json\__build__\boost_json.vcxproj]  
F:\xRepos\boost-json\include\boost/json/impl/object.ipp(220,34): message : 'T=boost::json::standalone::object &' [F:\xRepos\boost-json\__build__\boost_json.vcxproj]  
F:\xRepos\boost-json\include\boost/json/impl/object.ipp(232,24): error C2672: 'pilfer': no matching overloaded function found [F:\xRepos\boost-json\__build__\boost_json.vcxproj]  
F:\xRepos\boost-json\include\boost/json/impl/object.ipp(232,34): error C2893: Failed to specialize function template 'std::conditional<std::is_nothrow_constructible<std::remove_reference<_Ty>::type,boost::json::pilfered<std::remove_reference<_Ty>::type>>::value  
&&!std::is_nothrow_constructible<std::remove_reference<_Ty>::type,boost::json::detail::not_pilfered<std::remove_reference<_Ty>::type>>::value,boost::json::pilfered<std::remove_reference<_Ty>::type>,std::remove_reference<_Ty>::type&&>::type boost::json::pilfer(T  
 &&) noexcept' [F:\xRepos\boost-json\__build__\boost_json.vcxproj]  
F:\xRepos\boost-json\include\boost/json/pilfer.hpp(142): message : see declaration of 'boost::json::pilfer' [F:\xRepos\boost-json\__build__\boost_json.vcxproj]  
F:\xRepos\boost-json\include\boost/json/impl/object.ipp(232,34): message : With the following template arguments: [F:\xRepos\boost-json\__build__\boost_json.vcxproj]  
F:\xRepos\boost-json\include\boost/json/impl/object.ipp(232,34): message : 'T=boost::json::standalone::object &' [F:\xRepos\boost-json\__build__\boost_json.vcxproj]  
F:\xRepos\boost-json\include\boost/json/impl/object.ipp(244,24): error C2672: 'pilfer': no matching overloaded function found [F:\xRepos\boost-json\__build__\boost_json.vcxproj]  
F:\xRepos\boost-json\include\boost/json/impl/object.ipp(244,34): error C2893: Failed to specialize function template 'std::conditional<std::is_nothrow_constructible<std::remove_reference<_Ty>::type,boost::json::pilfered<std::remove_reference<_Ty>::type>>::value  
&&!std::is_nothrow_constructible<std::remove_reference<_Ty>::type,boost::json::detail::not_pilfered<std::remove_reference<_Ty>::type>>::value,boost::json::pilfered<std::remove_reference<_Ty>::type>,std::remove_reference<_Ty>::type&&>::type boost::json::pilfer(T  
 &&) noexcept' [F:\xRepos\boost-json\__build__\boost_json.vcxproj]  
F:\xRepos\boost-json\include\boost/json/pilfer.hpp(142): message : see declaration of 'boost::json::pilfer' [F:\xRepos\boost-json\__build__\boost_json.vcxproj]  
F:\xRepos\boost-json\include\boost/json/impl/object.ipp(244,34): message : With the following template arguments: [F:\xRepos\boost-json\__build__\boost_json.vcxproj]  
F:\xRepos\boost-json\include\boost/json/impl/object.ipp(244,34): message : 'T=boost::json::standalone::object &' [F:\xRepos\boost-json\__build__\boost_json.vcxproj]  
F:\xRepos\boost-json\include\boost/json/impl/object.ipp(372,26): error C2672: 'pilfer': no matching overloaded function found [F:\xRepos\boost-json\__build__\boost_json.vcxproj]  
F:\xRepos\boost-json\include\boost/json/impl/object.ipp(372,38): error C2893: Failed to specialize function template 'std::conditional<std::is_nothrow_constructible<std::remove_reference<_Ty>::type,boost::json::pilfered<std::remove_reference<_Ty>::type>>::value  
&&!std::is_nothrow_constructible<std::remove_reference<_Ty>::type,boost::json::detail::not_pilfered<std::remove_reference<_Ty>::type>>::value,boost::json::pilfered<std::remove_reference<_Ty>::type>,std::remove_reference<_Ty>::type&&>::type boost::json::pilfer(T  
 &&) noexcept' [F:\xRepos\boost-json\__build__\boost_json.vcxproj]  
F:\xRepos\boost-json\include\boost/json/pilfer.hpp(142): message : see declaration of 'boost::json::pilfer' [F:\xRepos\boost-json\__build__\boost_json.vcxproj]  
F:\xRepos\boost-json\include\boost/json/impl/object.ipp(372,38): message : With the following template arguments: [F:\xRepos\boost-json\__build__\boost_json.vcxproj]  
F:\xRepos\boost-json\include\boost/json/impl/object.ipp(372,38): message : 'T=boost::json::standalone::object &' [F:\xRepos\boost-json\__build__\boost_json.vcxproj]  
F:\xRepos\boost-json\include\boost/json/impl/object.ipp(374,24): error C2672: 'pilfer': no matching overloaded function found [F:\xRepos\boost-json\__build__\boost_json.vcxproj]  
F:\xRepos\boost-json\include\boost/json/impl/object.ipp(374,36): error C2893: Failed to specialize function template 'std::conditional<std::is_nothrow_constructible<std::remove_reference<_Ty>::type,boost::json::pilfered<std::remove_reference<_Ty>::type>>::value  
&&!std::is_nothrow_constructible<std::remove_reference<_Ty>::type,boost::json::detail::not_pilfered<std::remove_reference<_Ty>::type>>::value,boost::json::pilfered<std::remove_reference<_Ty>::type>,std::remove_reference<_Ty>::type&&>::type boost::json::pilfer(T  
 &&) noexcept' [F:\xRepos\boost-json\__build__\boost_json.vcxproj]  
F:\xRepos\boost-json\include\boost/json/pilfer.hpp(142): message : see declaration of 'boost::json::pilfer' [F:\xRepos\boost-json\__build__\boost_json.vcxproj]  
F:\xRepos\boost-json\include\boost/json/impl/object.ipp(374,36): message : With the following template arguments: [F:\xRepos\boost-json\__build__\boost_json.vcxproj]  
F:\xRepos\boost-json\include\boost/json/impl/object.ipp(374,36): message : 'T=boost::json::standalone::object &' [F:\xRepos\boost-json\__build__\boost_json.vcxproj]  
F:\xRepos\boost-json\include\boost/json/impl/string.ipp(285,24): error C2672: 'pilfer': no matching overloaded function found [F:\xRepos\boost-json\__build__\boost_json.vcxproj]  
F:\xRepos\boost-json\include\boost/json/impl/string.ipp(285,36): error C2893: Failed to specialize function template 'std::conditional<std::is_nothrow_constructible<std::remove_reference<_Ty>::type,boost::json::pilfered<std::remove_reference<_Ty>::type>>::value  
&&!std::is_nothrow_constructible<std::remove_reference<_Ty>::type,boost::json::detail::not_pilfered<std::remove_reference<_Ty>::type>>::value,boost::json::pilfered<std::remove_reference<_Ty>::type>,std::remove_reference<_Ty>::type&&>::type boost::json::pilfer(T  
 &&) noexcept' [F:\xRepos\boost-json\__build__\boost_json.vcxproj]  
F:\xRepos\boost-json\include\boost/json/pilfer.hpp(142): message : see declaration of 'boost::json::pilfer' [F:\xRepos\boost-json\__build__\boost_json.vcxproj]  
F:\xRepos\boost-json\include\boost/json/impl/string.ipp(285,36): message : With the following template arguments: [F:\xRepos\boost-json\__build__\boost_json.vcxproj]  
F:\xRepos\boost-json\include\boost/json/impl/string.ipp(285,36): message : 'T=boost::json::standalone::string &' [F:\xRepos\boost-json\__build__\boost_json.vcxproj]  
F:\xRepos\boost-json\include\boost/json/impl/string.ipp(287,26): error C2672: 'pilfer': no matching overloaded function found [F:\xRepos\boost-json\__build__\boost_json.vcxproj]  
F:\xRepos\boost-json\include\boost/json/impl/string.ipp(287,38): error C2893: Failed to specialize function template 'std::conditional<std::is_nothrow_constructible<std::remove_reference<_Ty>::type,boost::json::pilfered<std::remove_reference<_Ty>::type>>::value  
&&!std::is_nothrow_constructible<std::remove_reference<_Ty>::type,boost::json::detail::not_pilfered<std::remove_reference<_Ty>::type>>::value,boost::json::pilfered<std::remove_reference<_Ty>::type>,std::remove_reference<_Ty>::type&&>::type boost::json::pilfer(T  
 &&) noexcept' [F:\xRepos\boost-json\__build__\boost_json.vcxproj]  
F:\xRepos\boost-json\include\boost/json/pilfer.hpp(142): message : see declaration of 'boost::json::pilfer' [F:\xRepos\boost-json\__build__\boost_json.vcxproj]  
F:\xRepos\boost-json\include\boost/json/impl/string.ipp(287,38): message : With the following template arguments: [F:\xRepos\boost-json\__build__\boost_json.vcxproj]  
F:\xRepos\boost-json\include\boost/json/impl/string.ipp(287,38): message : 'T=boost::json::standalone::string &' [F:\xRepos\boost-json\__build__\boost_json.vcxproj]  
F:\xRepos\boost-json\include\boost/json/impl/value.ipp(317,29): error C2672: 'pilfer': no matching overloaded function found [F:\xRepos\boost-json\__build__\boost_json.vcxproj]  
F:\xRepos\boost-json\include\boost/json/impl/value.ipp(317,41): error C2893: Failed to specialize function template 'std::conditional<std::is_nothrow_constructible<std::remove_reference<_Ty>::type,boost::json::pilfered<std::remove_reference<_Ty>::type>>::value&  
&!std::is_nothrow_constructible<std::remove_reference<_Ty>::type,boost::json::detail::not_pilfered<std::remove_reference<_Ty>::type>>::value,boost::json::pilfered<std::remove_reference<_Ty>::type>,std::remove_reference<_Ty>::type&&>::type boost::json::pilfer(T  
&&) noexcept' [F:\xRepos\boost-json\__build__\boost_json.vcxproj]  
F:\xRepos\boost-json\include\boost/json/pilfer.hpp(142): message : see declaration of 'boost::json::pilfer' [F:\xRepos\boost-json\__build__\boost_json.vcxproj]  
F:\xRepos\boost-json\include\boost/json/impl/value.ipp(317,41): message : With the following template arguments: [F:\xRepos\boost-json\__build__\boost_json.vcxproj]  
F:\xRepos\boost-json\include\boost/json/impl/value.ipp(317,41): message : 'T=boost::json::standalone::value &' [F:\xRepos\boost-json\__build__\boost_json.vcxproj]  
F:\xRepos\boost-json\include\boost/json/impl/value.ipp(319,27): error C2672: 'pilfer': no matching overloaded function found [F:\xRepos\boost-json\__build__\boost_json.vcxproj]  
F:\xRepos\boost-json\include\boost/json/impl/value.ipp(319,39): error C2893: Failed to specialize function template 'std::conditional<std::is_nothrow_constructible<std::remove_reference<_Ty>::type,boost::json::pilfered<std::remove_reference<_Ty>::type>>::value&  
&!std::is_nothrow_constructible<std::remove_reference<_Ty>::type,boost::json::detail::not_pilfered<std::remove_reference<_Ty>::type>>::value,boost::json::pilfered<std::remove_reference<_Ty>::type>,std::remove_reference<_Ty>::type&&>::type boost::json::pilfer(T  
&&) noexcept' [F:\xRepos\boost-json\__build__\boost_json.vcxproj]  
F:\xRepos\boost-json\include\boost/json/pilfer.hpp(142): message : see declaration of 'boost::json::pilfer' [F:\xRepos\boost-json\__build__\boost_json.vcxproj]  
F:\xRepos\boost-json\include\boost/json/impl/value.ipp(319,39): message : With the following template arguments: [F:\xRepos\boost-json\__build__\boost_json.vcxproj]  
F:\xRepos\boost-json\include\boost/json/impl/value.ipp(319,39): message : 'T=boost::json::standalone::value &' [F:\xRepos\boost-json\__build__\boost_json.vcxproj]  
F:\xRepos\boost-json\include\boost/json/impl/value_stack.ipp(329,9): error C2672: 'pilfer': no matching overloaded function found [F:\xRepos\boost-json\__build__\boost_json.vcxproj]  
F:\xRepos\boost-json\include\boost/json/impl/value_stack.ipp(329,18): error C2893: Failed to specialize function template 'std::conditional<std::is_nothrow_constructible<std::remove_reference<_Ty>::type,boost::json::pilfered<std::remove_reference<_Ty>::type>>::  
value&&!std::is_nothrow_constructible<std::remove_reference<_Ty>::type,boost::json::detail::not_pilfered<std::remove_reference<_Ty>::type>>::value,boost::json::pilfered<std::remove_reference<_Ty>::type>,std::remove_reference<_Ty>::type&&>::type boost::json::pil  
fer(T &&) noexcept' [F:\xRepos\boost-json\__build__\boost_json.vcxproj]  
F:\xRepos\boost-json\include\boost/json/pilfer.hpp(142): message : see declaration of 'boost::json::pilfer' [F:\xRepos\boost-json\__build__\boost_json.vcxproj]  
F:\xRepos\boost-json\include\boost/json/impl/value_stack.ipp(329,18): message : With the following template arguments: [F:\xRepos\boost-json\__build__\boost_json.vcxproj]  
F:\xRepos\boost-json\include\boost/json/impl/value_stack.ipp(329,18): message : 'T=boost::json::standalone::storage_ptr &' [F:\xRepos\boost-json\__build__\boost_json.vcxproj]  
F:\xRepos\boost-json\include\boost/json/impl/value_stack.ipp(349,12): error C2672: 'pilfer': no matching overloaded function found [F:\xRepos\boost-json\__build__\boost_json.vcxproj]  
F:\xRepos\boost-json\include\boost/json/impl/value_stack.ipp(349,34): error C2893: Failed to specialize function template 'std::conditional<std::is_nothrow_constructible<std::remove_reference<_Ty>::type,boost::json::pilfered<std::remove_reference<_Ty>::type>>::  
value&&!std::is_nothrow_constructible<std::remove_reference<_Ty>::type,boost::json::detail::not_pilfered<std::remove_reference<_Ty>::type>>::value,boost::json::pilfered<std::remove_reference<_Ty>::type>,std::remove_reference<_Ty>::type&&>::type boost::json::pil  
fer(T &&) noexcept' [F:\xRepos\boost-json\__build__\boost_json.vcxproj]  
F:\xRepos\boost-json\include\boost/json/pilfer.hpp(142): message : see declaration of 'boost::json::pilfer' [F:\xRepos\boost-json\__build__\boost_json.vcxproj]  
F:\xRepos\boost-json\include\boost/json/impl/value_stack.ipp(349,34): message : With the following template arguments: [F:\xRepos\boost-json\__build__\boost_json.vcxproj]  
F:\xRepos\boost-json\include\boost/json/impl/value_stack.ipp(349,34): message : 'T=boost::json::standalone::value &' [F:\xRepos\boost-json\__build__\boost_json.vcxproj]  
F:\xRepos\boost-json\include\boost/json/detail/impl/except.ipp(22,1): error C2872: 'detail': ambiguous symbol [F:\xRepos\boost-json\__build__\boost_json.vcxproj]  
F:\xRepos\boost-json\include\boost/json/pilfer.hpp(79,11): message : could be 'boost::json::detail' [F:\xRepos\boost-json\__build__\boost_json.vcxproj]  
F:\xRepos\boost-json\include\boost/json/detail/impl/digest.ipp(14,11): message : or       'boost::json::standalone::detail' [F:\xRepos\boost-json\__build__\boost_json.vcxproj]  
F:\xRepos\boost-json\include\boost/json/detail/impl/except.ipp(22,19): error C2039: 'source_location': is not a member of 'boost::json::detail' [F:\xRepos\boost-json\__build__\boost_json.vcxproj]  
F:\xRepos\boost-json\include\boost/json/pilfer.hpp(79): message : see declaration of 'boost::json::detail' [F:\xRepos\boost-json\__build__\boost_json.vcxproj]  
F:\xRepos\boost-json\include\boost/json/detail/impl/except.ipp(22,1): error C2061: syntax error: identifier 'source_location' [F:\xRepos\boost-json\__build__\boost_json.vcxproj]  
F:\xRepos\boost-json\include\boost/json/detail/impl/except.ipp(36,12): error C2672: 'boost::throw_exception': no matching overloaded function found [F:\xRepos\boost-json\__build__\boost_json.vcxproj]  
F:\xRepos\boost-json\include\boost/json/detail/impl/except.ipp(37,30): error C2780: 'void boost::throw_exception(E)': expects 1 arguments - 2 provided [F:\xRepos\boost-json\__build__\boost_json.vcxproj]  
F:\xRepos\boost-json\include\boost/json/detail/impl/except.ipp(21): message : see declaration of 'boost::throw_exception' [F:\xRepos\boost-json\__build__\boost_json.vcxproj]  
F:\xRepos\boost-json\include\boost/json/detail/impl/except.ipp(45,12): error C2672: 'boost::throw_exception': no matching overloaded function found [F:\xRepos\boost-json\__build__\boost_json.vcxproj]  
F:\xRepos\boost-json\include\boost/json/detail/impl/except.ipp(46,37): error C2780: 'void boost::throw_exception(E)': expects 1 arguments - 2 provided [F:\xRepos\boost-json\__build__\boost_json.vcxproj]  
F:\xRepos\boost-json\include\boost/json/detail/impl/except.ipp(21): message : see declaration of 'boost::throw_exception' [F:\xRepos\boost-json\__build__\boost_json.vcxproj]  
F:\xRepos\boost-json\include\boost/json/detail/impl/except.ipp(54,12): error C2672: 'boost::throw_exception': no matching overloaded function found [F:\xRepos\boost-json\__build__\boost_json.vcxproj]  
F:\xRepos\boost-json\include\boost/json/detail/impl/except.ipp(55,41): error C2780: 'void boost::throw_exception(E)': expects 1 arguments - 2 provided [F:\xRepos\boost-json\__build__\boost_json.vcxproj]  
F:\xRepos\boost-json\include\boost/json/detail/impl/except.ipp(21): message : see declaration of 'boost::throw_exception' [F:\xRepos\boost-json\__build__\boost_json.vcxproj]  
F:\xRepos\boost-json\include\boost/json/detail/impl/except.ipp(62,12): error C2672: 'boost::throw_exception': no matching overloaded function found [F:\xRepos\boost-json\__build__\boost_json.vcxproj]  
F:\xRepos\boost-json\include\boost/json/detail/impl/except.ipp(64,33): error C2780: 'void boost::throw_exception(E)': expects 1 arguments - 2 provided [F:\xRepos\boost-json\__build__\boost_json.vcxproj]  
F:\xRepos\boost-json\include\boost/json/detail/impl/except.ipp(21): message : see declaration of 'boost::throw_exception' [F:\xRepos\boost-json\__build__\boost_json.vcxproj]  
F:\xRepos\boost-json\include\boost/json/detail/impl/except.ipp(72,12): error C2672: 'boost::throw_exception': no matching overloaded function found [F:\xRepos\boost-json\__build__\boost_json.vcxproj]  
F:\xRepos\boost-json\include\boost/json/detail/impl/except.ipp(73,30): error C2780: 'void boost::throw_exception(E)': expects 1 arguments - 2 provided [F:\xRepos\boost-json\__build__\boost_json.vcxproj]  
F:\xRepos\boost-json\include\boost/json/detail/impl/except.ipp(21): message : see declaration of 'boost::throw_exception' [F:\xRepos\boost-json\__build__\boost_json.vcxproj]  
  Building Custom Rule F:/xRepos/boost-json/test/CMakeLists.txt  
  Using triplet "x64-windows" from "C:\xDev\vcpkg\installed\x64-windows\"  
  limits.cpp  
F:\xRepos\boost-json\include\boost/json/pilfer.hpp(106,26): error C2872: 'detail': ambiguous symbol [F:\xRepos\boost-json\__build__\test\limits.vcxproj]  
F:\xRepos\boost-json\include\boost/json/pilfer.hpp(79,11): message : could be 'boost::json::detail' [F:\xRepos\boost-json\__build__\test\limits.vcxproj]  
F:\xRepos\boost-json\include\boost/json/detail/config.hpp(217,11): message : or       'boost::json::standalone::detail' [F:\xRepos\boost-json\__build__\test\limits.vcxproj]  
F:\xRepos\boost-json\include\boost/json/pilfer.hpp(100): error C2872: 'detail': ambiguous symbol [F:\xRepos\boost-json\__build__\test\limits.vcxproj]  
F:\xRepos\boost-json\include\boost/json/pilfer.hpp(79,11): message : could be 'boost::json::detail' [F:\xRepos\boost-json\__build__\test\limits.vcxproj]  
F:\xRepos\boost-json\include\boost/json/detail/config.hpp(217,11): message : or       'boost::json::standalone::detail' [F:\xRepos\boost-json\__build__\test\limits.vcxproj]  
F:\xRepos\boost-json\include\boost/json/pilfer.hpp(110): message : see reference to class template instantiation 'boost::json::is_pilfer_constructible<T>' being compiled [F:\xRepos\boost-json\__build__\test\limits.vcxproj]  
F:\xRepos\boost-json\include\boost/json/pilfer.hpp(149,1): error C2872: 'detail': ambiguous symbol [F:\xRepos\boost-json\__build__\test\limits.vcxproj]  
F:\xRepos\boost-json\include\boost/json/pilfer.hpp(79,11): message : could be 'boost::json::detail' [F:\xRepos\boost-json\__build__\test\limits.vcxproj]  
F:\xRepos\boost-json\include\boost/json/detail/config.hpp(217,11): message : or       'boost::json::standalone::detail' [F:\xRepos\boost-json\__build__\test\limits.vcxproj]  
F:\xRepos\boost-json\include\boost/json/detail/impl/except.ipp(22,1): error C2872: 'detail': ambiguous symbol [F:\xRepos\boost-json\__build__\test\limits.vcxproj]  
F:\xRepos\boost-json\include\boost/json/pilfer.hpp(79,11): message : could be 'boost::json::detail' [F:\xRepos\boost-json\__build__\test\limits.vcxproj]  
F:\xRepos\boost-json\include\boost/json/detail/except.hpp(21,11): message : or       'boost::json::standalone::detail' [F:\xRepos\boost-json\__build__\test\limits.vcxproj]  
F:\xRepos\boost-json\include\boost/json/detail/impl/except.ipp(22,19): error C2039: 'source_location': is not a member of 'boost::json::detail' [F:\xRepos\boost-json\__build__\test\limits.vcxproj]  
F:\xRepos\boost-json\include\boost/json/pilfer.hpp(79): message : see declaration of 'boost::json::detail' [F:\xRepos\boost-json\__build__\test\limits.vcxproj]  
F:\xRepos\boost-json\include\boost/json/detail/impl/except.ipp(22,1): error C2061: syntax error: identifier 'source_location' [F:\xRepos\boost-json\__build__\test\limits.vcxproj]  
F:\xRepos\boost-json\include\boost/json/detail/impl/except.ipp(36,12): error C2672: 'boost::throw_exception': no matching overloaded function found [F:\xRepos\boost-json\__build__\test\limits.vcxproj]  
F:\xRepos\boost-json\include\boost/json/detail/impl/except.ipp(37,30): error C2780: 'void boost::throw_exception(E)': expects 1 arguments - 2 provided [F:\xRepos\boost-json\__build__\test\limits.vcxproj]  
F:\xRepos\boost-json\include\boost/json/detail/impl/except.ipp(21): message : see declaration of 'boost::throw_exception' [F:\xRepos\boost-json\__build__\test\limits.vcxproj]  
F:\xRepos\boost-json\include\boost/json/detail/impl/except.ipp(45,12): error C2672: 'boost::throw_exception': no matching overloaded function found [F:\xRepos\boost-json\__build__\test\limits.vcxproj]  
F:\xRepos\boost-json\include\boost/json/detail/impl/except.ipp(46,37): error C2780: 'void boost::throw_exception(E)': expects 1 arguments - 2 provided [F:\xRepos\boost-json\__build__\test\limits.vcxproj]  
F:\xRepos\boost-json\include\boost/json/detail/impl/except.ipp(21): message : see declaration of 'boost::throw_exception' [F:\xRepos\boost-json\__build__\test\limits.vcxproj]  
F:\xRepos\boost-json\include\boost/json/detail/impl/except.ipp(54,12): error C2672: 'boost::throw_exception': no matching overloaded function found [F:\xRepos\boost-json\__build__\test\limits.vcxproj]  
F:\xRepos\boost-json\include\boost/json/detail/impl/except.ipp(55,41): error C2780: 'void boost::throw_exception(E)': expects 1 arguments - 2 provided [F:\xRepos\boost-json\__build__\test\limits.vcxproj]  
F:\xRepos\boost-json\include\boost/json/detail/impl/except.ipp(21): message : see declaration of 'boost::throw_exception' [F:\xRepos\boost-json\__build__\test\limits.vcxproj]  
F:\xRepos\boost-json\include\boost/json/detail/impl/except.ipp(62,12): error C2672: 'boost::throw_exception': no matching overloaded function found [F:\xRepos\boost-json\__build__\test\limits.vcxproj]  
F:\xRepos\boost-json\include\boost/json/detail/impl/except.ipp(64,33): error C2780: 'void boost::throw_exception(E)': expects 1 arguments - 2 provided [F:\xRepos\boost-json\__build__\test\limits.vcxproj]  
F:\xRepos\boost-json\include\boost/json/detail/impl/except.ipp(21): message : see declaration of 'boost::throw_exception' [F:\xRepos\boost-json\__build__\test\limits.vcxproj]  
F:\xRepos\boost-json\include\boost/json/detail/impl/except.ipp(72,12): error C2672: 'boost::throw_exception': no matching overloaded function found [F:\xRepos\boost-json\__build__\test\limits.vcxproj]  
F:\xRepos\boost-json\include\boost/json/detail/impl/except.ipp(73,30): error C2780: 'void boost::throw_exception(E)': expects 1 arguments - 2 provided [F:\xRepos\boost-json\__build__\test\limits.vcxproj]  
F:\xRepos\boost-json\include\boost/json/detail/impl/except.ipp(21): message : see declaration of 'boost::throw_exception' [F:\xRepos\boost-json\__build__\test\limits.vcxproj]  
F:\xRepos\boost-json\include\boost/json/string.hpp(2935,1): error C2872: 'detail': ambiguous symbol [F:\xRepos\boost-json\__build__\test\limits.vcxproj]  
F:\xRepos\boost-json\include\boost/json/pilfer.hpp(79,11): message : could be 'boost::json::detail' [F:\xRepos\boost-json\__build__\test\limits.vcxproj]  
F:\xRepos\boost-json\include\boost/json/detail/value.hpp(19,11): message : or       'boost::json::standalone::detail' [F:\xRepos\boost-json\__build__\test\limits.vcxproj]  
F:\xRepos\boost-json\include\boost/json/string.hpp(2935,39): error C2039: 'digest': is not a member of 'boost::json::detail' [F:\xRepos\boost-json\__build__\test\limits.vcxproj]  
F:\xRepos\boost-json\include\boost/json/pilfer.hpp(79): message : see declaration of 'boost::json::detail' [F:\xRepos\boost-json\__build__\test\limits.vcxproj]  
F:\xRepos\boost-json\include\boost/json/string.hpp(2935,1): error C3861: 'digest': identifier not found [F:\xRepos\boost-json\__build__\test\limits.vcxproj]  
F:\xRepos\boost-json\include\boost/json/impl/string.ipp(285,24): error C2672: 'pilfer': no matching overloaded function found [F:\xRepos\boost-json\__build__\test\limits.vcxproj]  
F:\xRepos\boost-json\include\boost/json/impl/string.ipp(285,36): error C2893: Failed to specialize function template 'std::conditional<std::is_nothrow_constructible<std::remove_reference<_Ty>::type,boost::json::pilfered<std::remove_reference<_Ty>::type>>::value  
&&!std::is_nothrow_constructible<std::remove_reference<_Ty>::type,boost::json::detail::not_pilfered<std::remove_reference<_Ty>::type>>::value,boost::json::pilfered<std::remove_reference<_Ty>::type>,std::remove_reference<_Ty>::type&&>::type boost::json::pilfer(T  
 &&) noexcept' [F:\xRepos\boost-json\__build__\test\limits.vcxproj]  
F:\xRepos\boost-json\include\boost/json/pilfer.hpp(142): message : see declaration of 'boost::json::pilfer' [F:\xRepos\boost-json\__build__\test\limits.vcxproj]  
F:\xRepos\boost-json\include\boost/json/impl/string.ipp(285,36): message : With the following template arguments: [F:\xRepos\boost-json\__build__\test\limits.vcxproj]  
F:\xRepos\boost-json\include\boost/json/impl/string.ipp(285,36): message : 'T=boost::json::standalone::string &' [F:\xRepos\boost-json\__build__\test\limits.vcxproj]  
F:\xRepos\boost-json\include\boost/json/impl/string.ipp(287,26): error C2672: 'pilfer': no matching overloaded function found [F:\xRepos\boost-json\__build__\test\limits.vcxproj]  
F:\xRepos\boost-json\include\boost/json/impl/string.ipp(287,38): error C2893: Failed to specialize function template 'std::conditional<std::is_nothrow_constructible<std::remove_reference<_Ty>::type,boost::json::pilfered<std::remove_reference<_Ty>::type>>::value  
&&!std::is_nothrow_constructible<std::remove_reference<_Ty>::type,boost::json::detail::not_pilfered<std::remove_reference<_Ty>::type>>::value,boost::json::pilfered<std::remove_reference<_Ty>::type>,std::remove_reference<_Ty>::type&&>::type boost::json::pilfer(T  
 &&) noexcept' [F:\xRepos\boost-json\__build__\test\limits.vcxproj]  
F:\xRepos\boost-json\include\boost/json/pilfer.hpp(142): message : see declaration of 'boost::json::pilfer' [F:\xRepos\boost-json\__build__\test\limits.vcxproj]  
F:\xRepos\boost-json\include\boost/json/impl/string.ipp(287,38): message : With the following template arguments: [F:\xRepos\boost-json\__build__\test\limits.vcxproj]  
F:\xRepos\boost-json\include\boost/json/impl/string.ipp(287,38): message : 'T=boost::json::standalone::string &' [F:\xRepos\boost-json\__build__\test\limits.vcxproj]  
F:\xRepos\boost-json\include\boost/json/impl/array.ipp(211,23): error C2672: 'pilfer': no matching overloaded function found [F:\xRepos\boost-json\__build__\test\limits.vcxproj]  
F:\xRepos\boost-json\include\boost/json/impl/array.ipp(211,33): error C2893: Failed to specialize function template 'std::conditional<std::is_nothrow_constructible<std::remove_reference<_Ty>::type,boost::json::pilfered<std::remove_reference<_Ty>::type>>::value&  
&!std::is_nothrow_constructible<std::remove_reference<_Ty>::type,boost::json::detail::not_pilfered<std::remove_reference<_Ty>::type>>::value,boost::json::pilfered<std::remove_reference<_Ty>::type>,std::remove_reference<_Ty>::type&&>::type boost::json::pilfer(T  
&&) noexcept' [F:\xRepos\boost-json\__build__\test\limits.vcxproj]  
F:\xRepos\boost-json\include\boost/json/pilfer.hpp(142): message : see declaration of 'boost::json::pilfer' [F:\xRepos\boost-json\__build__\test\limits.vcxproj]  
F:\xRepos\boost-json\include\boost/json/impl/array.ipp(211,33): message : With the following template arguments: [F:\xRepos\boost-json\__build__\test\limits.vcxproj]  
F:\xRepos\boost-json\include\boost/json/impl/array.ipp(211,33): message : 'T=boost::json::standalone::array &' [F:\xRepos\boost-json\__build__\test\limits.vcxproj]  
F:\xRepos\boost-json\include\boost/json/impl/array.ipp(221,23): error C2672: 'pilfer': no matching overloaded function found [F:\xRepos\boost-json\__build__\test\limits.vcxproj]  
F:\xRepos\boost-json\include\boost/json/impl/array.ipp(221,33): error C2893: Failed to specialize function template 'std::conditional<std::is_nothrow_constructible<std::remove_reference<_Ty>::type,boost::json::pilfered<std::remove_reference<_Ty>::type>>::value&  
&!std::is_nothrow_constructible<std::remove_reference<_Ty>::type,boost::json::detail::not_pilfered<std::remove_reference<_Ty>::type>>::value,boost::json::pilfered<std::remove_reference<_Ty>::type>,std::remove_reference<_Ty>::type&&>::type boost::json::pilfer(T  
&&) noexcept' [F:\xRepos\boost-json\__build__\test\limits.vcxproj]  
F:\xRepos\boost-json\include\boost/json/pilfer.hpp(142): message : see declaration of 'boost::json::pilfer' [F:\xRepos\boost-json\__build__\test\limits.vcxproj]  
F:\xRepos\boost-json\include\boost/json/impl/array.ipp(221,33): message : With the following template arguments: [F:\xRepos\boost-json\__build__\test\limits.vcxproj]  
F:\xRepos\boost-json\include\boost/json/impl/array.ipp(221,33): message : 'T=boost::json::standalone::array &' [F:\xRepos\boost-json\__build__\test\limits.vcxproj]  
F:\xRepos\boost-json\include\boost/json/impl/array.ipp(232,23): error C2672: 'pilfer': no matching overloaded function found [F:\xRepos\boost-json\__build__\test\limits.vcxproj]  
F:\xRepos\boost-json\include\boost/json/impl/array.ipp(232,33): error C2893: Failed to specialize function template 'std::conditional<std::is_nothrow_constructible<std::remove_reference<_Ty>::type,boost::json::pilfered<std::remove_reference<_Ty>::type>>::value&  
&!std::is_nothrow_constructible<std::remove_reference<_Ty>::type,boost::json::detail::not_pilfered<std::remove_reference<_Ty>::type>>::value,boost::json::pilfered<std::remove_reference<_Ty>::type>,std::remove_reference<_Ty>::type&&>::type boost::json::pilfer(T  
&&) noexcept' [F:\xRepos\boost-json\__build__\test\limits.vcxproj]  
F:\xRepos\boost-json\include\boost/json/pilfer.hpp(142): message : see declaration of 'boost::json::pilfer' [F:\xRepos\boost-json\__build__\test\limits.vcxproj]  
F:\xRepos\boost-json\include\boost/json/impl/array.ipp(232,33): message : With the following template arguments: [F:\xRepos\boost-json\__build__\test\limits.vcxproj]  
F:\xRepos\boost-json\include\boost/json/impl/array.ipp(232,33): message : 'T=boost::json::standalone::array &' [F:\xRepos\boost-json\__build__\test\limits.vcxproj]  
F:\xRepos\boost-json\include\boost/json/impl/array.ipp(465,23): error C2672: 'pilfer': no matching overloaded function found [F:\xRepos\boost-json\__build__\test\limits.vcxproj]  
F:\xRepos\boost-json\include\boost/json/impl/array.ipp(465,35): error C2893: Failed to specialize function template 'std::conditional<std::is_nothrow_constructible<std::remove_reference<_Ty>::type,boost::json::pilfered<std::remove_reference<_Ty>::type>>::value&  
&!std::is_nothrow_constructible<std::remove_reference<_Ty>::type,boost::json::detail::not_pilfered<std::remove_reference<_Ty>::type>>::value,boost::json::pilfered<std::remove_reference<_Ty>::type>,std::remove_reference<_Ty>::type&&>::type boost::json::pilfer(T  
&&) noexcept' [F:\xRepos\boost-json\__build__\test\limits.vcxproj]  
F:\xRepos\boost-json\include\boost/json/pilfer.hpp(142): message : see declaration of 'boost::json::pilfer' [F:\xRepos\boost-json\__build__\test\limits.vcxproj]  
F:\xRepos\boost-json\include\boost/json/impl/array.ipp(465,35): message : With the following template arguments: [F:\xRepos\boost-json\__build__\test\limits.vcxproj]  
F:\xRepos\boost-json\include\boost/json/impl/array.ipp(465,35): message : 'T=boost::json::standalone::array &' [F:\xRepos\boost-json\__build__\test\limits.vcxproj]  
F:\xRepos\boost-json\include\boost/json/impl/array.ipp(467,25): error C2672: 'pilfer': no matching overloaded function found [F:\xRepos\boost-json\__build__\test\limits.vcxproj]  
F:\xRepos\boost-json\include\boost/json/impl/array.ipp(467,37): error C2893: Failed to specialize function template 'std::conditional<std::is_nothrow_constructible<std::remove_reference<_Ty>::type,boost::json::pilfered<std::remove_reference<_Ty>::type>>::value&  
&!std::is_nothrow_constructible<std::remove_reference<_Ty>::type,boost::json::detail::not_pilfered<std::remove_reference<_Ty>::type>>::value,boost::json::pilfered<std::remove_reference<_Ty>::type>,std::remove_reference<_Ty>::type&&>::type boost::json::pilfer(T  
&&) noexcept' [F:\xRepos\boost-json\__build__\test\limits.vcxproj]  
F:\xRepos\boost-json\include\boost/json/pilfer.hpp(142): message : see declaration of 'boost::json::pilfer' [F:\xRepos\boost-json\__build__\test\limits.vcxproj]  
F:\xRepos\boost-json\include\boost/json/impl/array.ipp(467,37): message : With the following template arguments: [F:\xRepos\boost-json\__build__\test\limits.vcxproj]  
F:\xRepos\boost-json\include\boost/json/impl/array.ipp(467,37): message : 'T=boost::json::standalone::array &' [F:\xRepos\boost-json\__build__\test\limits.vcxproj]  
F:\xRepos\boost-json\include\boost/json/impl/object.ipp(220,24): error C2672: 'pilfer': no matching overloaded function found [F:\xRepos\boost-json\__build__\test\limits.vcxproj]  
F:\xRepos\boost-json\include\boost/json/impl/object.ipp(220,34): error C2893: Failed to specialize function template 'std::conditional<std::is_nothrow_constructible<std::remove_reference<_Ty>::type,boost::json::pilfered<std::remove_reference<_Ty>::type>>::value  
&&!std::is_nothrow_constructible<std::remove_reference<_Ty>::type,boost::json::detail::not_pilfered<std::remove_reference<_Ty>::type>>::value,boost::json::pilfered<std::remove_reference<_Ty>::type>,std::remove_reference<_Ty>::type&&>::type boost::json::pilfer(T  
 &&) noexcept' [F:\xRepos\boost-json\__build__\test\limits.vcxproj]  
F:\xRepos\boost-json\include\boost/json/pilfer.hpp(142): message : see declaration of 'boost::json::pilfer' [F:\xRepos\boost-json\__build__\test\limits.vcxproj]  
F:\xRepos\boost-json\include\boost/json/impl/object.ipp(220,34): message : With the following template arguments: [F:\xRepos\boost-json\__build__\test\limits.vcxproj]  
F:\xRepos\boost-json\include\boost/json/impl/object.ipp(220,34): message : 'T=boost::json::standalone::object &' [F:\xRepos\boost-json\__build__\test\limits.vcxproj]  
F:\xRepos\boost-json\include\boost/json/impl/object.ipp(232,24): error C2672: 'pilfer': no matching overloaded function found [F:\xRepos\boost-json\__build__\test\limits.vcxproj]  
F:\xRepos\boost-json\include\boost/json/impl/object.ipp(232,34): error C2893: Failed to specialize function template 'std::conditional<std::is_nothrow_constructible<std::remove_reference<_Ty>::type,boost::json::pilfered<std::remove_reference<_Ty>::type>>::value  
&&!std::is_nothrow_constructible<std::remove_reference<_Ty>::type,boost::json::detail::not_pilfered<std::remove_reference<_Ty>::type>>::value,boost::json::pilfered<std::remove_reference<_Ty>::type>,std::remove_reference<_Ty>::type&&>::type boost::json::pilfer(T  
 &&) noexcept' [F:\xRepos\boost-json\__build__\test\limits.vcxproj]  
F:\xRepos\boost-json\include\boost/json/pilfer.hpp(142): message : see declaration of 'boost::json::pilfer' [F:\xRepos\boost-json\__build__\test\limits.vcxproj]  
F:\xRepos\boost-json\include\boost/json/impl/object.ipp(232,34): message : With the following template arguments: [F:\xRepos\boost-json\__build__\test\limits.vcxproj]  
F:\xRepos\boost-json\include\boost/json/impl/object.ipp(232,34): message : 'T=boost::json::standalone::object &' [F:\xRepos\boost-json\__build__\test\limits.vcxproj]  
F:\xRepos\boost-json\include\boost/json/impl/object.ipp(244,24): error C2672: 'pilfer': no matching overloaded function found [F:\xRepos\boost-json\__build__\test\limits.vcxproj]  
F:\xRepos\boost-json\include\boost/json/impl/object.ipp(244,34): error C2893: Failed to specialize function template 'std::conditional<std::is_nothrow_constructible<std::remove_reference<_Ty>::type,boost::json::pilfered<std::remove_reference<_Ty>::type>>::value  
&&!std::is_nothrow_constructible<std::remove_reference<_Ty>::type,boost::json::detail::not_pilfered<std::remove_reference<_Ty>::type>>::value,boost::json::pilfered<std::remove_reference<_Ty>::type>,std::remove_reference<_Ty>::type&&>::type boost::json::pilfer(T  
 &&) noexcept' [F:\xRepos\boost-json\__build__\test\limits.vcxproj]  
F:\xRepos\boost-json\include\boost/json/pilfer.hpp(142): message : see declaration of 'boost::json::pilfer' [F:\xRepos\boost-json\__build__\test\limits.vcxproj]  
F:\xRepos\boost-json\include\boost/json/impl/object.ipp(244,34): message : With the following template arguments: [F:\xRepos\boost-json\__build__\test\limits.vcxproj]  
F:\xRepos\boost-json\include\boost/json/impl/object.ipp(244,34): message : 'T=boost::json::standalone::object &' [F:\xRepos\boost-json\__build__\test\limits.vcxproj]  
F:\xRepos\boost-json\include\boost/json/impl/object.ipp(372,26): error C2672: 'pilfer': no matching overloaded function found [F:\xRepos\boost-json\__build__\test\limits.vcxproj]  
F:\xRepos\boost-json\include\boost/json/impl/object.ipp(372,38): error C2893: Failed to specialize function template 'std::conditional<std::is_nothrow_constructible<std::remove_reference<_Ty>::type,boost::json::pilfered<std::remove_reference<_Ty>::type>>::value  
&&!std::is_nothrow_constructible<std::remove_reference<_Ty>::type,boost::json::detail::not_pilfered<std::remove_reference<_Ty>::type>>::value,boost::json::pilfered<std::remove_reference<_Ty>::type>,std::remove_reference<_Ty>::type&&>::type boost::json::pilfer(T  
 &&) noexcept' [F:\xRepos\boost-json\__build__\test\limits.vcxproj]  
F:\xRepos\boost-json\include\boost/json/pilfer.hpp(142): message : see declaration of 'boost::json::pilfer' [F:\xRepos\boost-json\__build__\test\limits.vcxproj]  
F:\xRepos\boost-json\include\boost/json/impl/object.ipp(372,38): message : With the following template arguments: [F:\xRepos\boost-json\__build__\test\limits.vcxproj]  
F:\xRepos\boost-json\include\boost/json/impl/object.ipp(372,38): message : 'T=boost::json::standalone::object &' [F:\xRepos\boost-json\__build__\test\limits.vcxproj]  
F:\xRepos\boost-json\include\boost/json/impl/object.ipp(374,24): error C2672: 'pilfer': no matching overloaded function found [F:\xRepos\boost-json\__build__\test\limits.vcxproj]  
F:\xRepos\boost-json\include\boost/json/impl/object.ipp(374,36): error C2893: Failed to specialize function template 'std::conditional<std::is_nothrow_constructible<std::remove_reference<_Ty>::type,boost::json::pilfered<std::remove_reference<_Ty>::type>>::value  
&&!std::is_nothrow_constructible<std::remove_reference<_Ty>::type,boost::json::detail::not_pilfered<std::remove_reference<_Ty>::type>>::value,boost::json::pilfered<std::remove_reference<_Ty>::type>,std::remove_reference<_Ty>::type&&>::type boost::json::pilfer(T  
 &&) noexcept' [F:\xRepos\boost-json\__build__\test\limits.vcxproj]  
F:\xRepos\boost-json\include\boost/json/pilfer.hpp(142): message : see declaration of 'boost::json::pilfer' [F:\xRepos\boost-json\__build__\test\limits.vcxproj]  
F:\xRepos\boost-json\include\boost/json/impl/object.ipp(374,36): message : With the following template arguments: [F:\xRepos\boost-json\__build__\test\limits.vcxproj]  
F:\xRepos\boost-json\include\boost/json/impl/object.ipp(374,36): message : 'T=boost::json::standalone::object &' [F:\xRepos\boost-json\__build__\test\limits.vcxproj]  
F:\xRepos\boost-json\include\boost/json/impl/value.ipp(317,29): error C2672: 'pilfer': no matching overloaded function found [F:\xRepos\boost-json\__build__\test\limits.vcxproj]  
F:\xRepos\boost-json\include\boost/json/impl/value.ipp(317,41): error C2893: Failed to specialize function template 'std::conditional<std::is_nothrow_constructible<std::remove_reference<_Ty>::type,boost::json::pilfered<std::remove_reference<_Ty>::type>>::value&  
&!std::is_nothrow_constructible<std::remove_reference<_Ty>::type,boost::json::detail::not_pilfered<std::remove_reference<_Ty>::type>>::value,boost::json::pilfered<std::remove_reference<_Ty>::type>,std::remove_reference<_Ty>::type&&>::type boost::json::pilfer(T  
&&) noexcept' [F:\xRepos\boost-json\__build__\test\limits.vcxproj]  
F:\xRepos\boost-json\include\boost/json/pilfer.hpp(142): message : see declaration of 'boost::json::pilfer' [F:\xRepos\boost-json\__build__\test\limits.vcxproj]  
F:\xRepos\boost-json\include\boost/json/impl/value.ipp(317,41): message : With the following template arguments: [F:\xRepos\boost-json\__build__\test\limits.vcxproj]  
F:\xRepos\boost-json\include\boost/json/impl/value.ipp(317,41): message : 'T=boost::json::standalone::value &' [F:\xRepos\boost-json\__build__\test\limits.vcxproj]  
F:\xRepos\boost-json\include\boost/json/impl/value.ipp(319,27): error C2672: 'pilfer': no matching overloaded function found [F:\xRepos\boost-json\__build__\test\limits.vcxproj]  
F:\xRepos\boost-json\include\boost/json/impl/value.ipp(319,39): error C2893: Failed to specialize function template 'std::conditional<std::is_nothrow_constructible<std::remove_reference<_Ty>::type,boost::json::pilfered<std::remove_reference<_Ty>::type>>::value&  
&!std::is_nothrow_constructible<std::remove_reference<_Ty>::type,boost::json::detail::not_pilfered<std::remove_reference<_Ty>::type>>::value,boost::json::pilfered<std::remove_reference<_Ty>::type>,std::remove_reference<_Ty>::type&&>::type boost::json::pilfer(T  
&&) noexcept' [F:\xRepos\boost-json\__build__\test\limits.vcxproj]  
F:\xRepos\boost-json\include\boost/json/pilfer.hpp(142): message : see declaration of 'boost::json::pilfer' [F:\xRepos\boost-json\__build__\test\limits.vcxproj]  
F:\xRepos\boost-json\include\boost/json/impl/value.ipp(319,39): message : With the following template arguments: [F:\xRepos\boost-json\__build__\test\limits.vcxproj]  
F:\xRepos\boost-json\include\boost/json/impl/value.ipp(319,39): message : 'T=boost::json::standalone::value &' [F:\xRepos\boost-json\__build__\test\limits.vcxproj]  
F:\xRepos\boost-json\include\boost/json/impl/value_stack.ipp(329,9): error C2672: 'pilfer': no matching overloaded function found [F:\xRepos\boost-json\__build__\test\limits.vcxproj]  
F:\xRepos\boost-json\include\boost/json/impl/value_stack.ipp(329,18): error C2893: Failed to specialize function template 'std::conditional<std::is_nothrow_constructible<std::remove_reference<_Ty>::type,boost::json::pilfered<std::remove_reference<_Ty>::type>>::  
value&&!std::is_nothrow_constructible<std::remove_reference<_Ty>::type,boost::json::detail::not_pilfered<std::remove_reference<_Ty>::type>>::value,boost::json::pilfered<std::remove_reference<_Ty>::type>,std::remove_reference<_Ty>::type&&>::type boost::json::pil  
fer(T &&) noexcept' [F:\xRepos\boost-json\__build__\test\limits.vcxproj]  
F:\xRepos\boost-json\include\boost/json/pilfer.hpp(142): message : see declaration of 'boost::json::pilfer' [F:\xRepos\boost-json\__build__\test\limits.vcxproj]  
F:\xRepos\boost-json\include\boost/json/impl/value_stack.ipp(329,18): message : With the following template arguments: [F:\xRepos\boost-json\__build__\test\limits.vcxproj]  
F:\xRepos\boost-json\include\boost/json/impl/value_stack.ipp(329,18): message : 'T=boost::json::standalone::storage_ptr &' [F:\xRepos\boost-json\__build__\test\limits.vcxproj]  
F:\xRepos\boost-json\include\boost/json/impl/value_stack.ipp(349,12): error C2672: 'pilfer': no matching overloaded function found [F:\xRepos\boost-json\__build__\test\limits.vcxproj]  
F:\xRepos\boost-json\include\boost/json/impl/value_stack.ipp(349,34): error C2893: Failed to specialize function template 'std::conditional<std::is_nothrow_constructible<std::remove_reference<_Ty>::type,boost::json::pilfered<std::remove_reference<_Ty>::type>>::  
value&&!std::is_nothrow_constructible<std::remove_reference<_Ty>::type,boost::json::detail::not_pilfered<std::remove_reference<_Ty>::type>>::value,boost::json::pilfered<std::remove_reference<_Ty>::type>,std::remove_reference<_Ty>::type&&>::type boost::json::pil  
fer(T &&) noexcept' [F:\xRepos\boost-json\__build__\test\limits.vcxproj]  
F:\xRepos\boost-json\include\boost/json/pilfer.hpp(142): message : see declaration of 'boost::json::pilfer' [F:\xRepos\boost-json\__build__\test\limits.vcxproj]  
F:\xRepos\boost-json\include\boost/json/impl/value_stack.ipp(349,34): message : With the following template arguments: [F:\xRepos\boost-json\__build__\test\limits.vcxproj]  
F:\xRepos\boost-json\include\boost/json/impl/value_stack.ipp(349,34): message : 'T=boost::json::standalone::value &' [F:\xRepos\boost-json\__build__\test\limits.vcxproj]  
  main.cpp  
  Generating Code...```

---

## Comment 1

> Username: Mike-Devel  
> Created at: 2020-09-06 16:45:20 UTC  
> Url: https://github.com/boostorg/json/issues/272#issuecomment-687838372  

Thanks. Can confirm that it compiles now on my system.

---

## Comment 2

> Username: vinniefalco  
> Created at: 2020-09-06 16:46:49 UTC  
> Url: https://github.com/boostorg/json/issues/272#issuecomment-687838736  

Related: https://www.reddit.com/r/cpp/comments/inmo3a/inline_namespace_bug_in_msvc/
