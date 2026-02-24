# #239 - [functional] [test] hana::partial re-forwarding (what were originally) rvalue references can bind to non-const lvalue reference overloads [Closed]

> Username: badair  
> Created at: 2016-01-13 09:52:40 UTC  
> Updated at: 2018-01-01 20:11:58 UTC  
> Closed at: 2018-01-01 20:11:57 UTC  
> Url: https://github.com/boostorg/hana/issues/239  

Is this intended behavior? I was expecting it to bind to the const lvalue reference overload instead of the `&&` overload, since I assume you want a `partial` object to remain idempotent - but I was not expecting the non-const lvalue reference overload.  
  
Repro - the side effects probably don't matter here, but I always like to have them when testing just to be sure, and so I can print the call counts whenever:  
  
``` cpp  
#include <iostream>  
#include <boost/hana.hpp>  
  
struct bar {  
  
    static int move_count;  
    static int copy_count;  
  
    bar() = default;  
  
    bar(const bar&) {  
        copy_count++;  
    }  
  
    bar(bar&&) {  
        move_count++;  
    }  
};  
  
int bar::move_count = 0;  
int bar::copy_count = 0;  
  
struct print_overload_t {  
  
    void operator()(bar& f) {  
        std::cout << "bar&" << std::endl;  
    }  
  
    void operator()(const bar& f) {  
        std::cout << "const bar&" << std::endl;  
  
    }  
    void operator()(bar&& f) {  
        std::cout << "bar&&" << std::endl;  
    }  
  
};  
  
int main() {  
    auto print_overload = print_overload_t{};  
    auto p = boost::hana::partial(print_overload, bar{});  
  
    print_overload(bar{});  
    p();  
  
    std::cin.get();  
    return 0;  
}  
```  
  
By the way, I studied your implementation of `partial` for a bit, and it's the coolest few lines of C++ I think I've ever read. The way you forward from the templated "secret" constructor back into the class template types so you can store the forwarded values is staggeringly creative. I've been trying to think of a way to do exactly that, and I never would've thought of that trick. It took me a few double takes to realize what you were doing.

---

## Comment 1

> Username: badair  
> Created at: 2016-01-13 10:00:55 UTC  
> Url: https://github.com/boostorg/hana/issues/239#issuecomment-171238525  

Also, are there tests for reference forwarding? I did not find them in the test cases, which is why I did this, but I admittedly don't know exactly what's going on in the hana::partial tests - it appears to only be equality comparisons. Let me know if you want me to write more test cases.

---

## Comment 2

> Username: ldionne  
> Created at: 2016-01-13 15:39:51 UTC  
> Url: https://github.com/boostorg/hana/issues/239#issuecomment-171334159  

I'll tell you what happens, and then you can tell me whether you think it is broken or sensible.  
  
`hana::partial` holds the partially-applied arguments by value. In other words, `auto p = partial(print_overload, bar{})` makes a copy of `bar{}` (by move-constructing), and stores that somewhere inside of the `p` variable. The goal here is that `p` is now a fully standalone closure that owns whatever objects it needs to do its job (here `bar{}`). However, that function object (`p`) is a **non-const** lvalue (because created that way). Hence, when you call it, `p`'s non-const overload of `operator()` is called, and since it holds a copy of `bar{}` **by value**, this value is also a non-const lvalue. Hence, `print_overload_t::operator()` is called with a non-const lvalue.  
  
This is, I think, the most flexible option (assuming we hold the arguments by value inside `partial`). However, having `partial` provide a non-const overload to `operator()` is only useful if you want the partially-applied function to modify its argument, which is highly discouraged by Hana. Hence, this flexibility might just be a possibility to shoot oneself in the foot.  
  
Do you see what's happenning now? If so, do you think this behavior is broken? I'm ambivalent about it, but I'm inclined to think that this is unintuitive to most people given your own confusion.  
  
> Also, are there tests for reference forwarding?  
  
No. If you are willing to add some tests, please do so. The tests only make sure that `partial` does the right thing in terms of operation, but they don't check anything related to value categories.

---

## Comment 3

> Username: pfultz2  
> Created at: 2016-01-13 17:58:36 UTC  
> Url: https://github.com/boostorg/hana/issues/239#issuecomment-171380152  

> This is, I think, the most flexible option (assuming we hold the arguments by value inside partial). However, having partial provide a non-const overload to operator() is only useful if you want the partially-applied function to modify its argument, which is highly discouraged by Hana. Hence, this flexibility might just be a possibility to shoot oneself in the foot.  
  
As a note, the Fit library supports both ways, which is interesting. Here is an example:  
  
``` cpp  
#include <fit/pack.h>  
#include <fit/unpack.h>  
#include <iostream>  
  
struct bar {};  
  
struct print_overload {  
  
    void operator()(bar& f) const {  
        std::cout << "bar&" << std::endl;  
    }  
  
    void operator()(const bar& f) const {  
        std::cout << "const bar&" << std::endl;  
  
    }  
    void operator()(bar&& f) const {  
        std::cout << "bar&&" << std::endl;  
    }  
  
};  
  
int main() {  
    auto p = fit::pack(bar{});  
    p(print_overload{});  
    fit::unpack(print_overload{})(p);  
}  
```  
  
Which outputs this:  
  
```  
bar&&  
bar&  
```  
  
As you can see calling `fit::pack(bar{})(print_overload{})` will preserve the original value categories that were packed. That is partially because `fit::pack` only provides a `const` overload. However, `fit::unpack(print_overload{})(bar{})` will take on the value categories of the sequence. This essentially allows the user to modify the sequence in place if they wanted to.   
  
For function adaptors, such as `partial` or `capture`, it doesn't make sense to modify them in place. Furthermore, I found the change in the value category to be surprising. So all the adaptors actually use the member call operator of `pack` to unpack the values. The only disadvantage to this approach is that rvalues are copied(or moved) when the function is invoked.

---

## Comment 4

> Username: ldionne  
> Created at: 2016-01-13 18:15:33 UTC  
> Url: https://github.com/boostorg/hana/issues/239#issuecomment-171385118  

However, then, you might end up using a moved-from object when you probably do not expect it. Consider  
  
``` c++  
#include <fit/pack.h>  
#include <fit/unpack.h>  
#include <iostream>  
  
struct print_overload {  
    void operator()(std::string& s) const {  
        std::cout << "std::string&: " << s << std::endl;  
    }  
    void operator()(std::string const& s) const {  
        std::cout << "std::string const&: " << s << std::endl;  
    }  
    void operator()(std::string&& s) const {  
        std::cout << "std::string&&: " << s << std::endl;  
        // actually move from s, as we expect might happen in real code  
        std::string ss = std::move(s);  
    }  
};  
  
int main() {  
    auto p = fit::pack(std::string{"abcdef"});  
    p(print_overload{});  
    p(print_overload{}); // uses a moved-from object  
    fit::unpack(print_overload{})(p);  
}  
```  
  
which outputs  
  
```  
std::string&&: abcdef  
std::string&&:  
std::string&:  
```  
  
The second call to `p` uses the moved-from `std::string` stored in the pack. At first sight, I would definitely not have expected the first call to `p` to allow the string to be moved from. What is the rationale for this?

---

## Comment 5

> Username: badair  
> Created at: 2016-01-13 18:34:55 UTC  
> Updated at: 2016-01-13 19:50:10 UTC  
> Url: https://github.com/boostorg/hana/issues/239#issuecomment-171390791  

@pfultz2  
I really like the idea of having the option to preserve reference forwarding. It may also be useful to provide a way to specify whether we want to re-move move-constructed objects, or continue dishing out const lvalue references for idempotence.   
  
I also don't think we run the risk of providing too much granularity on this issue (even though I'm biased).  
  
@ldionne   
I see - you decay the types to the class template, and take a universal reference in the constructor - last night I thought that the `Args...` in the `storage_` type `tuple< F, Args...>`  (paraphrasing) were the same references from the constructor. That's why I was so amazed - because I was pretty sure that was impossible. So now I see the full picture.   
  
Side question: is the rvalue casting in `hana::partial` strictly an optimization to avoid redundant type deduction in `std::move`?  
  
I wrote [this](https://github.com/badair/CLBL/blob/develop/include/CLBL/forward.h) class template in my toy project for perfect forwarding the same reference from parameters I can't control, to arguments I can't control. (I guess we can call it "double forwarding"). An example/explanation can be found below this [line](https://github.com/badair/CLBL/blob/develop/example/intro.cpp#L213). In order for hana::partial to do this, it would have to introspect on the argument types, which isn't possible for ambiugous cases (overloaded/templated callables) unless they are explicitly specified. Dealing with these situations is the main use case of CLBL. However, this class was not originally intended for partial function application - I only use it to "invisibly" forward between two sequence points. Prvalues would immediately lose scope and become invalid if this were used for persistent storage ("persistent double forwarding"), unless construction semantics were added. Perhaps the onus of reference preservation could be on the client, after forcing them to enable it explicitly.  
  
Another option would be to map some bit flags representing the value categories of the forwarding references at partial_t construction, and then dispatch on the flags when reforwarding, which I believe would still be done with constexpr-ness. I use that solution throughout CLBL. For persistent double forwarding, the onus of reference preservation would still have to be on the client. (Too bad we can't use Rust lifetimes).  
  
Not to toot my own horn on your issues thread - I just thought my trials-and-errors in this area might be of some help.

---

## Comment 6

> Username: pfultz2  
> Created at: 2016-01-13 18:50:25 UTC  
> Url: https://github.com/boostorg/hana/issues/239#issuecomment-171395643  

> At first sight, I would definitely not have expected the first call to p to allow the string to be moved from.  
  
It should move from a copy of `std::string`. However, I realize now that a copy is never made. I'll need to fix that.

---

## Comment 7

> Username: badair  
> Created at: 2016-01-13 19:29:09 UTC  
> Updated at: 2016-01-13 19:48:49 UTC  
> Url: https://github.com/boostorg/hana/issues/239#issuecomment-171407138  

Another idea:  
  
You could permute the possible qualifier/reference combinations on each the `partial_t` constructor argument type, and declare the `storage_` tuple with that in mind. Then you could definitely map into the `storage_` object based on the universal reference type, and later dispatch back into the hosted callable, perserving the reference. Then you could even add logic to bypass redundant copies and moves.  
  
This would, however, probably be horribly slow to compile, so maybe not worth exploring. However, if you decay the types first, you don't have to permute over each argument independently - those permutations are, obviously, known by us at compile time. I imagine the type mapping would be the real kicker - I don't know what the space/time complexity is of `hana::map`, but I imagine it's pretty heavy.  
  
Side question - I know you can use the term "qualifier" for const and volatile in all contexts, and I know you can have ref-qualified overloads of member functions, but can you call the `&`on `const int &` a _reference qualifier_? I don't know what else to call it, but I haven't found a definitive answer. I thought one of you might know.  
  
(if my boss is somehow reading this: I'm billing these comments to weekly education time)

---

## Comment 8

> Username: badair  
> Created at: 2016-01-13 20:33:51 UTC  
> Updated at: 2016-01-13 20:40:03 UTC  
> Url: https://github.com/boostorg/hana/issues/239#issuecomment-171424376  

To answer your question:  
  
> do you think this behavior is broken?  
  
No. No single implementation of hana::partial can universally accommodate the 3 use cases I can see for it. I think the current implementation fits case number 1 of these. 2 and 3 have potential as sister classes or tagged specializations:  
1. Copy everything and don't worry about value categories. This is ideal for most real world usage - don't have to worry about having stored references destructed out from under you. This is also especially nice for the library maintainer, who doesn't receive any misplaced blame for invalid addess errors caused by client code.  
2. Store lvalue and const lvalue references in a tuple, and either move-construct rvalue references or keep them in a const lvalue reference (probably the former, as I don't see a use case for attempting to keep a dying object alive). Dispatch back out to the hosted function when called, maintaining the original intent of the partially applied signature. _Arguments received as rvalue references are re-forwarded as const lvalue references_.  
3. Same as 2, except _arguments received as rvalue references are moved again when invoking the hosted function, invalidating future calls on the partial object_.  
  
If a client thinks they need use case 2 or 3, they either have no idea what they are doing, or they have a very special use case (e.g. when they are forced to partially apply a std::unique_ptr argument). Since differentiating the 3 use cases will have to be explicit, the obvious choice is to leave the current and most common-sense implementation, 1, the default.  
  
I don't see the harm in adding support for 2 and 3, besides maintenance concerns. I'm interested in implementing them if you are indifferent. 3 is probably different enough to warrant a new name, such as `hana::partial_once`.

---

## Comment 9

> Username: ldionne  
> Created at: 2016-01-14 21:34:22 UTC  
> Url: https://github.com/boostorg/hana/issues/239#issuecomment-171786782  

> Side question: is the rvalue casting in hana::partial strictly an optimization to avoid redundant type deduction in std::move?  
  
It's to avoid instantiating `std::forward`.  
  
> Side question - I know you can use the term "qualifier" for const and volatile in all contexts, and I know you can have ref-qualified overloads of member functions, but can you call the `&` on `const int &` a reference qualifier?   
  
I don't know for sure. I'm sure I've used it before, but perhaps it was wrong. That might be a good question to ask on StackOverflow.  
  
Regarding allowing references in `partial`:  
  
If someone is reading this, it might be interesting to see the chat discussion starting [here](https://gitter.im/boostorg/hana?at=5696b9565de13b3f15e2e681).  
Also, I think it would be possible to achieve (almost) what we want with the following `unref` helper:  
  
``` c++  
#include <boost/hana.hpp>  
#include <functional>  
#include <iostream>  
#include <string>  
#include <type_traits>  
namespace hana = boost::hana;  
  
  
template <typename T>  
struct is_reference_wrapper {  
    static constexpr bool value = false;  
};  
  
template <typename T>  
struct is_reference_wrapper<std::reference_wrapper<T>> {  
    static constexpr bool value = true;  
};  
  
  
struct unref_impl {  
    template <typename T, typename = void, typename = std::enable_if_t<  
        is_reference_wrapper<std::decay_t<T>>::value  
    >>  
    constexpr decltype(auto) operator()(T&& t) const {  
        return t.get();  
    }  
  
    template <typename T, typename = std::enable_if_t<  
        !is_reference_wrapper<std::decay_t<T>>::value  
    >>  
    constexpr decltype(auto) operator()(T&& t) const {  
        return static_cast<T&&>(t);  
    }  
};  
  
  
template <typename F>  
constexpr auto unref(F&& f) {  
    return hana::on(static_cast<F&&>(f), unref_impl{});  
}  
  
int main() {  
    auto f = [](auto const& s) {  
        std::cout << s << std::endl;  
    };  
    std::string s = "abcdef";  
  
    auto p = hana::partial(unref(f), std::ref(s));  
    p();  
}  
```  
  
Basically, instead of changing `hana::partial` a user knowing that his function needs to take references could use `std::ref`/`std::cref` and wrap his function with `unref` to make it usable with reference wrappers. For those times where we actually want to store references in `hana::partial`, that might be the least complicated option.  
  
Now, of course, since we lack a way to store rvalue references in a `std::reference_wrapper`, that wouldn't allow perfect forwarding stuff through `hana::partial`. However, one could argue that this is a problem of the standard library, and not of Hana.  
  
IDK, I'm just trying to find the least intrusive way (i.e. the most composable way) to add this functionality. I'm not sure it is to create a new `hana::partial_once` function or to specialize `hana::partial` for `std::reference_wrapper`s, since the same would have to be done for virtually every other functional combinator (`curry`, `reverse_partial`, `demux`, etc...).

---

## Comment 10

> Username: pfultz2  
> Created at: 2016-01-14 22:30:19 UTC  
> Url: https://github.com/boostorg/hana/issues/239#issuecomment-171802542  

As a note, `fit::partial` stores everything by value unless `std::ref` is used then it stores by reference. If the user wants perfect forwarding then `fit::capture` or `fit::capture_forward` can be used. The reason for this is that the partial adaptor, the partial application, and the invocation can all happen at different scopes, so it can easily lead to dangling references if not careful. In addition, `std::bind` follows the same semantics.  
  
However, with `fit::capture` the adapting and partial application happens in one step(ie `capture(xs...)(f)`). So the user is more aware of the scope they have captured in and the scope they will invoke the function in(much like lambdas).  
  
> I'm not sure it is to create a new hana::partial_once function or to specialize hana::partial for std::reference_wrappers, since the same would have to be done for virtually every other functional combinator (curry, reverse_partial, demux, etc...).  
  
Actually, it would only would be needed for `curry`, `partial`, and `reverse_partial`. If the user wants partial application with perfect forwarding then `capture` can be used. If the user wants to decay parameters or unwrap references on the other adaptors(such as `demux`) the user can use `hana::on`(ie `on(demux, decay)`).

---

## Comment 11

> Username: ldionne  
> Created at: 2016-01-19 14:11:21 UTC  
> Url: https://github.com/boostorg/hana/issues/239#issuecomment-172864530  

Is there a difference between adding support for `reference_wrappers` in `hana::partial` & friends, and using the `unref` function I outlined above? I see none, but perhaps I'm missing something? If there is no difference, it seems to me that providing this with an additional combinator would increase the composability of the library instead of adding a special case. I do agree that it shoves more work on the hands of the users though.

---

## Comment 12

> Username: pfultz2  
> Created at: 2016-01-19 18:15:29 UTC  
> Url: https://github.com/boostorg/hana/issues/239#issuecomment-172938655  

Of course, unwrapping ref wrappers when taking by value is just purely a convenience to the user, but no doubt could be beyond the scope of Hana(as its purpose is for heterogeneous sequences), especially since the user could just simply use `fit::partial` to achieve the behavior.

---

## Comment 13

> Username: badair  
> Created at: 2016-01-22 03:30:01 UTC  
> Updated at: 2016-01-22 03:33:19 UTC  
> Url: https://github.com/boostorg/hana/issues/239#issuecomment-173794481  

> It's to avoid instantiating `std::forward`.  
  
Oops. Reference collapsing. Got it.  
  
> `unref`  
  
I think that's a nice idea. Clever, too. Automatic unwrapping of `reference_wrapper`s would be nice, but I don't think the lack thereof detracts significantly from the library. Especially not for the first release. Specializing for `reference_wrapper` would also increase compile times for the affected components, which makes everyone sad.

---

## Comment 14

> Username: ldionne  
> Created at: 2018-01-01 20:11:57 UTC  
> Url: https://github.com/boostorg/hana/issues/239#issuecomment-354673645  

I'm going to close this as "wontfix" since this is out of scope for the library. With C++17, the need for functional combinators is rare (and in fact if it were not for constexpr lambdas, Hana would never have had them). When needed, Boost.Fit should be preferred as its goal is to solve that specific problem.
