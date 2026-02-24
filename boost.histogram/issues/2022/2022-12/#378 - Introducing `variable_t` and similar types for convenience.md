# #378 - Introducing `variable_t` and similar types for convenience [Open]

> Username: HDembinski  
> Created at: 2022-12-24 13:46:44 UTC  
> Updated at: 2022-12-27 17:24:19 UTC  
> Url: https://github.com/boostorg/histogram/issues/378  

@HDembinski Here are some examples why using `variable_t` can bring convenience.  
  
```C++  
std::is_same_v<  
    boost::histogram::axis::variable<double, std::string, boost::use_default>,  
    boost::histogram::axis::variable<double, std::string, decltype(boost::histogram::axis::option::underflow | boost::histogram::axis::option::overflow)>  
>  
```  
is `false`,  
```C++  
std::is_same_v<  
    boost::histogram::axis::variable_t<double, std::string, boost::use_default>,  
    boost::histogram::axis::variable_t<double, std::string, decltype(boost::histogram::axis::option::underflow | boost::histogram::axis::option::overflow)>  
>  
```  
is `true`;  
  
---  
  
This causes a compile error:  
```C++  
boost::histogram::axis::variable<double, std::string, boost::use_default> a;  
boost::histogram::axis::variable<double, std::string, decltype(boost::histogram::axis::option::underflow | boost::histogram::axis::option::overflow)> b;  
a = b;  
```  
This does not cause a compile error:  
```C++  
boost::histogram::axis::variable_t<double, std::string, boost::use_default> a;  
boost::histogram::axis::variable_t<double, std::string, decltype(boost::histogram::axis::option::underflow | boost::histogram::axis::option::overflow)> b;  
a = b;  
```  
  
---  
  
In templates, you can get the meta type, options type, allocator type conveniently if template users write `variable_t`, not if template users write `variable`:  
```  
template<typename V, typename M, typename O, typename A>  
void f(boost::histogram::axis::variable<V, M, O, A>)  
{  
    std::cout << typeid(V).name() << '\n';  
    std::cout << typeid(M).name() << '\n';  
    std::cout << typeid(O).name() << '\n';  
    std::cout << typeid(A).name() << '\n';  
}  
  
int main(int argc, char *argv[])  
{  
    f(boost::histogram::axis::variable<>());  
    //double  
    //struct boost::use_default  
    //struct boost::use_default  
    //class std::allocator<double>  
  
    f(boost::histogram::axis::variable_t<>());  
    //double  
    //class std::basic_string<char,struct std::char_traits<char>,class std::allocator<char> >  
    //struct boost::histogram::axis::option::bitset<3>  
    //class std::allocator<double>  
}  
```  
  
---  
  
In debugger, you can not see what options `a` has but can see what options `b` has:  
![image](https://user-images.githubusercontent.com/5171262/209428440-790b9979-b09b-475a-b231-45c61ae69506.png)  
```C++  
boost::histogram::axis::variable<> a;  
boost::histogram::axis::variable_t<> b;  
```  
  
_Originally posted by @jhcarl0814 in https://github.com/boostorg/histogram/issues/372#issuecomment-1364494250_

---

## Comment 1

> Username: HDembinski  
> Created at: 2022-12-24 13:47:33 UTC  
> Updated at: 2022-12-24 13:47:46 UTC  
> Url: https://github.com/boostorg/histogram/issues/378#issuecomment-1364533006  

> @HDembinski Here are some examples why using `variable_t` can bring convenience.  
  
@jhcarl0814   
Ok, thanks, now I got it. If we can make this change without breaking the API, then I am all for it. In other words, I do not want to introduce `variable_t`, but use `variable` as the template alias and call the underlying struct variable_t. If that's not breaking any existing code.

---

## Comment 2

> Username: jhcarl0814  
> Created at: 2022-12-24 22:29:18 UTC  
> Updated at: 2022-12-26 08:52:26 UTC  
> Url: https://github.com/boostorg/histogram/issues/378#issuecomment-1364590904  

@HDembinski Sorry I have to mention https://www.hyrumslaw.com/ again. We need to know how many users are relying on `variable`'s type template parameters staying in a deduced context. If few people are using this, then it's safe to start the transition to the new approach.  
  
e.g.  
  
Some old code that `are relying on variable's type template parameters staying in a deduced context`:  
```C++  
template<typename T = int>  
struct variable  
{  
    std::conditional_t<std::is_same_v<T, void>, int, T> data;  
};  
  
template<typename T>  
void f1(variable<T>)  
{  
}  
void f2()  
{  
    f1(variable<int>());  
}  
```  
When replace `struct variable` with `struct variable_t`+`using variable`, template deduction stops working:  
```C++  
template<typename T = int>  
struct variable_t  
{  
    T data;  
};  
template<typename T = int>  
using variable = variable_t<std::conditional_t<std::is_same_v<T, void>, int, T>>;  
  
template<typename T>  
void f1(variable<T>) // Candidate template ignored: couldn't infer template argument 'T'  
{  
}  
void f2()  
{  
    f1(variable<int>()); // No matching function cal to 'f1'  
}  
```  
So require every user to change code to:  
```C++  
template<typename T = int>  
struct variable_t  
{  
    T data;  
};  
template<typename T = int>  
using variable = variable_t<std::conditional_t<std::is_same_v<T, void>, int, T>>;  
  
template<typename T>  
void f1(variable_t<T>) ///////////////////////////////////////  
{  
}  
void f2()  
{  
    f1(variable<int>());  
}  
```  
  
---  
  
All other non-generic code (e.g. `void f1(variable_t<>)`) and generic code that don't care what's inside `variable`'s template parameter list (e.g. `void f1(T)`) are not affected.

---

## Comment 3

> Username: jhcarl0814  
> Created at: 2022-12-26 09:01:44 UTC  
> Url: https://github.com/boostorg/histogram/issues/378#issuecomment-1365013434  

@HDembinski The type alias **should work** with template deduction guides, it's **just not working now**.  
According to https://en.cppreference.com/w/cpp/language/class_template_argument_deduction#Deduction_for_alias_templates ,  
  
- for each constructor of `variable`:  
  - the implicitly-generated deduction guides for `variable_t` is like  
  
```C++  
template<  
    /*only `variable_t`'s definition's template parameters that appear in the deduction result*/,  
    /*the constructor's template parameters that are not deduced from template arguments given in `variable_t`'s definition*/>  
variable(/*the constructor's function parameters (the constructor's template parameters that are deduced from template arguments given in `variable_t`'s definition are replaced with the deduction result)*/)  
->  
variable<  
    std::conditional_t<std::is_same_v<Value, use_default>, double, Value>,  
    std::conditional_t<std::is_same_v<MetaData, use_default>, std::string, MetaData>,  
    std::conditional_t<std::is_same_v<Options, use_default>, decltype(option::underflow | option::overflow), Options>,  
    std::conditional_t<std::is_same_v<Allocator, use_default>, std::allocator<std::conditional_t< std::is_same_v<Value, use_default>, double, Value>>, Allocator>>;  
  
// e.g.  
  
template</*variable_t's*/class Value, /*variable_t's*/class MetaData, /*variable_t's*/class Options, /*variable_t's*/class Allocator, /*variable's*/class U>  
variable(  
    std::initializer_list<U>,  
    typename boost::histogram::axis::metadata_base<  
        std::conditional_t<  
            std::is_same_v<std::conditional_t<std::is_same_v<MetaData, use_default>, std::string, MetaData>, boost::use_default>,  
            std::string,  
            std::conditional_t<std::is_same_v<MetaData, use_default>, std::string, MetaData>>,  
        (std::is_empty_v<std::conditional_t</* ... */>> && std::is_final_v<std::conditional_t</* ... */>>)  
    >::metadata_type,  
    std::conditional_t<  
        std::is_same_v<std::conditional_t<std::is_same_v<Options, use_default>, decltype(option::underflow | option::overflow), Options>, boost::use_default>,  
        boost::histogram::axis::option::bitset<3>,  
        std::conditional_t<std::is_same_v<Options, use_default>, decltype(option::underflow | option::overflow), Options>>,  
    std::conditional_t<std::is_same_v<Allocator, use_default>, std::allocator<std::conditional_t< std::is_same_v<Value, use_default>, double, Value>>, Allocator>>  
)->  
variable<  
    std::conditional_t<std::is_same_v<Value, use_default>, double, Value>,  
    std::conditional_t<std::is_same_v<MetaData, use_default>, std::string, MetaData>,  
    std::conditional_t<std::is_same_v<Options, use_default>, decltype(option::underflow | option::overflow), Options>,  
    std::conditional_t<std::is_same_v<Allocator, use_default>, std::allocator<std::conditional_t< std::is_same_v<Value, use_default>, double, Value>>, Allocator>  
>;  
  
// U is in a deduced context and is deduced to float/double/long double etc,  
// Value is in a non-deduced context and uses default template argument (double) in the end  
```  
  
- for each user-defined deduction guide of `variable`: **(I don't know why the compiler ignores all user-defined deduction guides of `variable`, looks like a compiler bug)**  
  - the user-defined deduction guides for `variable_t` is like  
  
```C++  
template<  
    /*only `variable_t`'s definition's template parameters that appear in the deduction result*/,  
    /*the deduction guide's template parameters that are not deduced from template arguments given in `variable_t`'s definition*/>  
variable(/*the deduction guide's function parameters (the deduction guide's template parameters that are deduced from template arguments given in `variable_t`'s definition are replaced with the deduction result)*/)  
->  
variable</*the deduction guide's template arguments (the deduction guide's template parameters that are deduced from template arguments given in `variable_t`'s definition are replaced with the deduction result)*/>;  
  
// e.g.  
  
template </* none of variable_t's template parameters appear in the deduction result */ /*variable's*/class T>  
variable(std::initializer_list<T>) -> variable<detail::convert_integer<T, double>, null_type>;  
  
// T is in a deduced context and is deduced to float/double/long double etc,  
// so we get variable<float/double/long double, null_type>  
```  
  
[`variable_t`'s type template parameters are in non-deduced context in compiler generated deduction guides **(I don't know why the compiler only processes implicitly-generated deduction guides of `variable` and ignores all user-defined deduction guides of `variable`)**, as a result `variable_t`'s type template parameters' default arguments are always used](https://godbolt.org/#z:OYLghAFBqd5QCxAYwPYBMCmBRdBLAF1QCcAaPECAMzwBtMA7AQwFtMQByARg9KtQYEAysib0QXACx8BBAKoBnTAAUAHpwAMvAFYTStJg1DIApACYAQuYukl9ZATwDKjdAGFUtAK4sGIAGykrgAyeAyYAHI%2BAEaYxCCSZqQADqgKhE4MHt6%2BASlpGQKh4VEssfGJtpj2jgJCBEzEBNk%2BfoF2mA6Z9Y0ExZExcQlJCg1NLbntY31hA2VDiQCUtqhexMjsHCYaAILmAMxhyN5YANQm%2B27RqGkEAPQIeKOowMSsd0yqTwB0CMnJF2w2z2ZkODGOXjOFyuN1GdwIAE9kpgAPphLCqX7/QHAg5HE6YaFOUbETCsHG7YHMNgKZJMDana63c4Adisu2pmFp9Mwp0ez1erFZ7J2nO5DM%2BT2FwNOstOBEwLGSBgV0OOTAUClOADUxF5USiUed9gARU7oVbReikU7qzWnACymAaJqYDUNRouZq8ShRWCoTC8tAINrtWoA8slagwFB7jd7ff7A8HQwZ7TtaLRUKIiMQ417TqN0CAQGIszmSNDdd4DSicfsgbs5acfWFgKcAG6NPBMK2ogjxxmwggl/lEQUsEuShQlrvEHt96Ey5tyrANOgl0nKnl%2BzABoMEKt62s2i1eRcN0jLlfm51MDcgLcGDa7/fB6FOl1upgem1Fkskm2gJXk2N63uutCbpg24vkmB7QpG0axoap6dMGSKYBAqBRpkJZeAwWDEFQWYAO6sm414rth0YlqgHZxMRqAkYswHgfekGPtBz6onB76XBm5ZuiQv6Uc2/6lpm2ZCcQ0JruxUEwTxe7JoelzVvqv7mpa9D1kCDb1iKJgsmadx3KcYp0hKXwKLixmnKZ5msFylm8mOLxvCwtkmWZFk8kOty4rsoxungyAohqShNBAhrIP8u7oF4XQCCiwBeHgWBavWZpmBoXAAJwaCywQsfshm7GEA4sPeDAQCxlJspRRkimB/mjKOTzjh5U7WbO3a9jplyAqcTBcBcFiFgQxYoKsqkUZcTJtSAiLIuimDqEtGFougKIkYQCAosgHYyZcWDHMtmEjSV2C1d8ySkgQiIopytVLpcYBbAArG4DDvWNolygtI4gG5E7dU8vXzv1/avdgjKjaVE1TWgXizdCgMludq3redW07XtB1HbJaHnRA0RcFdN13c6j3PSVc1uL9X0/VsCPicjqNvZ932/aVgU7C16PAx17msGDM4gHOC70MNZhjYjJbs69MK3BjGFY6ryK47tBD7Ydx1uKd6HIhATBmBTiy3fdNNOS9g2cyYTM881YGCyDXWlj1Et9X2KIDtEssI/L00o0rguYwRa0a6i6V4zrBP64bJP%2B%2BblvUwiT023T0KM9zLPjWzM1KznzN/ZSoE3q7wugx74Ne5DfbDfsEBNVwpBJPsRkmiV%2BeTQrhd28ri3hxiUda/jetE2dGEm/sKdUw96e00XXMl7z5crpXAru9OENS/2jJNy3bekB3xnd7KBchwPYdqxH2ObTH2u64TJ3E9P0Sz4ClNW4vmfLw7uc5aXw5gzFeTs%2BYC2HO1Leosa7i0llDYakhm5si4N8DQVA27oMwfsbBndz7ANDlAjaK076j0fuPF%2BBs37GyYJIOeP8M5sFtvTYu4D17Nk3p1WBO865719oyZBLdsFYIwSfPBZ85ZB0VtfYhw9I4kOjttJ%2B8dJ5G0wtEehX8Lbz2tswrO9tHZ52kf3VhYC84QJdsQt2PDPYIIbkwD6KCLBoI0KI8RGh8FAN7sHEBN9SEj0UWPOOE9X5T1oR9BhacmGYQMaAgBq9nYV2sVXbedjvb0AEdEJxwi3FmHQR4rxgdCGyJVoo9WQSKEhKoYnd%2BkTtGpwXjElh2dzHeKRqY1pCT2F7A4QDFJMDJxwN3ogpg/hnGuNoKIqZuCNC0CKT3DpV96b%2BMwBUnGVTn4JxoRdfwUSmlLwHmwixfTZRcJFkM3h9jMl%2B3GbkqZ%2BS5kePmZI4pPiZErLkbfQJGzlGUO2eEjReyGm6N/vo/%2BRj2l92WV0yFa9%2BZyk7l5YEHBliQQ4B9XgfgOBaFIKgTgFFLDWELKsdYvIDg8FICOHFqLlgAGsQAfTceiyQvBJwfQABzfA%2BvsfwnKeV8o5QKj60hsW4vxRwXgM43HUq0MsOAsAkBoCVHQOI5BKDKuSKq%2BIwAFDMGSAoBAqAQxYA7KFTA2o8CYBIpGRgnBKU0GDHEGcpNNC8GiGERoCJ7W8GVWwQQ4YGC0G9TS0gWAqpGHEKG/ApIuj0RnKGtanQUabEpRVaobr9B4GiG8YgCIPBYEzQQeck5uCor4AYXVlrrW2uxQ62QIgxDsCkDIQQigVDqFDboVuBgjAoGsNYLN0QZywE5CAQGpB6LxG4ByuVyxqKZATQAWiLF6UwRLLCglOEu8MZht0AHUyzbuROgQwjhkDHp1qSJg6ApXVE6DRCArgJh%2BFbiEWYpRyh6FSOkaML7v0FGjP0T9QxW4dCSgwHo4xPCtD0OB6MUGZglEGPEMD0x/1od6MBlDEhlgKFJRsXD%2BhOCYtIGK3gErTiqA5f4Jd/hJCnGAMgC9EBi34TpYsU4EBcCEBIOcUE5NeCyvLRAJASbkAoxIOqk2xBdXKEMNUIQRqSJ1t9agFVKpMjyfCLQJTTFyOkE1dqkAur9WGuNYZ9TWr6DEHDCjPTKnM3iZ2LJhNvBxP1HwNi3g/BBCNvEC23z8glBqEzd2/QhhjADssEOkdUAx0Tqnfa2dtK8U4QEMu1dpp11WE3fsbdu6D1HqXSes9oVL0IGvbelYaxCNoa89pxTynVNUtJKm3gJE3jJB9WikjWLM0SuwKoZNuYqM0bowxpjLG2MMA41xwluWbCnB46NiliwhNusWMsBAZJCKUF6xwVlpBJySDQZIfw%2BxTvncu1IC752yMDc4FKvQwmtukAZVwDQzLOD7H66GiVG2aVvanekZwkggA%3D):  
```C++  
  
#include <boost/histogram/axis.hpp>  
#include <boost/type_index.hpp>  
#include<iostream>  
  
namespace boost {  
namespace histogram {  
namespace axis {  
    template<class Value___ = double, class MetaData___ = use_default, class Options___ = use_default, class Allocator___ = std::allocator<Value___>>  
    using variable_t = boost::histogram::axis::variable<  
        detail::replace_default<Value___, double>,  
        detail::replace_default<MetaData___, std::string>,  
        detail::replace_default<Options___, decltype(option::underflow |  
        option::overflow)>, detail::replace_default<Allocator___,  
        std::allocator<detail::replace_default<Value___, double>>>>;  
} // namespace axis  
} // namespace histogram  
} // namespace boost  
  
static_assert(__cpp_deduction_guides >= 201907L);  
  
int main()  
{  
    {  
        boost::histogram::axis::variable<> a1; std::cout<<boost::typeindex::type_id_with_cvr<decltype(a1)>().pretty_name()<<'\n';  
        boost::histogram::axis::variable_t<> b1; std::cout<<boost::typeindex::type_id_with_cvr<decltype(b1)>().pretty_name()<<'\n'; std::cout<<'\n';  
  
        boost::histogram::axis::variable a2; std::cout<<boost::typeindex::type_id_with_cvr<decltype(a2)>().pretty_name()<<'\n';  
        boost::histogram::axis::variable_t b2;  std::cout<<boost::typeindex::type_id_with_cvr<decltype(b2)>().pretty_name()<<'\n'; std::cout<<'\n';  
  
        boost::histogram::axis::variable a3({1,2,3}); std::cout<<boost::typeindex::type_id_with_cvr<decltype(a3)>().pretty_name()<<'\n';  
        boost::histogram::axis::variable_t b3({1,2,3});  std::cout<<boost::typeindex::type_id_with_cvr<decltype(b3)>().pretty_name()<<'\n'; std::cout<<'\n';  
  
        boost::histogram::axis::variable a4({1.0f,2.0f,3.0f}); std::cout<<boost::typeindex::type_id_with_cvr<decltype(a4)>().pretty_name()<<'\n';  
        boost::histogram::axis::variable_t b4({1.0f,2.0f,3.0f});  std::cout<<boost::typeindex::type_id_with_cvr<decltype(b4)>().pretty_name()<<'\n'; std::cout<<'\n';  
  
        boost::histogram::axis::variable a5({1.0,2.0,3.0}); std::cout<<boost::typeindex::type_id_with_cvr<decltype(a5)>().pretty_name()<<'\n';  
        boost::histogram::axis::variable_t b5({1.0,2.0,3.0});  std::cout<<boost::typeindex::type_id_with_cvr<decltype(b5)>().pretty_name()<<'\n'; std::cout<<'\n';  
  
        boost::histogram::axis::variable a6({1.0l,2.0l,3.0l}); std::cout<<boost::typeindex::type_id_with_cvr<decltype(a6)>().pretty_name()<<'\n';  
        boost::histogram::axis::variable_t b6({1.0l,2.0l,3.0l});  std::cout<<boost::typeindex::type_id_with_cvr<decltype(b6)>().pretty_name()<<'\n'; std::cout<<'\n';  
    }  
}  
```  
  
---  
  
PS:  
`Boost.Parameter` allows omiting argument in the middle (by requiring users to specify parameters' names),  but also does not address the issue: users also get different types by passing different template argument list, so it only verifies that "member types are correct" instead of "type template parameters are correct" at the end of the `class_` example. (https://www.boost.org/doc/libs/release/libs/parameter/doc/html/index.html#named-template-parameters )

---

## Comment 4

> Username: HDembinski  
> Created at: 2022-12-26 09:11:51 UTC  
> Updated at: 2022-12-26 13:08:34 UTC  
> Url: https://github.com/boostorg/histogram/issues/378#issuecomment-1365022855  

> @HDembinski Sorry I have to mention https://www.hyrumslaw.com/ again. We need to know how many users are relying on `variable`'s type template parameters staying in a deduced context. If few people are using this, then it's safe to start the transition to the new approach.  
  
I am well aware of Hyrum's law, that's why I stated my preference so carefully. Okay, so replacing the struct name with an alias name does not work. :(  
  
Do you think it is still useful to introduce variable_t in addition to variable? And likewise for every axis?

---

## Comment 5

> Username: HDembinski  
> Created at: 2022-12-26 13:10:53 UTC  
> Url: https://github.com/boostorg/histogram/issues/378#issuecomment-1365157025  

> (I don't know why the compiler only processes implicitly-generated deduction guides of variable and ignores all user-defined deduction guides of variable)  
  
That seems to be an issue with gcc only, the other compilers use the deduction guides.  
  
I implemented some workarounds just recently here https://github.com/boostorg/histogram/pull/373 to fix gcc>=11.

---

## Comment 6

> Username: HDembinski  
> Created at: 2022-12-26 13:25:04 UTC  
> Updated at: 2022-12-26 13:27:26 UTC  
> Url: https://github.com/boostorg/histogram/issues/378#issuecomment-1365171676  

In my opinion, the current version is better than the `variable_t` one, the type is much more readable.  
```c++  
boost::histogram::axis::variable<double, boost::use_default, boost::use_default, std::allocator<double> >  
boost::histogram::axis::variable<double, std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> >, boost::histogram::axis::option::bitset<3u>, std::allocator<double> >  
```  
  
You should not make type checks like this anyway (what is the use case for such type checks)?  
```c++  
std::is_same_v<  
    boost::histogram::axis::variable<double, std::string, boost::use_default>,  
    boost::histogram::axis::variable<double, std::string, decltype(boost::histogram::axis::option::underflow | boost::histogram::axis::option::overflow)>  
>  
```  
If you want to know what the options are, call `options()`, etc.

---

## Comment 7

> Username: HDembinski  
> Created at: 2022-12-26 13:29:01 UTC  
> Updated at: 2022-12-26 13:30:14 UTC  
> Url: https://github.com/boostorg/histogram/issues/378#issuecomment-1365179257  

The main point you have in favor is:  
> This causes a compile error:  
  
```c++  
boost::histogram::axis::variable<double, std::string, boost::use_default> a;  
boost::histogram::axis::variable<double, std::string, decltype(boost::histogram::axis::option::underflow | boost::histogram::axis::option::overflow)> b;  
a = b;  
```  
  
> This does not cause a compile error:  
  
```c++  
boost::histogram::axis::variable_t<double, std::string, boost::use_default> a;  
boost::histogram::axis::variable_t<double, std::string, decltype(boost::histogram::axis::option::underflow | boost::histogram::axis::option::overflow)> b;  
a = b;  
```  
  
This could be fixed with a converting ctor.

---

## Comment 8

> Username: jhcarl0814  
> Created at: 2022-12-26 21:37:42 UTC  
> Url: https://github.com/boostorg/histogram/issues/378#issuecomment-1365475401  

@HDembinski You are right. It's not transparent to switch to the type alias (and I forgot **there's no CTAD for template type alias during [C++17, C++20)**). So I'd better only use it in my own projects when need it.  
  
---  
  
The tricky part is, there are already so many places scattered around various libraries (e.g. standard library) that uses the "same type" type trait/concept that:  
  
>  You only know there is a landmine when you step on it.  
  
[searching "same type](https://www.google.com/search?q=%22same+type%22+site%3Acppreference.com)" can give us an impression of how many places depend on the "same type" type trait/concept in standard library **(maybe the doc should add a note to warn users so they will not shoot themselves in the foot?)**:  
  
- (`operator=`, could be fixed with a converting constructor)  
- (`std::is_same` and `std::same_as` gives unexpected result, other things might depend on them)  
- (`std::is_base_of` gives unexpected result, other things might depend on them)  
- (`std::common_type` gives unexpected result, other things might depend on them)  
- `std::variant`, users can not `get`/`visit` using `t<boost::use_default>` if it stores `t<actual_type>`, can not `get`/`visit` using `t<actual_type>` if it stores `t<boost::use_default>`  
- `std::type_info::operator==`, `std::type_info::hash_code` and `std::any`, when you use these kind of things it probably means you are passing type-erasured objects across lib boundaries, the receivers can not `any_cast` using ... if ..., can not `any_cast` using ... if ...  
- `reinterpret_cast`, also often used in type erasure, `informally, two types are similar if, ignoring top-level cv-qualification: they are the same type; or ...`  
- `typedef`, `once declared, a typedef-name may only be redeclared to refer to the same type again`  
- `implicit conversions`, the pointer and pointer-to-member conversions stops working and can't be fixed because pointers require that a non-temporary object actually exists (can't point to converting constructor generated temporary)  
- `implicit conversions`, you can only implicit convert once (two or more times are not supported), "using a converting constructor" uses up a turn for this  
- `function declaration`, `if there are multiple return statements, they must all deduce to the same type`  
- `std::is_layout_compatible`, `similar types are not layout-compatible if they are not the same type after ignoring top-level cv-qualification`

---

## Comment 9

> Username: HDembinski  
> Created at: 2022-12-27 17:19:45 UTC  
> Updated at: 2022-12-27 17:24:19 UTC  
> Url: https://github.com/boostorg/histogram/issues/378#issuecomment-1366057774  

Thanks for listing all that. I agree with you in general, but most of these caveats do not seem to matter for Boost.Histogram. In practice, it does not seem to be an issue.  
  
- We must use our own axis::variant instead of std::variant anyway.  
- You shouldn't use `std::is_same` with axis types. We have axis traits to write code which works with any axis.  
- `std::common_type` applied to axis types makes no sense.  
- We don't use `std::type_info` in this way and the library does not use type erasure for axis types.  
  
etc.  
  
That being said, I am not against supporting `<axis>_t` in addition to `<axis>` on C++17 and higher. This can be hidden behind an `#if`
