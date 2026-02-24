# #5 - Manual type registering/structured bindings might be unnecessary [Closed]

> Username: brunocodutra  
> Created at: 2016-12-24 16:36:32 UTC  
> Updated at: 2017-01-05 18:01:09 UTC  
> Closed at: 2017-01-05 18:01:09 UTC  
> Url: https://github.com/boostorg/pfr/issues/5  

It occurred to me that it is possible to implement `as_tuple` in C++14 very easily using the visitor pattern, with the added benefit of supporting pointers, `enum`s and non primitive types out of the box.  
  
Following is a rough implementation of the idea, which outputs:  
  
`{42, a, 3.1, (nil), green, hello world!}`  
  
for   
  
```.cpp  
as_tuple(reg{42, 'a', 3.1, nullptr, color::green, "hello world!"}, [](auto&& t) {  
    print_tuple(std::forward<decltype(t)>(t));  
    std::cout << std::endl;  
});  
```  
  
where   
  
```.cpp  
struct reg {  
    int a;  
    char b;  
    double d;  
    void* e;  
    color f;  
    std::string g;  
};  
```  
  
and   
  
```.cpp  
enum class color {  
    red,  
    green,  
    blue  
};  
  
std::ostream& operator <<(std::ostream& os, color c) {  
    switch(c) {  
        case color::red:  
            os << "red";  
            break;  
        case color::green:  
            os << "green";  
            break;  
        case color::blue:  
            os << "blue";  
            break;  
    };  
  
    return os;  
}  
```  
  
**Note:** it only works on Clang + libc++ due to the `reinterpret_cast` to `std::tuple`.  
  
```.cpp  
#include <tuple>  
#include <cstdint>  
#include <utility>  
#include <iostream>  
#include <type_traits>  
#include <initializer_list>  
  
template<typename...>  
using void_t = void;  
  
template<typename>  
struct type {};  
  
template<std::size_t>  
struct ubiq  
{  
    template<typename T>  
    operator T() const;  
};  
  
  
template<typename, typename, typename = void>  
struct _size_impl  
{};  
  
template<typename T, std::size_t head, std::size_t... tail>  
struct _size_impl<T, std::index_sequence<head, tail...>,  
    void_t<decltype(T{ubiq<head>{}, ubiq<tail>{}...})>  
> :  
    std::integral_constant<std::size_t, sizeof...(tail) + 1>  
{};  
  
template<typename T, std::size_t head, std::size_t... tail, typename _>  
struct _size_impl<T, std::index_sequence<head, tail...>, _> :  
    _size_impl<T, std::index_sequence<tail...>>  
{};  
  
template<typename T>  
struct _size :  
    _size_impl<T, std::make_index_sequence<sizeof(T)>>  
{};  
  
template<typename T>  
using size = typename _size<T>::type;  
  
template<typename F>  
struct deducer  
{  
    F f;  
  
    template<typename T>  
    operator T() const {  
        f(type<T>{});  
        return {};  
    }  
};  
  
template<typename F>  
deducer<std::decay_t<F>> deduce(F&& f) {  
    return {std::forward<F>(f)};  
}  
  
template<typename F, typename T, typename... Props>  
auto as_tuple_impl(F&& f, T&& t, type<Props>...)  
    -> std::enable_if_t<size<std::decay_t<T>>::value != sizeof...(Props)> {  
    std::decay_t<T>{  
        Props{}...,  
        deduce(  
            [&f, &t](auto prop) {  
                as_tuple_impl(  
                    std::forward<F>(f),  
                    std::forward<T>(t),  
                    type<Props>{}...,  
                    prop  
                );  
            }  
        )  
    };  
}  
  
template<typename F, typename T, typename... Props>  
auto as_tuple_impl(F&& f, T&& t, type<Props>...)  
    -> std::enable_if_t<size<std::decay_t<T>>::value == sizeof...(Props)> {  
    std::forward<F>(f)(reinterpret_cast<std::tuple<Props...>&&>(t));  
}  
  
template<typename F, typename T, typename... Props>  
auto as_tuple_impl(F&& f, T& t, type<Props>...)  
    -> std::enable_if_t<size<T>::value == sizeof...(Props)> {  
    std::forward<F>(f)(reinterpret_cast<std::tuple<Props...>&>(t));  
}  
  
template<typename T, typename F>  
auto as_tuple(T&& t, F&& f) {  
    as_tuple_impl(std::forward<F>(f), std::forward<T>(t));  
}  
  
template<typename... R, std::size_t h, std::size_t... t>  
void print_tuple_impl(std::tuple<R...> const& r, std::index_sequence<h, t...>) {  
    std::cout << '{' << std::get<h>(r);  
    std::initializer_list<int>{  
        (void(std::cout << ", " << std::get<t>(r)), 0)...  
    };  
    std::cout << '}';  
}  
  
template<typename... T>  
void print_tuple(std::tuple<T...> const& t) {  
    print_tuple_impl(t, std::index_sequence_for<T...>{});  
}  
```

---

## Comment 1

> Username: brunocodutra  
> Created at: 2016-12-24 16:43:58 UTC  
> Url: https://github.com/boostorg/pfr/issues/5#issuecomment-269091399  

Also note that it is intrinsically variadic and does not require any script generated code.

---

## Comment 2

> Username: apolukhin  
> Created at: 2016-12-24 19:56:41 UTC  
> Url: https://github.com/boostorg/pfr/issues/5#issuecomment-269098154  

Super awesome! I will need to experiment with your approach.  
  
But it does not 100% replace the structured bindings. The later could be used in constant expressions, but your approach has a `reinterpret_cast` that is unusable within `constexpr` function.

---

## Comment 3

> Username: brunocodutra  
> Created at: 2016-12-24 20:11:09 UTC  
> Url: https://github.com/boostorg/pfr/issues/5#issuecomment-269098569  

> The later could be used in constant expressions, but your approach has a `reinterpret_cast` that is unusable within `constexpr` function.  
  
That's a good point, I completely ignored constant expressions in that snippet for the sake of simplicity.  
  
It is not yet obvious to me how to work around the cast, but there must be a way. I'll think about it and let you know if I get anywhere.

---

## Comment 4

> Username: brunocodutra  
> Created at: 2016-12-25 12:54:00 UTC  
> Updated at: 2016-12-25 13:29:57 UTC  
> Url: https://github.com/boostorg/pfr/issues/5#issuecomment-269121519  

I've been thinking about `constexpr` in this context and realized it doesn't make sense, because here the `tuple` is made available as the argument to a function and, as such, it loses the ability to be explicitly marked `constexpr`.  
  
The following silly example illustrates this argument  
  
```.cpp  
template<typename T>  
constexpr T identity(T i) {  
    constexpr T j = i;  
    return j;  
}  
```  
  
```.cpp  
constexpr auto i = 42;           // regardless of the fact `i` is `constexpr`  
static_assert(identity(i) == i); // error: constexpr variable 'j' must be initialized by a constant expression  
```  
  
I'm not an expert in the internals of compilers ~~nor have I checked the assembly generated~~, but I see no reason why a decent compiler wouldn't be able to elide all those hoops regardless of the fact we can't explicitly assign the `tuple` to a `constexpr` variable, so I wonder what advantage one would gain from it.  
  
**Update:** indeed Clang seems to be able to completely elide the *magic get*  
  
```.cpp  
// main.cpp  
int main() {  
    color c = color::red;  
    as_tuple(reg{42, 'a', 3.1, nullptr, color::green}, [&c](auto&& t) {  
        c = std::get<4>(t);  
    });  
  
    return c == color::green;  
}  
```  
  
```.sh  
clang -S main.cpp -std=c++1z -stdlib=libc++ -O2  
```  
  
Produces  
  
```  
main:                                   # @main  
        .cfi_startproc  
# BB#0:  
        movl    $1, %eax  
        retq  
.Lfunc_end0:  
```

---

## Comment 5

> Username: apolukhin  
> Created at: 2016-12-25 13:53:35 UTC  
> Url: https://github.com/boostorg/pfr/issues/5#issuecomment-269123698  

It's not he matter of optimization, it's the matter of functionality. Theoretically structured bindings in magic_get give you an ability to write following things:  
```  
template <auto F>  
void do_something() {  
    static_assert(get<3>(F) == 42); // works with any aggregate  
};  
```  
So your technique is awesome for improving the C++14 functionality, but does not replace the C++17 functionality.

---

## Comment 6

> Username: brunocodutra  
> Created at: 2016-12-25 14:51:52 UTC  
> Url: https://github.com/boostorg/pfr/issues/5#issuecomment-269125675  

Right, but such an usage may or may not be deemed a *practical advantage*.  
  
Moving on to the *undefined behaviorness* of `reinterpret_cast`ing unrelated types even if they are layout compatible, couldn't it be worked around using an `union` and relying on the fact that  
  
> In a standard-layout union with an active member of non-union class type T1, it is permitted to read a non-static data member m of another union member of non-union class type T2 provided m is part of the common initial sequence of T1 and T2 (except that reading a volatile member through non-volatile glvalue is undefined).  
  
(ref: http://en.cppreference.com/w/cpp/language/data_members#Standard_layout)

---

## Comment 7

> Username: apolukhin  
> Created at: 2016-12-25 18:17:40 UTC  
> Url: https://github.com/boostorg/pfr/issues/5#issuecomment-269132485  

Did nit get that. Could you elaborate?

---

## Comment 8

> Username: brunocodutra  
> Created at: 2016-12-25 19:11:04 UTC  
> Updated at: 2016-12-25 19:12:56 UTC  
> Url: https://github.com/boostorg/pfr/issues/5#issuecomment-269134074  

> Could you elaborate?  
  
While it is undefined to access members of an instance `x` of `some_type` through `reinterpret_cast<tuple<Props...>&>(x)`, it appears to me from the reference I quoted in my previous comment that the following would be well defined  
  
```.cpp  
some_type x;  
  
union {  
    some_type _;  
    tuple<Props...> as_tuple;  
} u{x};  
  
get<3>(u.as_tuple);  
```  
  
provided `some_type` and `tuple<Props...>` are layout compatible.  
  
The drawback of that approach however is that it entails a copy of `x`, which would probably be less than desirable in most cases.

---

## Comment 9

> Username: apolukhin  
> Created at: 2016-12-26 19:26:00 UTC  
> Url: https://github.com/boostorg/pfr/issues/5#issuecomment-269236444  

Writing to one field of a union and reading from another is stil an Undefined Behavior.  
  
I've done some experiments and looks like your solution calls default and copy constructors in evaluated contexts multiple times. Problems are in lines  
* `return {};` - default constructs a type `T`  
* `std::decay_t<T>{` - constructs type `T` and aggregate initializes it  
* `Props{}...,` - multiple default constructions  
  
This gives a side-effect of 24+5 default constructions on a 5 element array. This is a big problem for classes with user defined constructors/destructor with side effects, so either your technique must be disabled for those classes or those 3 lines must be fixed somehow.  
  
You could find my test case in attachment [for_each_field.txt](https://github.com/apolukhin/magic_get/files/673265/for_each_field.txt)  
  
I've got some idea on how to check for side effects, but it requires testing. Do you know some technique that could be helpful in that case?

---

## Comment 10

> Username: brunocodutra  
> Created at: 2016-12-26 20:18:05 UTC  
> Url: https://github.com/boostorg/pfr/issues/5#issuecomment-269239269  

> Writing to one field of a union and reading from another is stil an Undefined Behavior.  
  
It's not as long as all types are layout compatible   
(see http://en.cppreference.com/w/cpp/language/data_members#Standard_layout)  
  
> I've got some idea on how to check for side effects, but it requires testing. Do you know some technique that could be helpful in that case?  
  
It would probably be more reasonable to enable the technique only if all members are trivially default constructible.   
  
What about constraining the conversion operator in `ubiq_constructor_copy` using `std::is_trivially_default_constructible` and, in turn, `for_each_field_in_depth` using something similar to `decltype(T{ubiq_constructor_copy{(std::declval<Fields>(), void(), 0)}..., ubiq_constructor_copy{I0}, ubiq_constructor_copy{I}...})`?

---

## Comment 11

> Username: apolukhin  
> Created at: 2016-12-29 07:15:13 UTC  
> Url: https://github.com/boostorg/pfr/issues/5#issuecomment-269591195  

> It's not as long as all types are layout compatible  
  
Unfortunately sequence_tuple::tuple is not a standard layout type.   
  
> It would probably be more reasonable to enable the technique only if all members are trivially default constructible.  
  
I've enabled it if type is constexpr aggregate initializable. That means that all the source codes for construction, destruction and initialization are visible to compiler, have no side effect and are computable at compile time.  
  
I've significantly rewritten the library. All the functions still exist, but header names changed and non flat_ functions are now usable in C++14. A lot of polishing and docs updating is to be done (I'll add an acknowledgments section with your name, more examples, better reference and tests).  
  
I'd appreciate comments on the changes!

---

## Comment 12

> Username: brunocodutra  
> Created at: 2017-01-02 22:58:30 UTC  
> Url: https://github.com/boostorg/pfr/issues/5#issuecomment-270029305  

>> It's not as long as all types are layout compatible  
>  
> Unfortunately sequence_tuple::tuple is not a standard layout type.  
  
Silly me, I somehow had overlooked some of the requirements.  
  
> I've enabled it if type is constexpr aggregate initializable.   
  
That sounds very reasonable.  
  
> I'd appreciate comments on the changes!  
  
I would expect `operator noexcept` to be used to check whether a type is `constexpr constructible`, which would have the advantage of being SFINAE friendly, or, if a hard error is preferred, `static_assert`ing with a nice error message.  
  
see: http://stackoverflow.com/a/13305072/801438

---

## Comment 13

> Username: apolukhin  
> Created at: 2017-01-03 18:31:09 UTC  
> Url: https://github.com/boostorg/pfr/issues/5#issuecomment-270186610  

> I would expect operator noexcept to be used to check whether a type is constexpr constructible, which would have the advantage of being SFINAE friendly, or, if a hard error is preferred, static_asserting with a nice error message.  
>  
> see: http://stackoverflow.com/a/13305072/801438  
  
I've tried that solution half a year ago and it was not working on some compilers. I'd rather avoid it and leave the existing clumsy version.

---

## Comment 14

> Username: apolukhin  
> Created at: 2017-01-05 18:01:09 UTC  
> Url: https://github.com/boostorg/pfr/issues/5#issuecomment-270712014  

Your initial technique was integrated, looks OK, tests pass.   
  
Any other comments, ideas and pull requests are welcomed!
