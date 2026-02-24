# #516 - some compile error when I  use msvc2022 [Open]

> Username: kcwl  
> Created at: 2023-03-16 09:18:55 UTC  
> Updated at: 2023-11-09 11:20:36 UTC  
> Url: https://github.com/boostorg/hana/issues/516  

when i use vs2022, Something went wrong！ when I define hana struct like this:   
```  
struct Person {  
    BOOST_HANA_DEFINE_STRUCT(Person,  
        (std::string, name),  
        (unsigned short, age)  
    );  
  
    Person john{ "John", 30 };  
    boost::hana::find(john, BOOST_HANA_STRING("name")) == boost::hana::just("John");  
};  
```  
  
Error !!  
`  
1>F:\boost_1_81_0\boost\hana\traits.hpp(71,80): error C4996: 'std::is_pod': warning STL4025: std::is_pod and std::is_pod_v are deprecated in C++20. The std::is_trivially_copyable and/or std::is_standard_layout traits likely suit your use case. You can define _SILENCE_CXX20_IS_POD_DEPRECATION_WARNING or _SILENCE_ALL_CXX20_DEPRECATION_WARNINGS to acknowledge that you have received this warning.  
1>F:\boost_1_81_0\boost\hana\traits.hpp(74,89): error C2039: 'is_literal_type': is not a member of 'std'  
1>D:\vs2022\Community\VC\Tools\MSVC\14.29.30133\include\iostream(19): message : see declaration of 'std'  
1>F:\boost_1_81_0\boost\hana\traits.hpp(74,104): error C3536: 'boost::hana::traits::is_literal_type': cannot be used before it is initialized  
1>F:\boost_1_81_0\boost\hana\traits.hpp(74,104): error C3200: 'int': invalid template argument for template parameter 'F', expected a class template  
1>F:\boost_1_81_0\boost\hana\traits.hpp(74,105): error C3200: 'int': invalid template argument for template parameter 'F', expected a class template  
1>F:\boost_1_81_0\boost\hana\traits.hpp(74,105): error C2641: cannot deduce template arguments for 'boost::hana::traits::detail::hana_trait'  
1>F:\boost_1_81_0\boost\hana\traits.hpp(74,105): error C2783: 'boost::hana::traits::detail::hana_trait<F> boost::hana::traits::detail::hana_trait(void)': could not deduce template argument for 'F'  
1>F:\boost_1_81_0\boost\hana\traits.hpp(27): message : see declaration of 'boost::hana::traits::detail::hana_trait'  
1>F:\boost_1_81_0\boost\hana\traits.hpp(74,106): error C2780: 'boost::hana::traits::detail::hana_trait<F> boost::hana::traits::detail::hana_trait(boost::hana::traits::detail::hana_trait<F>)': expects 1 arguments - 0 provided  
1>F:\boost_1_81_0\boost\hana\traits.hpp(27): message : see declaration of 'boost::hana::traits::detail::hana_trait'  
1>F:\boost_1_81_0\boost\hana\traits.hpp(74,47): error C2737: 'boost::hana::traits::is_literal_type': constexpr object must be initialized  
1>G:\git\ConsoleApplication6\ConsoleApplication6.cpp(29,18): error C2078: too many initializers  
`  
  
What can I do for this??

---

## Comment 1

> Username: ldionne  
> Created at: 2023-03-17 13:53:06 UTC  
> Url: https://github.com/boostorg/hana/issues/516#issuecomment-1473878564  

Can you show the command-line you use to compile?

---

## Comment 2

> Username: ldionne  
> Created at: 2023-03-17 13:56:49 UTC  
> Url: https://github.com/boostorg/hana/issues/516#issuecomment-1473883513  

We have this in the code so I suspect MSVC isn't defining `__cplusplus` appropriately:  
  
```  
#if __cplusplus < 202002L  
    BOOST_HANA_INLINE_VARIABLE constexpr auto is_pod = detail::hana_trait<std::is_pod>{};  
#endif  
#if __cplusplus < 201703L  
    BOOST_HANA_INLINE_VARIABLE constexpr auto is_literal_type = detail::hana_trait<std::is_literal_type>{};  
#endif  
```

---

## Comment 3

> Username: kcwl  
> Created at: 2023-03-21 06:02:46 UTC  
> Url: https://github.com/boostorg/hana/issues/516#issuecomment-1477325829  

`/JMC /permissive- /ifcOutput "x64\Debug\" /GS /W4 /Zc:wchar_t /I"C:\Program Files\MySQL\MySQL Server 8.0\mysql\include" /I"G:\git\sqlpro\include" /I"F:\boost_1_81_0" /ZI /Gm- /Od /sdl /Fd"x64\Debug\vc143.pdb" /Zc:inline /fp:precise /D "_DEBUG" /D "_CONSOLE" /D "_UNICODE" /D "UNICODE" /errorReport:prompt /WX- /Zc:forScope /RTC1 /std:c17 /Gd /MDd /std:c++latest /FC /Fa"x64\Debug\" /EHsc /nologo /Fo"x64\Debug\" /Fp"x64\Debug\ConsoleApplication6.pch" /diagnostics:column `  
  
Top is my command-line! where do I go wrong?

---

## Comment 4

> Username: tuokri  
> Created at: 2023-11-09 11:20:35 UTC  
> Url: https://github.com/boostorg/hana/issues/516#issuecomment-1803643798  

I fixed this issue by adding the [/Zc:__cplusplus](https://learn.microsoft.com/en-us/cpp/build/reference/zc-cplusplus?view=msvc-170) option. For reference, I'm using `MSVC 19.37.32825` and `Boost.Hana 1.83.0` from vcpkg.
