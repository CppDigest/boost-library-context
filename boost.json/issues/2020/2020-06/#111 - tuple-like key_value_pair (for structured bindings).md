# #111 - tuple-like key_value_pair (for structured bindings) [Closed]

> Username: AeroStun  
> Created at: 2020-06-13 18:06:24 UTC  
> Updated at: 2020-07-01 03:41:37 UTC  
> Closed at: 2020-07-01 03:41:37 UTC  
> Url: https://github.com/boostorg/json/issues/111  

At the moment `key_value_pair` cannot be used for structured bindings without user boilerplate.  
Since it does model a pair anyway and offers an interface similar to `std::pair`, it would be nice for it to also specialize `std::tuple_size`, `std::tuple_element` and a form of `get` when using C++17 or above  
This would make the following code well-formed in C++17 or above:  
```cpp  
object o{{"one", 1}, {"two", 2}};  
const auto it = o.find("one");  
if (it == o.end())  
  handle_error();  
const auto& [key, value] = *it;  
```  
Note that replacing `object` with `std::map<std::string, int>` would produce well-formed C++17 code (see https://godbolt.org/z/fVZUkC)  
  
  
If this is something the authors would like too see too, I'd happily make a PR for it

---

## Comment 1

> Username: vinniefalco  
> Created at: 2020-06-13 18:09:50 UTC  
> Updated at: 2020-06-13 18:10:19 UTC  
> Url: https://github.com/boostorg/json/issues/111#issuecomment-643658248  

This needs:  
1. To be conditionally enabled when C++17 or later  
2. To have tests, also conditionally enabled  
3. To be mentioned in the docs  
  
@AeroStun up to you if you want to try it as a pull request, but if not then @sdkrystian will do it.

---

## Comment 2

> Username: AeroStun  
> Created at: 2020-06-13 18:20:38 UTC  
> Url: https://github.com/boostorg/json/issues/111#issuecomment-643659438  

I'll give it a try right now; might come back with questions if I fail to find an answer by reading the current code

---

## Comment 3

> Username: sdkrystian  
> Created at: 2020-06-13 20:24:16 UTC  
> Url: https://github.com/boostorg/json/issues/111#issuecomment-643673742  

We don't need this conditionally enabled.

---

## Comment 4

> Username: vinniefalco  
> Created at: 2020-06-13 20:31:25 UTC  
> Updated at: 2020-06-13 20:31:34 UTC  
> Url: https://github.com/boostorg/json/issues/111#issuecomment-643674520  

Ah `<tuple>`is C++11. Hmm... including <tuple> from value.hpp is heavyweight. Maybe this should go in a different header so that someone can avoid getting those headers if they don't need it.

---

## Comment 5

> Username: AeroStun  
> Created at: 2020-06-13 20:34:10 UTC  
> Updated at: 2020-06-13 21:23:45 UTC  
> Url: https://github.com/boostorg/json/issues/111#issuecomment-643674836  

Well, given `<array>` and `<utility>` contain specializations of `std::tuple_element` and `std::tuple_size` (for `std::array` and `std::pair` respectively), isn't it fair to assume that we can rely on those being included instead? (they are already included in `value.hpp`)  
  
https://eel.is/c++draft/tuple.helper#:%3Cutility%3E

---

## Comment 6

> Username: vinniefalco  
> Created at: 2020-06-13 21:19:31 UTC  
> Url: https://github.com/boostorg/json/issues/111#issuecomment-643679833  

If true, that's a good point! I don't recall why I include `<array>` though, doesn't sound needed.

---

## Comment 7

> Username: AeroStun  
> Created at: 2020-06-13 21:21:05 UTC  
> Url: https://github.com/boostorg/json/issues/111#issuecomment-643679959  

Self-correction: you don't include `<array>`, only `<utility>`

---

## Comment 8

> Username: vinniefalco  
> Created at: 2020-06-13 21:21:51 UTC  
> Url: https://github.com/boostorg/json/issues/111#issuecomment-643680067  

Yes you can specialize it after including `<utility>` good idea!

---

## Comment 9

> Username: sdkrystian  
> Created at: 2020-06-13 21:25:37 UTC  
> Url: https://github.com/boostorg/json/issues/111#issuecomment-643680472  

I have an implementation done  -- not sure if we want to document all this boilerplate.

---

## Comment 10

> Username: vinniefalco  
> Created at: 2020-06-13 21:27:54 UTC  
> Url: https://github.com/boostorg/json/issues/111#issuecomment-643680663  

We don't need to include std symbols in the reference, but we should include `json::get` in the reference, and in the exposition we should point out that structured bindings will work. Perhaps in a sub-section with the heading "C++17 and later."

---

## Comment 11

> Username: AeroStun  
> Created at: 2020-06-13 21:30:36 UTC  
> Url: https://github.com/boostorg/json/issues/111#issuecomment-643680920  

I have <https://github.com/AeroStun/json/commit/5f4d3fca4e4fa5112bdcbf928a5b3c15872aa751> running some CIs

---

## Comment 12

> Username: vinniefalco  
> Created at: 2020-06-13 21:32:28 UTC  
> Url: https://github.com/boostorg/json/issues/111#issuecomment-643681101  

@sdkrystian when a user offers to submit a contribution, let them :)

---

## Comment 13

> Username: vinniefalco  
> Created at: 2020-06-13 21:37:50 UTC  
> Url: https://github.com/boostorg/json/issues/111#issuecomment-643681674  

@AeroStun I left comments: https://github.com/AeroStun/json/commit/5f4d3fca4e4fa5112bdcbf928a5b3c15872aa751

---

## Comment 14

> Username: AeroStun  
> Created at: 2020-06-13 22:06:19 UTC  
> Url: https://github.com/boostorg/json/issues/111#issuecomment-643684202  

@vinniefalco Addressed comments: https://github.com/AeroStun/json/commit/333e3bb5b885e5ca97d3c8fe49155f191a1eca6d

---

## Comment 15

> Username: AeroStun  
> Created at: 2020-06-14 00:07:45 UTC  
> Url: https://github.com/boostorg/json/issues/111#issuecomment-643697040  

I'm pretty much ready to squash and PR except clang-3.8 on Travis fails, likely due to a compiler bug  
https://travis-ci.com/github/AeroStun/json/jobs/348807479#L1276  
Googling around seems to suggest that this is related to https://bugs.llvm.org/show_bug.cgi?id=17537#c4  
How do you want to handle old compilers having nasty bugs?

---

## Comment 16

> Username: vinniefalco  
> Created at: 2020-06-14 01:30:06 UTC  
> Url: https://github.com/boostorg/json/issues/111#issuecomment-643704853  

Well it depends. Does Boost.JSON support clang-3.8?

---

## Comment 17

> Username: vinniefalco  
> Created at: 2020-06-14 01:30:47 UTC  
> Url: https://github.com/boostorg/json/issues/111#issuecomment-643704886  

Unfortunately it does (according to the docs)  
  
>Tested with these compilers: msvc-14.1+, gcc 4.8.4+, clang 3.6+.  
  
We have to figure out a work-around.

---

## Comment 18

> Username: vinniefalco  
> Created at: 2020-06-14 01:31:20 UTC  
> Updated at: 2020-06-14 01:31:30 UTC  
> Url: https://github.com/boostorg/json/issues/111#issuecomment-643704931  

Please submit the branch as a pull request, even with the error on clang-3.8, thanks!

---

## Comment 19

> Username: AeroStun  
> Created at: 2020-06-14 01:31:58 UTC  
> Url: https://github.com/boostorg/json/issues/111#issuecomment-643705005  

I found a workaround in the meantime  
Going to squash and PR

---

## Comment 20

> Username: sdkrystian  
> Created at: 2020-07-01 03:41:37 UTC  
> Url: https://github.com/boostorg/json/issues/111#issuecomment-652170355  

Addressed by #112
