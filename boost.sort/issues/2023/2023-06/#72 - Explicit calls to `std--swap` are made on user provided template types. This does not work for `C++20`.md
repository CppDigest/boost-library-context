# #72 - Explicit calls to `std::swap` are made on user provided template types. This does not work for `C++20`. [Closed]

> Username: melton1968  
> Created at: 2023-06-06 21:04:22 UTC  
> Updated at: 2023-09-10 22:48:00 UTC  
> Closed at: 2023-09-10 22:48:00 UTC  
> Url: https://github.com/boostorg/sort/issues/72  

Starting with `C++20`, [fully specializing a function template](https://en.cppreference.com/w/cpp/language/extending_std) in the `std` namespace is undefined behavior. This precludes implementing `swap` for user-defined types in the standard namespace. You can find the reasoning in this [paper](https://www.open-std.org/jtc1/sc22/wg21/docs/papers/2017/p0551r0.pdf).  
  
I am running into this issue while using `boost::sort` in a project using `C++20`. I have implemented a free function `swap` for my type in the type's namespace, but since the library code is explicitly calling `std::swap`, it cannot find it.  If I implement the free function in `std`, the compiler chooses to ignore it, which is allowed under the standard.  
  
I changed the sort code to use the "`use std::swap` and then call `swap` unqualified" idiom, and everything worked as expected. For example:  
```c++  
std::swap(*iter1, *iter2);  
```  
becomes,  
```c++  
using std::swap;  
swap(*iter1, *ite2);  
```  
  
I can submit a PR for this change if that is appropriate.

---

## Comment 1

> Username: spreadsort  
> Created at: 2023-06-06 21:48:11 UTC  
> Url: https://github.com/boostorg/sort/issues/72#issuecomment-1579493830  

On Tue, Jun 6, 2023, 5:04 PM Mark Melton ***@***.***> wrote:  
  
> Starting with C++20, fully specializing a function template  
> <https://en.cppreference.com/w/cpp/language/extending_std> in the std  
> namespace is undefined behavior. This precludes implementing swap for  
> user-defined types in the standard namespace. You can find the reasoning in  
> this paper  
> <https://www.open-std.org/jtc1/sc22/wg21/docs/papers/2017/p0551r0.pdf>.  
>  
> I am running into this issue while using boost::sort in a project using  
> C++20. I have implemented a free function swap for my type in the type's  
> namespace, but since the library code is explicitly calling std::swap, it  
> cannot find it. If I implement the free function in std, the compiler  
> chooses to ignore it, which is allowed under the standard.  
>  
> I changed the sort code to use the "use std::swap and then call swap  
> unqualified" idiom, and everything worked as expected. For example:  
>  
> std::swap(*iter1, *iter2);  
>  
> becomes,  
>  
> using std::swap;swap(*iter1, *ite2);  
>  
> I can submit a PR for this change if that is appropriate.  
>  
Yes, please.  
  
  
—  
> Reply to this email directly, view it on GitHub  
> <https://github.com/boostorg/sort/issues/72>, or unsubscribe  
> <https://github.com/notifications/unsubscribe-auth/ABG2HXF2H4FOEKN7KVJHZ63XJ6LOFANCNFSM6AAAAAAY46UQUY>  
> .  
> You are receiving this because you are subscribed to this thread.Message  
> ID: ***@***.***>  
>

---

## Comment 2

> Username: nigels-com  
> Created at: 2023-09-10 07:51:24 UTC  
> Url: https://github.com/boostorg/sort/issues/72#issuecomment-1712742561  

Resolved in PR #73, by the look of it.

---

## Comment 3

> Username: spreadsort  
> Created at: 2023-09-10 22:48:00 UTC  
> Url: https://github.com/boostorg/sort/issues/72#issuecomment-1712961179  

Thanks Nigel!
