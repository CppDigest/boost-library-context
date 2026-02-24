# #150 - GIve away the details of `get_name` [Closed]

> Username: akrzemi1  
> Created at: 2023-11-28 20:45:44 UTC  
> Updated at: 2024-02-14 18:00:29 UTC  
> Closed at: 2024-02-14 18:00:29 UTC  
> Url: https://github.com/boostorg/pfr/issues/150  

Would it be possible to describe in "How it works" how obtaining the name is implemented?  
  
I tried to study the library for a couple of hours, and still cannot understand it. Apparently, you must be creating a pointer-to-member somewhere, no? But how is it done?

---

## Comment 1

> Username: bansan85  
> Created at: 2023-11-29 07:07:36 UTC  
> Updated at: 2023-11-30 08:23:16 UTC  
> Url: https://github.com/boostorg/pfr/issues/150#issuecomment-1831329846  

You need to pass the field in a template function. Then you use a demangle function inside the current template function.  
  
To demangle, use the special feature from the preprocessor: `__PRETTY_FUNCTION__` or `__FUNCSIG__`. https://github.com/boostorg/pfr/blob/c695aa0b32ea7665d9e76cfa25655ed6302508e5/include/boost/pfr/detail/core_name20_static.hpp#L88-L96  
  
Then you need to extract the template parameter of the current template function. But the output of the demangle function is not standardized, it's compiler dependent. See `BOOST_PFR_CORE_NAME_PARSING` https://github.com/boostorg/pfr/blob/c695aa0b32ea7665d9e76cfa25655ed6302508e5/include/boost/pfr/config.hpp#L115-L126  
  
You may have a look at https://bitwizeshift.github.io/posts/2021/03/09/getting-an-unmangled-type-name-at-compile-time/  
  
And after writing it, I noticed it returns only the type, not the member.... I hope it will help you a little... I also spend an hour without understand how the name of the field is demangled by `__PRETTY_FUNCTION__`.

---

## Comment 2

> Username: denzor200  
> Created at: 2023-11-29 07:26:43 UTC  
> Url: https://github.com/boostorg/pfr/issues/150#issuecomment-1831352726  

https://github.com/boostorg/pfr/issues/90  
It was borned here in the comments :)

---

## Comment 3

> Username: akrzemi1  
> Created at: 2023-11-29 08:51:25 UTC  
> Url: https://github.com/boostorg/pfr/issues/150#issuecomment-1831468967  

Thanks.  
Bu actually the hard part that I  struggle with is how you obtain a member, or a pointer to member. PFR so far only returned tuples of references, and I still do not see how you get from there to the "member", or a pointer to one.

---

## Comment 4

> Username: schaumb  
> Created at: 2023-11-29 09:11:05 UTC  
> Updated at: 2024-01-03 15:22:54 UTC  
> Url: https://github.com/boostorg/pfr/issues/150#issuecomment-1831500004  

(There are no pointer-to-member in this method (I tried to get [here](https://github.com/boostorg/pfr/pull/77) but constexpr time will not work).)  
  
The simplified strategy is the following:  
  
- We create a not existing extern object [(code)](https://github.com/boostorg/pfr/blob/98789f610a8bab53d5523157ff6a3fac7606764a/include/boost/pfr/detail/fake_object.hpp#L22)  
  
```cpp  
template<class T>  
extern const T non_exists;  
```  
  
- We use the structure binding constexpr time on this object to get the members by their pointers [(code)](https://github.com/boostorg/pfr/blob/98789f610a8bab53d5523157ff6a3fac7606764a/include/boost/pfr/detail/core_name20_static.hpp#L189-L191)  
  
```cpp  
template<class T>  
constexpr auto get_members() {  
    // this example works only with 3 member  
    auto& [m1, m2, m3] = non_exists<T>;  
    return std::tuple{&m1, &m2, &m3};  
}  
```  
  
- We pass these objects to a template argument [(code)](https://github.com/boostorg/pfr/blob/98789f610a8bab53d5523157ff6a3fac7606764a/include/boost/pfr/detail/core_name20_static.hpp#L83-L84)  
  
```cpp  
template<class T, std::size_t I, auto* member = std::get<I>(get_members<T>())>  
auto get_name()  
```  
  
- We generates the function structure with compiler specific macro, and we process the textual representation which contains the `member` template argument with it's value [(code)](https://github.com/boostorg/pfr/blob/98789f610a8bab53d5523157ff6a3fac7606764a/include/boost/pfr/detail/core_name20_static.hpp#L92)  
```cpp  
{  
    __PRETTY_FUNCTION__ // this contains:  
   // member = &non_exists<the_type>.member_name  
}  
```  
  
So we "get" the name from the non-existent object's member address.  
This is works because the `non_exists` object is not used runtime and `T` class is not instantiated.  
  
---  
  
The library uses some workaround, but basically, this is under the hood

---

## Comment 5

> Username: akrzemi1  
> Created at: 2023-11-29 20:48:13 UTC  
> Url: https://github.com/boostorg/pfr/issues/150#issuecomment-1832677686  

>  We use the structure binding constexpr time on this object to get the members by their pointers [(code)](https://github.com/boostorg/pfr/blob/98789f610a8bab53d5523157ff6a3fac7606764a/include/boost/pfr/detail/core_name20_static.hpp#L189-L191)  
>   
>   
> ```c++  
> template<class T>  
> constexpr auto get_members() {  
>     // this example works only with 3 member  
>     auto& [m1, m2, m3] = non_exists<T>;  
>     return std::tuple{&m1, &m2, &m3};  
> }  
> ```  
  
I still don't get this part. This code only returns pointers (rather than pointers-to-members). `boost::pfr::detail::sequence_tuple::get` returns a reference, like `int const&`.  How can you turn it into a member pointer, or something that you could get a name from?

---

## Comment 6

> Username: denzor200  
> Created at: 2023-11-29 21:21:33 UTC  
> Updated at: 2023-11-29 21:23:39 UTC  
> Url: https://github.com/boostorg/pfr/issues/150#issuecomment-1832718966  

You right, only references in this code. And it's more than enough to obtain the name.  
Looks at this: https://godbolt.org/z/31T817Mef  
No pointer to members in the snippet, only regular pointer(of course, might be changed to reference).  
That's possible only since C++20, looks at the section "Template non-type arguments" here: https://en.cppreference.com/w/cpp/language/template_parameters

---

## Comment 7

> Username: akrzemi1  
> Created at: 2023-11-30 07:48:30 UTC  
> Url: https://github.com/boostorg/pfr/issues/150#issuecomment-1833260947  

Oh. Thank you. This is brilliant.  
I think this explanation is worth putting in the docs. This is very interesting on its own, and would contribute to Boost being not only good libraries but also the source to learn interesting programming techniques.

---

## Comment 8

> Username: denzor200  
> Created at: 2023-11-30 09:40:33 UTC  
> Url: https://github.com/boostorg/pfr/issues/150#issuecomment-1833412202  

Sure, article in the docs will be created later

---

## Comment 9

> Username: XRay3D  
> Created at: 2023-12-16 09:21:09 UTC  
> Updated at: 2023-12-16 09:30:05 UTC  
> Url: https://github.com/boostorg/pfr/issues/150#issuecomment-1858771444  

> There are no pointer-to-member in this method (I tried to get [here](https://github.com/boostorg/pfr/pull/77) but constexpr time will not work).  
  
  
https://godbolt.org/z/Kj9MKosz9  
Perhaps line 14 like this is faster at compile time.  
```cpp  
constexpr auto first = sv.find_last_of(":.>", last);  
```  
than  
```cpp  
constexpr auto first = sv.find_last_not_of("abcdefghijklmnopqrstuvwxyzABCDEFGHIJKLMNOPQRSTUVWXYZ_0123456789", last);  
```
