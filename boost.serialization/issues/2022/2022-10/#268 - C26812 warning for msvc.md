# #268 - C26812 warning for msvc [Open]

> Username: danyhm  
> Created at: 2022-10-13 12:27:22 UTC  
> Updated at: 2022-10-13 12:27:22 UTC  
> Url: https://github.com/boostorg/serialization/issues/268  

Hello,  
I'm getting C26812 Warning in a project for Visual studio 2019 for boost using vcpkg.  
  
`Warning	C26812	The enum type 'boost::archive::basic_text_oarchive<boost::archive::text_oarchive>::<unnamed-type-delimiter>' is unscoped. Prefer 'enum class' over 'enum' (Enum.3).	test_winapi	C:\dev\vcpkg\installed\x86-windows\include\boost\archive\basic_text_oarchive.hpp	75`  
  
it would be nice to either suppress the warning or take another action accordingly
