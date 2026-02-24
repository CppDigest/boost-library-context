# #244 - [type] pointer to member operator [Closed]

> Username: badair  
> Created at: 2016-01-22 08:07:44 UTC  
> Updated at: 2016-02-02 12:52:47 UTC  
> Closed at: 2016-02-02 12:52:47 UTC  
> Url: https://github.com/boostorg/hana/issues/244  

I'm interested in heterogeneous computations for callable types:  
  
``` cpp  
#include <boost/hana.hpp>  
namespace hana = boost::hana;  
  
struct foo {  
    const char* bar() const {  
        return "hello, foo::bar!";  
    }  
};  
  
int main() {  
  
    auto my_foo = foo{};  
  
    auto ptr = &my_foo;  
    auto ptr_type = hana::make_type(ptr); //hana::type<foo*>  
  
    auto bar = &foo::bar;  
    auto bar_type = hana::make_type(bar); //hana::type<const char*(foo::*)(void) const>  
  
    auto call_pmf = [](auto&& pmf, auto&& obj) {  
        return (obj->*pmf)();  
    };  
  
    //value computation - result is is const char* pointing to "hello, foo::bar!"  
    auto result = call_pmf(bar, ptr);   
  
    //type computation - result_type is object of type hana::type<const char*>  
    auto result_type = call_pmf(bar_type, ptr_type);   
  
    return 0;  
}  
  
```  
  
Of course, the `result_type` initialization causes a compiler error under the current implementation of Hana, because `operator->*` isn't defined on `hana::type`.   
  
Note: I'll refer to Hana's implementation at a high level here  - I admittedly haven't traveled very far into the `hana::detail::operators::adl` rabbit hole.  
  
I'm aware that user-defined overloads on the plain `operator->` are wimpy in C++ compared to the built-in version. But I don't know of any reason why `hana::type` couldn't have an `operator->*` to achieve what I describe in the code above.   
  
Do you know of any obstructions to an implementation of this? Would this be a good feature for Hana? What is your opinion on implementing some of the more "exotic" operators in `hana::type`?  
  
I'm also interested in `operator()`.   
  
I didn't find either of these operators in a browser `Ctrl + F` of commit 4bef99d3d045ae55c490c4e6def3c003ad85f96f. I also assumed that issue #30 is concerned with `operator->`, so I though it would be appropriate to open a new issue for this. Please close if you've addressed this before - I would be a little surprised if Hana hasn't already been down this road once.

---

## Comment 1

> Username: ldionne  
> Created at: 2016-01-22 12:42:48 UTC  
> Updated at: 2016-01-30 19:01:56 UTC  
> Url: https://github.com/boostorg/hana/issues/244#issuecomment-173910389  

Indeed, Hana has been down this road. See the `experimental/type_operators.cpp` file. It does not address the operators that you propose, but it presents the general idea.  
  
As to why it isn't included in the library right now, I'd say it is mostly because  
1. Deduced returned type for functions make it less useful to have a way to determine the result of a function. Usually, using `decltype(function(std::declval<...>()...))` is equivalent and more efficient.  
2. The trick you explored only works if builtin operators are called on the `type<...>` object, but not if a member function is used.  
3. `hana::type` already defines the unary `+` operator to do something else, but that could be changed.  
  
**Edit**  
I want to do some cleanup in the `experimental/` directory and remove the `experimental/type_operators.cpp` file. So here's the content of the file  
  
``` c++  
#include <boost/hana/assert.hpp>  
#include <boost/hana/core/is_a.hpp>  
#include <boost/hana/core/tag_of.hpp>  
#include <boost/hana/equal.hpp>  
#include <boost/hana/functional/compose.hpp>  
#include <boost/hana/type.hpp>  
  
#include <type_traits>  
namespace hana = boost::hana;  
  
  
#define BOOST_HANA_TYPE_UNARY_PREFIX_OPERATOR(OP)                           \  
    template <typename T, typename = std::enable_if_t<                      \  
        hana::is_a<hana::type_tag, T>()                                     \  
    >>                                                                      \  
    constexpr auto operator OP (T) {                                        \  
        return hana::type_c<decltype(                                       \  
            OP std::declval<typename T::type>()                             \  
        )>;                                                                 \  
    }                                                                       \  
/**/  
  
#define BOOST_HANA_TYPE_UNARY_POSTFIX_OPERATOR(OP)                          \  
    template <typename T, typename = std::enable_if_t<                      \  
        hana::is_a<hana::type_tag, T>()                                     \  
    >>                                                                      \  
    constexpr auto operator OP (T, int) {                                   \  
        return hana::type_c<decltype(                                       \  
            std::declval<typename T::type>() OP                             \  
        )>;                                                                 \  
    }                                                                       \  
/**/  
  
#define BOOST_HANA_TYPE_BINARY_OPERATOR(OP)                                 \  
    template <typename T, typename U, typename = std::enable_if_t<          \  
        hana::is_a<hana::type_tag, T>() && hana::is_a<hana::type_tag, U>()  \  
    >>                                                                      \  
    constexpr auto operator OP (T, U) {                                     \  
        return hana::type_c<decltype(                                       \  
            std::declval<typename T::type>()                                \  
            OP                                                              \  
            std::declval<typename U::type>()                                \  
        )>;                                                                 \  
    }                                                                       \  
/**/  
  
  
BOOST_HANA_TYPE_UNARY_PREFIX_OPERATOR(+)  
BOOST_HANA_TYPE_UNARY_PREFIX_OPERATOR(-)  
BOOST_HANA_TYPE_BINARY_OPERATOR(+)  
BOOST_HANA_TYPE_BINARY_OPERATOR(-)  
BOOST_HANA_TYPE_BINARY_OPERATOR(*)  
BOOST_HANA_TYPE_BINARY_OPERATOR(/)  
BOOST_HANA_TYPE_BINARY_OPERATOR(%)  
  
BOOST_HANA_TYPE_BINARY_OPERATOR(<)  
BOOST_HANA_TYPE_BINARY_OPERATOR(>)  
BOOST_HANA_TYPE_BINARY_OPERATOR(<=)  
BOOST_HANA_TYPE_BINARY_OPERATOR(>=)  
  
BOOST_HANA_TYPE_UNARY_PREFIX_OPERATOR(++)  
BOOST_HANA_TYPE_UNARY_PREFIX_OPERATOR(--)  
BOOST_HANA_TYPE_UNARY_POSTFIX_OPERATOR(++)  
BOOST_HANA_TYPE_UNARY_POSTFIX_OPERATOR(--)  
  
BOOST_HANA_TYPE_BINARY_OPERATOR(<<)  
BOOST_HANA_TYPE_BINARY_OPERATOR(>>)  
BOOST_HANA_TYPE_BINARY_OPERATOR(|)  
BOOST_HANA_TYPE_BINARY_OPERATOR(&)  
BOOST_HANA_TYPE_BINARY_OPERATOR(^)  
BOOST_HANA_TYPE_UNARY_PREFIX_OPERATOR(~)  
  
BOOST_HANA_TYPE_BINARY_OPERATOR(&&)  
BOOST_HANA_TYPE_BINARY_OPERATOR(||)  
BOOST_HANA_TYPE_UNARY_PREFIX_OPERATOR(!)  
  
BOOST_HANA_TYPE_UNARY_PREFIX_OPERATOR(*)  
BOOST_HANA_TYPE_UNARY_PREFIX_OPERATOR(&)  
  
  
int main() {  
    auto deref = [](auto x) { return *x; };  
    auto deref3 = hana::compose(deref, deref, deref);  
  
    BOOST_HANA_CONSTANT_CHECK(hana::equal(  
        *hana::type_c<char*>,  
        hana::type_c<char&>  
    ));  
  
    BOOST_HANA_CONSTANT_CHECK(hana::equal(  
        deref(hana::type_c<int*>),  
        hana::type_c<int&>  
    ));  
  
    BOOST_HANA_CONSTANT_CHECK(hana::equal(  
        deref3(hana::type_c<int***>),  
        hana::type_c<int&>  
    ));  
  
    BOOST_HANA_CONSTANT_CHECK(hana::equal(  
        hana::type_c<int> + hana::type_c<float>,  
        hana::type_c<float>  
    ));  
  
    BOOST_HANA_CONSTANT_CHECK(hana::equal(  
        ++hana::type_c<int&>,  
        hana::type_c<int&>  
    ));  
}  
```

---

## Comment 2

> Username: ldionne  
> Created at: 2016-01-30 16:04:55 UTC  
> Url: https://github.com/boostorg/hana/issues/244#issuecomment-177219944  

@badair I'm curious to know what you think of using `auto` deduced return type to achieve what you want. Do you think that would work? If so, is there still a use case for operators on `hana::type`s? If so, let's discuss; otherwise I'll close this.

---

## Comment 3

> Username: badair  
> Created at: 2016-01-30 17:21:02 UTC  
> Updated at: 2016-01-30 17:33:07 UTC  
> Url: https://github.com/boostorg/hana/issues/244#issuecomment-177252461  

You are right that `decltype`/`auto` is the better choice. The only argument I can see for implementing `operator()` on `hana::type` is that it allows one to write a generic computation for a callable type that applies to both a `hana::type` object for compile time computation, as well instances of the underlying type for runtime computations. I recall seeing one of your presentation examples that did something similar - I believe it was a generic lambda named `remove_pointer`.  
  
I really thought one could overload  `operator->*` to accept external member function _pointers_ (as opposed to a member name lookup). When I get a little bit more free time I'll do some more experimentation and see if I can find anything worth mentioning. I'm leaving my job in 2 weeks to pursue open source development and personal education for at least a couple of months, so I'll have more time soon to explore some ideas.  
  
Would there be any value in exploring a Hana-lifted version of `declval` to get around the limitations of overloaded member access operators in C++?

---

## Comment 4

> Username: ldionne  
> Created at: 2016-01-30 18:56:07 UTC  
> Url: https://github.com/boostorg/hana/issues/244#issuecomment-177275230  

> The only argument I can see for implementing `operator()` on `hana::type` is that it allows one to write a generic computation for a callable type that applies to both a `hana::type` object for compile time computation, as well instances of the underlying type for runtime computations.  
  
I think I don't understand the difference between this and using `auto` deduced return type.  
  
> I really thought one could overload operator->\* to accept external member function pointers (as opposed to a member name lookup).  
  
It is possible (but I certainly didn't know that before I tried!):  
  
``` c++  
struct Nested {  
    int bar;  
    char baz;  
};  
  
struct Foo { };  
  
void operator->*(Foo, int Nested::*) { }  
void operator->*(Foo, char Nested::*) { }  
  
int main() {  
    auto bar = &Nested::bar;  
    auto baz = &Nested::baz;  
  
    Foo foo;  
    foo->*bar;  
    foo->*baz;  
}  
```  
  
> Would there be any value in exploring a Hana-lifted version of declval to get around the limitations of overloaded member access operators in C++?  
  
What limitations? Concretely, what are you thinking about when you say a "Hana-lifted version of `declval`"?

---

## Comment 5

> Username: badair  
> Created at: 2016-02-01 14:21:17 UTC  
> Url: https://github.com/boostorg/hana/issues/244#issuecomment-177991307  

I'll leave a proper reply in about 12 hours.

---

## Comment 6

> Username: ldionne  
> Created at: 2016-02-01 14:45:53 UTC  
> Url: https://github.com/boostorg/hana/issues/244#issuecomment-177999875  

We're not in a hurry. Take your time.

---

## Comment 7

> Username: badair  
> Created at: 2016-02-02 02:29:40 UTC  
> Updated at: 2016-02-02 04:00:03 UTC  
> Url: https://github.com/boostorg/hana/issues/244#issuecomment-178321037  

If binary `operator+` doesn't apply to `hana::type`, then `operator->*` probably doesn't either.  
  
> I think I don't understand the difference between this and using auto deduced return type.  
  
This non-working `operator()` example is what I'm trying to describe:  
  
``` cpp  
  
struct foo {  
    char operator()(int){  
        return 'a';  
    }  
};  
  
auto call_with_int = [](auto&& x) {  
    int i = 0;  
    return foo(i);    
}  
  
int main() {  
    var foo_t = hana::type_c<foo>;  
    var foo_v = foo{};  
  
    //hana::type_c<char>  
    auto foo_result_t = call_with_int(foo_t);  
  
    //as opposed to:  
    //auto foo_result_t = hana::decltype_(call_with_int(foo_v));  
  
    //'a'  
    auto foo_result_v = call_with_int(foo_v);  
}  
```  
  
The difference is small, but interesting, since the type and value semantics are identical in the uncommented initialization of `foo_result_t`, which assumes an `operator()` overload on `hana::type`.  
  
regarding `operator->*`, something like this working example might be cool:  
  
``` cpp  
#include <utility>  
#include <type_traits>  
  
namespace pretend_hana {  
  
    template<typename T>  
    struct type {  
        template<typename K, typename U>  
        auto operator->*(K U::*) {  
            return type<K U::*>{};  
        }  
    };  
  
     template <typename K, typename U>  
    struct type<K U::*> {  
  
        template<typename... Args>  
        auto operator()(Args&&... args) {  
  
            //I never know how to format these...  
  
            return type<decltype(  
                    (std::declval<U>().*std::declval<K U::*>())  
                        (static_cast<Args&&>(args)...)  
                )>{};  
        }  
    };  
}  
  
struct bar{};  
  
struct foo {  
    bar return_bar(int, char){ return bar{};}  
};  
  
int main() {  
  
    auto foo_type = pretend_hana::type<foo>{};  
    auto result_type = (foo_type->* &foo::return_bar)(1, 'a');  
  
    static_assert(std::is_same<decltype(result_type), pretend_hana::type<bar>>::value, "");  
  
    return 0;     
}  
```  
  
But I don't know what that member specialization would do to your benchmarks, or how many wrenches it throws in your existing implementation. Is this just _cool_, or is it _useful_ as well?  
  
>  limitations of overloaded member access operators  
  
The answers to [this SO question](http://stackoverflow.com/questions/8777845/overloading-member-access-operators-c/8777860) say to me that `operator->` is basically worthless for `hana::type` and friends, since the return type peculiarities for `operator->` leave little room for creativity and indirection.  
  
> Concretely, what are you thinking about when you say a "Hana-lifted version of declval"?  
  
I imagined that the advantages of my first snippet in this reply could be combined with my second snippet to provide a universal "operator" for member access that applies to both `hana::type` and ordinary objects. `declval` is probably not the right name for this... `member` makes more sense. However, I realized the only way to implement this was with a lambda, which can be used for much more than member access. I really just wanted a nice, pretty `.` access to members, somehow... But it's not going to happen. I'm going to go ahead and retract the proposed idea of a "Hana-lifted version of declval", if I may.  
  
I believe the logical conclusion of my second snippet in this reply provides universality at the expense of brevity. If we toy around with the `type` alias inside for the case of data members, I think it could be pretty consistent. But the question remains: is it _useful_?  
  
_Edit_: "universality at the expense of brevity" is, of course, only true for pointer types, since `->*` is used instead of `.*`. Such is C++...  
  
_Edit 2_: removed a question that you'd answered previously

---

## Comment 8

> Username: badair  
> Created at: 2016-02-02 03:50:21 UTC  
> Updated at: 2016-02-02 03:56:27 UTC  
> Url: https://github.com/boostorg/hana/issues/244#issuecomment-178353255  

I guess what I'd like to see is what the `type_operators.cpp` file was attempting: the ability to treat a `hana::type` object like an instance of its underlying type, allowing us to pass instances into non-trivial logic and get the type result of the operation instead of the value result.  
  
Since there isn't a clean way to perform Hana-lifted member access, there isn't anything to find at the end of that road. We really can't do better than `decltype`. This `operator->*` idea is a step closer, but it still can't make `hana::type` a drop-in replacement for a value of it's type, which is the only use case for these operators in the first place, since we already have `decltype` at our disposal. Which means this issue should be closed.  
  
Is that a correct summary of this situation?

---

## Comment 9

> Username: ldionne  
> Created at: 2016-02-02 12:52:47 UTC  
> Url: https://github.com/boostorg/hana/issues/244#issuecomment-178558239  

> This non-working operator() example is what I'm trying to describe:  
>   
> [...]  
  
Ok, then we're on the same page about the potential use case for overloading operators for `hana::type`s. The issue is whether this is useful, see below.  
  
> [...]  
>   
> But I don't know what that member specialization would do to your benchmarks, or how many wrenches it throws in your existing implementation.   
  
I don't think it would be a major problem. Implementation difficulty does not seem to be the issue here.  
  
> Is this just cool, or is it useful as well?  
  
This, I fear, is the issue. I don't think this is useful, given that one could use `decltype(std::declval<foo>().return_bar(1, 'a'))` to achieve the same result. Plus, this solution is   
1. Conceptually simpler for anyone that knows C++ (but not necessarily Hana)  
2. Simpler for the compiler, as there is no additional instatiation of `hana::type<...>::operator()`  
3. Shorter to type, at least in most cases  
  
The same reasoning applies to the overloading of any operator I can think of on `hana::type`s. Instead of using `hana::type<T>.operator whatever`, one can simply use `std::declval<T>().operator whatever`. This will work out of the box, and there are no catches to this technique. So while I think this experiment is interesting (I was amazed when I first wrote `type_operators.cpp`!), I also think it is useless as a feature of Hana. Of course, time may prove me wrong, but I can simply not find a use case where `std::declval` + `decltype` wouldn't be better than overloading operators on `hana::type`s.  
  
> Since there isn't a clean way to perform Hana-lifted member access, there isn't anything to find at the end of that road. We really can't do better than decltype. This operator->\* idea is a step closer, but it still can't make hana::type a drop-in replacement for a value of it's type, which is the only use case for these operators in the first place, since we already have decltype at our disposal.   
  
We agree.  
  
> Which means this issue should be closed.  
>   
> Is that a correct summary of this situation?  
  
It is, from my point of view at least. If we realize that we can do something awesome by overloading operators on `hana::type`s, then we'll definitely revisit this. But for the time being, the only sane thing to do is to close this. Nonetheless, thanks for the interesting discussion.
