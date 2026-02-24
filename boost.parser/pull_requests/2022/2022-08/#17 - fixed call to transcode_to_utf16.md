# #17 fixed call to transcode_to_utf16 [Closed]

> Username: tobias-loew  
> Created at: 2022-08-04 14:45:27 UTC  
> Updated at: 2024-01-03 08:10:37 UTC  
> Closed at: 2024-01-03 08:10:36 UTC  
> Url: https://github.com/boostorg/parser/pull/17  

Hi Zach,  
  
apart from this minor error, I'm not able to compile a simple example with VS 2019 or 2022 (even with clang-cl). I always get an error in  
parser\include\boost\parser\detail\text\transcode_view.hpp(519,16): error C7602: 'boost::parser::detail::text::utf8_view': the associated constraints are not satisfied  
It seems that the given template-types to utf8_view are not utf8_iters.  
  
Tobias

---

## Comment 1

> Username: tobias-loew  
> Created_at: 2022-08-04 17:57:19 UTC  
> Url: https://github.com/boostorg/parser/pull/17#issuecomment-1205589513  

I took a look at the compilation error: it only happens with C++20 and concepts enabled. The contraints for utf8_view fail since the given Iterator doesn't support post-increment which is required by std::weakly_incrementable (which is part of std::input_or_output_iterator, which is part of std::sentinel_for)

---

## Comment 2

> Username: tobias-loew  
> Created_at: 2022-08-09 13:00:31 UTC  
> Url: https://github.com/boostorg/parser/pull/17#issuecomment-1209352041  

I added tests for VS 2022, Win32/x64 and removed those for VS 2017. (VS 2017 has its own opinion about literal-types. Supporting it would require optional removing of several "constexpr".)

---

## Comment 3

> Username: tobias-loew  
> Created_at: 2022-08-16 14:52:00 UTC  
> Updated_at: 2022-08-16 14:55:26 UTC  
> Url: https://github.com/boostorg/parser/pull/17#issuecomment-1216748091  

While implementing a Pascal parser I missed the `no_case` directive from spirit.   
So, I added an input-manipulation-directive `input_transform<Functor>`, where the input-characters are transformed by `Functor{}.operator()`.  
Since I never liked the name `no_case`, I added two specialization: `lower_case` and `upper_case`

---

## Comment 4

> Username: tzlaine  
> Created_at: 2023-12-02 04:41:25 UTC  
> Url: https://github.com/boostorg/parser/pull/17#issuecomment-1837037718  

Hey, Tobias.  Sorry this took me *forever* to get back to, but now this should be fixed on master.  I've build with VS2017, VS2019, and VS2022.   VS2022 was tested in C++17 and C++20 modes.  The others I only tested C++17 mode.  Let me know if that work for you.  If it does, I'll close this PR.

---
