# #524 - Equality behavior between value and other types [Closed]

> Username: doganulus  
> Created at: 2021-03-07 21:05:18 UTC  
> Updated at: 2024-04-08 16:44:01 UTC  
> Closed at: 2024-04-08 16:44:00 UTC  
> Url: https://github.com/boostorg/json/issues/524  

Currently following equality checks evaluate to `true` for `json::value`.   
  
```cpp  
BOOST_TEST(value(nullptr) == nullptr);  
BOOST_TEST(value(true) == true);  
BOOST_TEST(value(false) == false);  
BOOST_TEST(value(13) == 13);  
BOOST_TEST(value("abc") == "abc");  
BOOST_TEST(value({1,2,3}) == array({1,2,3}));  
BOOST_TEST(value({{"a", 1}, {"b",2}}) == object({{"a", 1}, {"b",2}}));  
```  
  
On the other hand, the case of `std::variant` has a different behavior such that   
```cpp  
using number = std::variant<int, double>;  
assert((number{12} == 12)); // error: no match for ‘operator==’ (operand types are ‘std::variant’ and ‘int’)  
```  
  
Now, although `json::value` is a `variant`-like type, I think it is acceptable that it deviates from the `std` and has specialized behavior (like erasing the identity of `json::value` in equality checks). But this may mean over-javascriptized equality semantics (not really a fan of JS type conversions) so I wonder the general opinion in this matter.   
  
This is also related to #522 as hash and equality behaviors must be compatible.

---

## Comment 1

> Username: vinniefalco  
> Created at: 2021-03-07 21:34:19 UTC  
> Url: https://github.com/boostorg/json/issues/524#issuecomment-792357291  

In order to be used in an unordered container, `value` has to provide some sort of equality operator. If we use the proposed `std::hash` in the pull request, then I think there's a problem where digests for different instances of `value` (a signed and an unsigned with the same positive number) can be different, while `operator==` will say they are the same. @pdimov ?

---

## Comment 2

> Username: pdimov  
> Created at: 2021-03-07 22:28:09 UTC  
> Url: https://github.com/boostorg/json/issues/524#issuecomment-792365181  

I don't understand this comment.

---

## Comment 3

> Username: doganulus  
> Created at: 2021-03-08 12:34:49 UTC  
> Updated at: 2021-03-08 20:54:06 UTC  
> Url: https://github.com/boostorg/json/issues/524#issuecomment-792726799  

I think I have needed some clarifications regarding the desired equality behavior of `json::value`. Since I only follow here, it may have been already discussed in other places but here is my current thinking.   
  
I think there are at least two useful definitions for the equality behavior for users.   
1. C++ standard type-aware equality  
    * Follows the equality behavior of `std` containers  
    * Differentiate number types and `json::value` type (because `std::variant` does)  
    * Does not differentiate the insertion order for `json::object` (because `std::unordered_map` does not)  
2. JSON semantic equality   
   * Follows JSON standard  
   * Number types are not differentiated, i.e. `assert(value(7) == value(7U) == value(7.0))`  
   * `value` type is ignored in comparison, i.e. `assert(array({"a", 1, true}) == value({"a", 1, true}))`  
   * But still differentiate JSON types, i.e.`assert(value({}) != value(""))`  
  
Both definitions would have their own use cases and may be provided to users in my opinion. Then I think `std::hash` specializations may follow (1) and aims to be compatible with standard type aware equality definition. In the standard library, such behavior can be seen from `std::hash` values of `int` and `double` is different while they compare equal by `operator==`.

---

## Comment 4

> Username: pdimov  
> Created at: 2021-03-08 14:52:17 UTC  
> Url: https://github.com/boostorg/json/issues/524#issuecomment-792807521  

std::hash specializations must follow whatever the type's operator== does. They have no opinion of their own on what it ought to do.

---

## Comment 5

> Username: doganulus  
> Created at: 2021-03-08 20:49:27 UTC  
> Url: https://github.com/boostorg/json/issues/524#issuecomment-793066611  

That's almost clear to me. First I thought the inequality of hash values of `7` and `7.0` while they compare equal by `operator==` could be an exception for that rule. But probably it is not technically correct as implicit type conversions involved there too.   
  
Still this issue is mostly about the equality behavior(s) itself.

---

## Comment 6

> Username: pdimov  
> Created at: 2021-03-08 20:58:11 UTC  
> Url: https://github.com/boostorg/json/issues/524#issuecomment-793072098  

I don't think that `value(7)` and `value(7.0)` compare equal?

---

## Comment 7

> Username: doganulus  
> Created at: 2021-03-08 21:04:37 UTC  
> Url: https://github.com/boostorg/json/issues/524#issuecomment-793076223  

More basic than that. I was talking about `std::hash<int>{}(7) != std::hash<double>{}(7.0)` while `7 == 7.0`.

---

## Comment 8

> Username: pdimov  
> Created at: 2021-03-08 21:13:37 UTC  
> Url: https://github.com/boostorg/json/issues/524#issuecomment-793081984  

The expression `7 == 7.0` doesn't actually compare `7` and `7.0`. It converts `7` to `7.0`, then compares `7.0` and `7.0`.

---

## Comment 9

> Username: grisumbras  
> Created at: 2024-04-08 16:44:01 UTC  
> Url: https://github.com/boostorg/json/issues/524#issuecomment-2043212698  

This appear to be resolved.
