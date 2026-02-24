# #880 - `if_contains` currently returns `value*`, but it'd be better if it returned `optional<value&>` [Closed]

> Username: brevzin  
> Created at: 2023-04-19 21:07:50 UTC  
> Updated at: 2024-07-19 10:58:20 UTC  
> Closed at: 2024-07-19 10:58:20 UTC  
> Url: https://github.com/boostorg/json/issues/880  

Currently, `json::object` provides [`if_contains`](https://www.boost.org/doc/libs/master/libs/json/doc/html/json/ref/boost__json__object/if_contains.html) that returns a `json::value*` (or `json::value const*`). This is a useful API, definitely more ergonomic than having to call `find()` and check against `end()`.  
  
However, it could be more ergonomic still. `boost::optional` actually supports references, and `boost::optional<value&>` is a much more appropriate type here than `value*`. For simple uses, they're the same:  
  
```cpp  
if (auto value = obj.if_contains(key)) {  
    use(*value);  
}  
```  
  
But returning `optional` allows you to chain further operations and more conveniently access that underlying value. Like, say:  
  
```cpp  
int val = obj.if_contains(key)  
    .map([](json::value const& v){ return v.to_number<int>(); })  
    .value_or(-1);  
```  
  
`optional` enables this approach, whereas with pointers you'd have to do something like:  
  
```cpp  
int val = [&]{  
    auto p = obj.if_contains(key);  
    return p ? p->to_number<int>() : -1;  
}();  
```  
  
This pretty quickly gets more and more clunky. And then [here's a bunch more reasons](https://brevzin.github.io/c++/2021/12/13/optional-ref-ptr/) to prefer `optional<value&>` here.   
  
In short, even if you don't care much for the continuation style, `optional<value&>` supports all the existing valid uses of `value*` that exist today, doesn't support any of the existing invalid uses (like `delete`-ing the pointer, indexing the pointer, incrementing the pointer, ...), and supports more useful operations that do regularly come up (like `map`, `and_then`, `value_or`, ...)

---

## Comment 1

> Username: vinniefalco  
> Created at: 2023-04-19 21:12:41 UTC  
> Url: https://github.com/boostorg/json/issues/880#issuecomment-1515388012  

Well it would have to return `boost::optional` since this library has to work in C++11, is this what you want?

---

## Comment 2

> Username: brevzin  
> Created at: 2023-04-19 21:15:58 UTC  
> Url: https://github.com/boostorg/json/issues/880#issuecomment-1515391834  

Yeah, it also would have to be `boost::optional` since `std::optional` doesn't support references.

---

## Comment 3

> Username: grisumbras  
> Created at: 2023-04-20 11:03:27 UTC  
> Url: https://github.com/boostorg/json/issues/880#issuecomment-1516133526  

I'm not opposed, but there is the issue of invalidating user code. `optional<T&>` doesn't implicitly convert to `T*`. So, changing the return type would break every user who didn't use `auto`.

---

## Comment 4

> Username: brevzin  
> Created at: 2023-04-20 14:38:52 UTC  
> Url: https://github.com/boostorg/json/issues/880#issuecomment-1516449181  

Theoretically you could return a type like:  
```cpp  
namespace boost::json {  
    template <typename T>  
    struct optional : boost::optional<T&> {  
        operator T*() const;  
    };  
}  
```  
which would preserve the `if (json::value* v = o.if_contains(k))` use-cases in addition to the `auto` ones, but would still break on the `auto* `ones and some other more complicated shenanigans.   
  
But I dunno, seems worth breaking that code to give people a better API.   
  
---  
  
Or you could just provide this other version under a different name. Like `try_at()` (since this function seems more closely related to `at()`, which returns a `T&`, than `contains()`, which returns `bool`).  That adds a bit of redundancy, but definitely won't break any users.

---

## Comment 5

> Username: vinniefalco  
> Created at: 2023-04-20 14:40:51 UTC  
> Url: https://github.com/boostorg/json/issues/880#issuecomment-1516452288  

I like better ergonomics. Dmitry can you perhaps do an internet and a smart GitHub search to get an idea of how many people are using it?

---

## Comment 6

> Username: grisumbras  
> Created at: 2023-04-20 20:23:07 UTC  
> Url: https://github.com/boostorg/json/issues/880#issuecomment-1516900197  

I can try

---

## Comment 7

> Username: grisumbras  
> Created at: 2023-05-04 11:25:56 UTC  
> Updated at: 2023-05-04 11:26:11 UTC  
> Url: https://github.com/boostorg/json/issues/880#issuecomment-1534600768  

I've discovered these projects that currently use JSON's `if_contains`, and would be broken by the change:  
* https://github.com/allesafe-com/beng-proxy/  
* https://github.com/CdecPGL/PlanetaMatchMaker/  
* https://github.com/CM4all/libcommon/  
* https://github.com/DyXel/Multirole/  
* https://github.com/gmarino2048/circuit-simulator/  
* https://github.com/guard3/GreekBot/  
* https://github.com/nn6n/beng-proxy/  
* https://github.com/RHVoice/RHVoice/  
* https://github.com/SOURC3XYZ/sourc3/  
* https://github.com/sptrakesh/config-db  
* https://github.com/StoneXLabs/stonex-cms-amqp-test-engine  
* https://github.com/XRPLF/clio/  
  
Ironically, most use `auto`, but still require a pointer (via `auto*`). Many of these projects were fairly recently updated.

---

## Comment 8

> Username: kamrann  
> Created at: 2023-05-10 11:44:30 UTC  
> Url: https://github.com/boostorg/json/issues/880#issuecomment-1542057952  

As someone who uses this library but in general is trying to minimize boost dependencies, can I ask for such a change to allow for opt-in/opt-out through a preprocessor define or similar? Compile-time overhead is my main reason for this - I'm using `std::optional` throughout my project and don't want translation units to start pulling in both `std::optional` and `boost::optional`. But there's also the added possibility that `boost.optional` will bring in more transitive dependencies that weren't already in `boost.json`, bloating the build footprint regardless of compile times.  
  
FWIW, I'll add that I have no issue with breaking changes. The library is still young so I'd agree that breaking changes are preferable to workarounds.

---

## Comment 9

> Username: grisumbras  
> Created at: 2023-06-21 11:36:49 UTC  
> Url: https://github.com/boostorg/json/issues/880#issuecomment-1600675509  

Dependency on Optional would also add dependency on Detail, Utility, Preprocessor, and Io. All fairly small libraries.  
  
If we go with this, we will not add a config option to choose optional implementation. Things like that are a constant source of headache for both users and maintainers, because people keep building libraries with one configuration and linking to it with another, which get them linking errors they have no idea how to fix.

---

## Comment 10

> Username: nigels-com  
> Created at: 2024-05-05 22:50:15 UTC  
> Url: https://github.com/boostorg/json/issues/880#issuecomment-2094980688  

I've been broadly interested in using `std::optional` as an alternative to throw-happy paths such as boost::json::object::at.  
I have two suggestions to add here.  
  
1. In addition to pointer-returning _if_contains_, an equivalent std::optional returning _optional_.  
2. The _optional_ API is only available for C++17 onwards, C++11 code can continue using _if_contains_.  
  
I had a change accepted to the [cxxopts](https://github.com/jarro2783/cxxopts) library along similar lines in  
[PR 421](https://github.com/jarro2783/cxxopts/pull/421).  That API now has a throwing _as_ and a non-throwing _as_optional_.  So the boost::json::object name could be _at_ for throwing and _at_optional_ for non-throwing perhaps?

---

## Comment 11

> Username: nigels-com  
> Created at: 2024-05-05 22:53:52 UTC  
> Url: https://github.com/boostorg/json/issues/880#issuecomment-2094982137  

There is a call for GSOC '24 mentors on the boost mailing list.  If this sort of "C++17 enrichment" project for boost::json is of interest to the maintainers, I'd be willing to propose, mentor and co-ordinate the work with input from the boost::json people.  I've also thought about [std::expected](https://en.cppreference.com/w/cpp/utility/expected) for the error-code path in boost::json as possibly being a nice thing in C++23 mode.

---

## Comment 12

> Username: nigels-com  
> Created at: 2024-05-05 22:57:12 UTC  
> Url: https://github.com/boostorg/json/issues/880#issuecomment-2094984265  

While I'm here rambling about `std::optional` I did also want to mention the CppCon 2023 talk by Peter Muldoon  
[Exceptionally Bad: The Misuse of Exceptions in C++ & How to Do Better](https://www.youtube.com/watch?v=Oy-VTqz1_58) which I did find informative in terms of reducing the use of C++ exceptions in cases like this.

---

## Comment 13

> Username: grisumbras  
> Created at: 2024-05-06 07:09:07 UTC  
> Url: https://github.com/boostorg/json/issues/880#issuecomment-2095326864  

I have a WIP branch (locally) that _adds_ accessor functions that return `system::result` and _not_ optional.  
  
Why add? I decided that changing all of the pointer-returning functions would be too much of a change. Maybe it would have been a good idea initially (and Boost.URL did in fact go with it), but for JSON this ship has sailed.  
  
Why `system::result`? It gives more information to the caller. And even if there's only one reason for a function to fail (e.g. `array::at`), usually you have multiple calls to accessor functions and bail out if you fail, and so it's just more convenient to get the error instead of a generic "not there". Peter also added a bunch of chaining operators to it. And I requested an even more powerful `invoke` function that is supposed to work as n-ary transform, and n-ary bind, and (semantically) does the monadic lifting of arguments for you. So, combining `result`s will be very easy. Oh, and also we already depend on System, so we won't even add any new dependencies.

---

## Comment 14

> Username: nigels-com  
> Created at: 2024-05-06 08:07:24 UTC  
> Url: https://github.com/boostorg/json/issues/880#issuecomment-2095412676  

I would definitely make use of an API using `boost::system::result`.  Ideally I might like C++23 [std::expected](https://en.cppreference.com/w/cpp/utility/expected) a little more, but `system::result` is workable here and now.  It's `std::optional` _enough_ for me.

---

## Comment 15

> Username: vinniefalco  
> Created at: 2024-05-06 16:41:29 UTC  
> Url: https://github.com/boostorg/json/issues/880#issuecomment-2096471967  

> can I ask for such a change to allow for opt-in/opt-out through a preprocessor define  
  
No. The problem with such macros is that it effectively creates two different and API-incompatible libraries.

---

## Comment 16

> Username: grisumbras  
> Created at: 2024-07-19 10:58:20 UTC  
> Url: https://github.com/boostorg/json/issues/880#issuecomment-2238901817  

#941 adds accessor functions that return `system::result<T&>`. I decided against changing existing functions, because I considered that change to be too disruptive. So, closing as "not planned".
