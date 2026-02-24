# #26 Fixing visual studio compilation of string_view::at() [Merged]

> Username: Surrog  
> Created at: 2016-12-12 21:59:55 UTC  
> Updated at: 2016-12-13 05:34:30 UTC  
> Merged at: 2016-12-13 01:22:29 UTC  
> Closed at: 2016-12-13 01:22:29 UTC  
> Url: https://github.com/boostorg/utility/pull/26  

VS2013, VS2015 & VS2017RC don't like the ternary throwing an exception :  
'return': cannot convert from 'void' to 'const char &'  
I propose to fix that by using classic if when compiling on a windows platform.

---

## Comment 1

> Username: Lastique  
> Created_at: 2016-12-12 22:11:20 UTC  
> Url: https://github.com/boostorg/utility/pull/26#issuecomment-266569301  

That makes the function not `constexpr` in C++11.

---

## Comment 2

> Username: Lastique  
> Created_at: 2016-12-12 22:13:38 UTC  
> Url: https://github.com/boostorg/utility/pull/26#issuecomment-266569882  

Maybe something like `BOOST_THROW_EXCEPTION(std::out_of_range("boost::string_view::at")), ptr_[0]` would do as the `true` branch of the operator?

---

## Comment 3

> Username: mclow  
> Created_at: 2016-12-12 22:33:15 UTC  
> Updated_at: 2016-12-12 22:33:24 UTC  
> Url: https://github.com/boostorg/utility/pull/26#issuecomment-266574820  

> Maybe something like ...  
  
I don't know. Can you try it and find out?

---

## Comment 4

> Username: Surrog  
> Created_at: 2016-12-12 22:47:39 UTC  
> Url: https://github.com/boostorg/utility/pull/26#issuecomment-266578161  

Hum, I didn't though about the old constexpr, it is only available in VS2015, where it has been introduced. VS2017 use the updated c++14.  
  
The "BOOST_THROW_EXCEPTION(std::out_of_range("boost::string_view::at")), ptr_[0]" do work, so I've update my proposal with a compiling ternary: nice trick

---

## Comment 5

> Username: mclow  
> Created_at: 2016-12-13 01:21:46 UTC  
> Url: https://github.com/boostorg/utility/pull/26#issuecomment-266607014  

If the `string_view` is empty, `ptr_[0]` invokes undefined behavior. On the other hand, it's never executed.

---

## Comment 6

> Username: Lastique  
> Created_at: 2016-12-13 05:34:30 UTC  
> Url: https://github.com/boostorg/utility/pull/26#issuecomment-266648181  

On 12/13/16 04:21, Marshall Clow wrote:  
> If the |string_view| is empty, |ptr_[0]| invokes undefined behavior. On  
> the other hand, it's never executed.  
  
Right. Although formally `constexpr` cannot work in presence of UB, I   
don't think compilers will fail to compile that.  
  
If that turns out as a problem, we could replace `ptr_` with a string   
literal instead. Maybe even the same literal that is used as the   
exception message, to conserve binary size.

---
