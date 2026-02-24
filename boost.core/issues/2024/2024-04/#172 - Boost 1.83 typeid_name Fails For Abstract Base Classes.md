# #172 - Boost 1.83 typeid_name Fails For Abstract Base Classes [Closed]

> Username: canon-cmi-william-hachfeld  
> Created at: 2024-04-24 18:06:55 UTC  
> Updated at: 2024-04-25 15:08:28 UTC  
> Closed at: 2024-04-24 23:21:20 UTC  
> Url: https://github.com/boostorg/core/issues/172  

I believe [this](https://github.com/boostorg/core/commit/36fa78f53c326d37d646dc6990879607726534b8) change made by @pdimov has broken `typeid_name` for abstract base classes (at least when compiling with MSVC 19.39). Consider the following minimal repro case:  
  
    #include <string>  
    #include <typeinfo>  
  
    inline std::string fix_typeid_name(char const* n)  
    {  
        return ""; // Empty Implementation  
    }  
  
    #if defined(USE_BOOST_1_83_VERSION)  
  
    // class types can be incomplete  
    template<class T> std::string typeid_name_impl(int T::*)  
    {  
        std::string r = fix_typeid_name(typeid(T[1]).name());  
        return r.substr(0, r.size() - 4); // remove ' [1]' suffix  
    }  
  
    template<class T> std::string typeid_name_impl(...)  
    {  
        return fix_typeid_name(typeid(T).name());  
    }  
  
    template<class T> std::string typeid_name()  
    {  
        return typeid_name_impl<T>(0);  
    }  
  
    #else  
  
    template<class T> std::string typeid_name()  
    {  
        return fix_typeid_name(typeid(T).name());  
    }  
  
    #endif  
  
    class MyClass  
    {  
        virtual int f() const = 0;  
    };  
  
    std::string test()  
    {  
        return typeid_name<MyClass>();  
    }  
  
Compiling this with MSVC using the Boost >= 1.83.0 version yields:  
  
    D:\Boost_Core_Issue>cl /DUSE_BOOST_1_83_VERSION test.cpp  
    Microsoft (R) C/C++ Optimizing Compiler Version 19.39.33523 for x64  
    Copyright (C) Microsoft Corporation.  All rights reserved.  
  
    test.cpp  
    test.cpp(15): error C2092: 'abstract declarator' array element type cannot be function or abstract class type  
    test.cpp(15): note: the template instantiation context (the oldest one first) is  
    test.cpp(45): note: see reference to function template instantiation 'std::string typeid_name<MyClass>(void)' being compiled  
    test.cpp(26): note: see reference to function template instantiation 'std::string typeid_name_impl<T>(int MyClass::* )' being compiled  
            with  
            [  
                T=MyClass  
            ]  
  
    D:\Boost_Core_Issue>  
  
But using the earlier implementation from Boost 1.82.0 compile fine:  
  
    D:\Boost_Core_Issue>cl test.cpp  
    Microsoft (R) C/C++ Optimizing Compiler Version 19.39.33523 for x64  
    Copyright (C) Microsoft Corporation.  All rights reserved.  
  
    test.cpp  
    Microsoft (R) Incremental Linker Version 14.39.33523.0  
    Copyright (C) Microsoft Corporation.  All rights reserved.  
  
    /out:test.exe  
    test.obj  
    LINK : fatal error LNK1561: entry point must be defined  
  
    D:\Boost_Core_Issue>  
  
I must admit I don't quite understand what the `int T::*` syntax is supposed to do exactly, so I'm unclear whether the Microsoft compiler itself could be at fault here. So please do let me know if you believe this is a compiler bug rather than an issue with the code itself. In which case I'll report the issue to Microsoft instead. Thanks in advance for taking a look!

---

## Comment 1

> Username: pdimov  
> Created at: 2024-04-24 18:21:17 UTC  
> Url: https://github.com/boostorg/core/issues/172#issuecomment-2075562596  

Thanks, I'll look into it. Various compilers have various limitations for what can go into `typeid(T)` so it sometimes happens that when I fix one thing, another breaks.

---

## Comment 2

> Username: canon-cmi-william-hachfeld  
> Created at: 2024-04-24 18:29:06 UTC  
> Url: https://github.com/boostorg/core/issues/172#issuecomment-2075576243  

Absolutely. Totally understand, Peter. I appreciate you taking a look at whether there is something that can be done on the Boost side to address this.

---

## Comment 3

> Username: pdimov  
> Created at: 2024-04-24 23:22:34 UTC  
> Url: https://github.com/boostorg/core/issues/172#issuecomment-2076021088  

Should be fixed by https://github.com/boostorg/core/commit/965508d9e187d589070c5cd1853cc035b51ea3cf.

---

## Comment 4

> Username: canon-cmi-william-hachfeld  
> Created at: 2024-04-25 15:08:27 UTC  
> Url: https://github.com/boostorg/core/issues/172#issuecomment-2077501601  

Fantastic. Thank you, @pdimov, for addressing this so quickly!
