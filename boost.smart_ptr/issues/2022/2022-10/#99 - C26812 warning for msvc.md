# #99 - C26812 warning for msvc [Open]

> Username: danyhm  
> Created at: 2022-10-13 12:27:55 UTC  
> Updated at: 2022-10-13 12:27:55 UTC  
> Url: https://github.com/boostorg/smart_ptr/issues/99  

Hello,  
I'm getting C26812 Warning in a project for Visual studio 2019 for boost using vcpkg.  
  
`Warning	C26812	The enum type 'boost::detail::local_counted_base::count_type' is unscoped. Prefer 'enum class' over 'enum' (Enum.3).	test_winapi	C:\dev\vcpkg\installed\x86-windows\include\boost\smart_ptr\detail\local_counted_base.hpp	45`  
  
it would be nice to either suppress the warning or take another action accordingly
