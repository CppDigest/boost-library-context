# #157 - Macro facilitating perfectly forwarded `this` pointers [Open]

> Username: badair  
> Created at: 2016-03-24 23:05:50 UTC  
> Updated at: 2016-04-11 16:59:25 UTC  
> Url: https://github.com/boostorg/hof/issues/157  

I thought of a potential Fit feature today when browsing [this r/cpp thread](https://www.reddit.com/r/cpp/comments/4b7zfe/a_possible_c_standard_proposal_on_overloading/).  
  
It would be nice sometimes to write the same function for all function qualifier overloads, especially for generic programming. The following macro, `FIT_OVERLOAD_ALL_QUALIFIERS`, facilitates this. The first argument to this macro is the user-facing member name, and the second argument is the static internal implementation, which takes a universal reference representing `*this`.  
  
``` cpp  
#include <type_traits>  
  
#define FIT_OVERLOAD_ALL_DETAIL(name, impl, qual) \  
template<typename... Args>                        \  
decltype(auto) name(Args&&... args) qual {        \  
    using this_t = std::remove_reference_t<       \  
        decltype(*this)                           \  
    >;                                            \  
    return impl(                                  \  
        static_cast<this_t qual>(*this),          \  
        static_cast<Args&&>(args)...              \  
    );                                            \  
}                                                 \  
/**/  
  
#define FIT_OVERLOAD_ALL_QUALIFIERS(name, impl)        \  
FIT_OVERLOAD_ALL_DETAIL(name, impl, &)                 \  
FIT_OVERLOAD_ALL_DETAIL(name, impl, &&)                \  
FIT_OVERLOAD_ALL_DETAIL(name, impl, const &)           \  
FIT_OVERLOAD_ALL_DETAIL(name, impl, const &&)          \  
FIT_OVERLOAD_ALL_DETAIL(name, impl, volatile &)        \  
FIT_OVERLOAD_ALL_DETAIL(name, impl, volatile &&)       \  
FIT_OVERLOAD_ALL_DETAIL(name, impl, const volatile &)  \  
FIT_OVERLOAD_ALL_DETAIL(name, impl, const volatile &&) \  
/**/  
  
//</library code>  
  
#include <iostream>  
  
struct foo {  
  
    FIT_OVERLOAD_ALL_QUALIFIERS(operator(), print_qualifiers)  
  
    template<typename ThisRef>  
    static void print_qualifiers(ThisRef&&) {  
        using no_ref = std::remove_reference_t<ThisRef>;  
  
        if (std::is_const<no_ref>{}) {  
            std::cout << "const ";  
        }  
  
        if (std::is_volatile<no_ref>{}) {  
            std::cout << "volatile ";  
        }  
  
        if (std::is_rvalue_reference<ThisRef&&>{}) {  
            std::cout << "&& ";  
        }  
        else if (std::is_lvalue_reference<ThisRef&&>{}) {  
            std::cout << "& ";  
        }  
  
        std::cout << '\n';  
    }  
};  
  
int main() {  
    using F = foo;  
    using CF = const foo;  
    using VF = volatile foo;  
    using VCF = const volatile foo;  
  
    F f{};  
    CF cf{};  
    VF vf{};  
    VCF vcf{};  
  
    f();  
    cf();  
    vf();  
    vcf();  
  
    F{}();  
    CF{}();  
    VF{}();  
    VCF{}();  
}  
```  
  
Output:  
  
```  
&   
const &   
volatile &   
const volatile &   
&&   
const &&   
volatile &&   
const volatile &&   
```  
  
I'd be happy to submit a PR with test cases and tweaks if there is further interest here.

---

## Comment 1

> Username: pfultz2  
> Created at: 2016-03-26 01:55:13 UTC  
> Url: https://github.com/boostorg/hof/issues/157#issuecomment-201661348  

I think this could be a good idea. A couple of things.   
  
It needs to keep the constraints of the static function, so using `FIT_RETURNS` needs to be used instead of `decltype(auto)`. On gcc 4.6 and 4.7, `FIT_CONST_THIS` and `FIT_THIS` has to be used because they don't handle `this` pointer in a decltype.  
  
Also, it should be `constexpr`. This might prove to be difficult on C++11 compilers since `constexpr` implies `const`, so it can't be overloaded. So, we might have to drop `constexpr` on C++11 compilers.  
  
> I'd be happy to submit a PR with test cases and tweaks if there is further interest here.  
  
Yes that would be great. However, I would wait a little since the library is under going a lot of changes right now due to the boost formal review. Once that settles down, a PR would be great. Thanks.

---

## Comment 2

> Username: badair  
> Created at: 2016-04-07 20:53:15 UTC  
> Url: https://github.com/boostorg/hof/issues/157#issuecomment-207083094  

Let me know whenever you're ready for the PR. I'll use the macros you mentioned and add test cases/documentation.  
  
P.S. Congratulations on surviving the review process. I hope Fit makes it into Boost next time.

---

## Comment 3

> Username: pfultz2  
> Created at: 2016-04-11 16:59:25 UTC  
> Url: https://github.com/boostorg/hof/issues/157#issuecomment-208449559  

Thanks, I am going to be making a few API breaking changes in the next month or so due to feedback from the review, and then I'll let you know when its safe to submit the pull request.
