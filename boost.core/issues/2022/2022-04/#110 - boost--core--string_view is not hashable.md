# #110 - boost::core::string_view is not hashable [Closed]

> Username: madmongo1  
> Created at: 2022-04-24 10:09:14 UTC  
> Updated at: 2022-09-18 10:13:43 UTC  
> Closed at: 2022-09-18 10:13:43 UTC  
> Url: https://github.com/boostorg/core/issues/110  

ideally the free function overload `boost::core::hash_value(boost::core::string_view)` would be made available so that string_views can take part in heterogeneous lookups in unordered maps.  
  
This overload should of course produce the same hash as for the equivalent:  
std::string  
boost::json::string  
boost::string_view  
std::string_view

---

## Comment 1

> Username: pdimov  
> Created at: 2022-04-28 17:58:35 UTC  
> Url: https://github.com/boostorg/core/issues/110#issuecomment-1112503872  

> This overload should of course produce the same hash as for the equivalent:  
> std::string  
> boost::json::string  
> boost::string_view  
> std::string_view  
  
This actually brings up an interesting point. There are scenarios in which these won't necessarily match, assuming a typical implementation of `return hash_range(v.begin(), v.end());`. That's because `begin`/`end` return iterators, and the iterator types of `std::string`, `boost::json::string`, `boost::string_view`, `std::string_view`, `boost::core::string_view` may in some cases be real class types, and in other cases pointers. And it's quite possible that in the future `hash_range` acquires an optimization such that if you call it with `char*` it uses a faster code path where it hashes `sizeof(size_t)` chars at a time.  
  
This is I think fixable, but it's something to keep in mind. I suppose I need to add test cases for this somewhere.

---

## Comment 2

> Username: sehe  
> Created at: 2022-04-28 18:14:16 UTC  
> Url: https://github.com/boostorg/core/issues/110#issuecomment-1112517205  

```c++  
return boost::hash_range( sv.begin(), sv.end() );  
```  
  
Love to see it. I was wondering whether you'd approve of depending on ContainerHash for this.  
  
-------  
  
Side note: if/since there is interop between std::string_view and core::string_view, wouldn't it make sense to express hash support in terms of that, something like  
  
```c++  
    template <typename...Ts> struct boost::hash<boost::core::basic_string_view<Ts...> >   
          : boost::hash<std::basic_string_view<Ts...> > {};  
```  
  
(variadics as shortcut for exposition here)  
  
That way even if the behaviour of one implementation changes, it would stay the same. I realize that explicit converting adaptors will be required for other implementations, but it would help to express the common semantics.

---

## Comment 3

> Username: Lastique  
> Created at: 2022-04-28 21:00:30 UTC  
> Url: https://github.com/boostorg/core/issues/110#issuecomment-1112650016  

IMHO, hash equivalence between different string types should not be guaranteed. Or even attempted to be achieved.  
  
First, we can't guarantee this across all string types in the existence, so there will always be the case where one has two string types that are hashed differently. You could attempt to make Boost and std types aligned, but that's about the extent of what we can do, and even that is problematic. For example, we could reuse `std::hash<std::string_view>`, but what about targets that don't have `std::string_view`? We can't reuse `std::hash<std::string>` because that would require `std::string` construction. So the only solution would be to reimplement the same hash function that every std library implementation provides, which is obviously fragile and not scalable (we can't reasonably cover every std library).  
  
Second, hash equivalence can be achieved by users relatively easily, *where needed*. One way is to convert every string type to a single string view type before hashing, be that `boost::core::string_view` or something else. Another way is to specify a user-defined hash function that would support all string types that the user needs that will guarantee hash equivalence.  
  
Third, we should be able to modify the hash function in the future. This may be needed for various reasons - improving performance, security, whatever. We should not be locked to a specific implementation.

---

## Comment 4

> Username: sehe  
> Created at: 2022-04-28 21:10:31 UTC  
> Updated at: 2022-04-28 21:12:10 UTC  
> Url: https://github.com/boostorg/core/issues/110#issuecomment-1112657310  

@Lastique   
  
> but what about targets that don't have std::string_view  
  
We could of course pick `boost::hash<boost::core::string_view>` as the default.   
  
> Second, hash equivalence can be achieved by users relatively easily, where needed  
  
True. Perhaps it's not a good use of our time to try to promise such things. This happens to be what I've been doing for quite some time, and it wasn't much of a burden (in fact, it gives expressive code that makes assumptions explicit).  
  
> Third, we should be able to modify the hash function in the future  
  
I don't think anyone was suggesting casting the actual algorithm in stone. I think @madmongo1 and @pdimov where considering doing things to prevent different types that hash the same today to start hashing differently in the future. That leaves all the room for changing the algorithm, or even having different outcomes (e.g. salts) across runs.  
  
Summarizing:  
  
 - @madmongo1 / @pdimov do we want to keep these hashes "together" for the group of character-sequence (string-likes)?  
 - @sehe : _if we do_, let's make the relation explicitly by relaying down to a common implementation  
 - @Lastique it's not trivial and adds limited value to end-users.  
  
I'm actually quite on board with that reasoning.

---

## Comment 5

> Username: madmongo1  
> Created at: 2022-04-28 22:40:21 UTC  
> Url: https://github.com/boostorg/core/issues/110#issuecomment-1112718832  

> IMHO, hash equivalence between different string types should not be guaranteed. Or even attempted to be achieved.  
@Lastique   
  
If `string_view` and `string` dont hash to the same value, then you can't achieve heterogeneous lookup in unordered maps, which makes the exercise of providing `hash_value` for `string_view` pointless.

---

## Comment 6

> Username: sehe  
> Created at: 2022-04-28 23:18:58 UTC  
> Updated at: 2022-04-28 23:19:49 UTC  
> Url: https://github.com/boostorg/core/issues/110#issuecomment-1112739614  

You can, it just won't be automatic. http://www.open-std.org/jtc1/sc22/wg21/docs/papers/2018/p0919r3.html shows some reasoning for that.  
  
Anyways, `std::hash<std::string_view>` isn't marked as transparent anyways AFAIK.  
  
I usually do this for more complicated keys (like interprocess shared strings):  
  
https://godbolt.org/z/TfsEooffc (prints 1, 3, true, false)  
  
```c++  
#include <string>  
#include <string_view>  
#include <unordered_map>  
#include <iostream>  
  
struct KeyEq : std::equal_to<> {  
    struct is_transparent {};  
};  
struct KeyHash : std::hash<std::string_view> {  
    struct is_transparent {};  
};  
  
int main(void) {  
    using namespace std::string_view_literals;  
  
    std::unordered_map<std::string, int, KeyHash, KeyEq> m{  
        {"one", 1}, {"two", 2}, {"three", 3}};  
  
#ifdef __cpp_lib_generic_unordered_lookup  
    std::cout << m.find("one"sv)->second << "\n";  
    std::cout << m.find("three")->second << "\n";  
    std::cout << std::boolalpha << m.contains("two"sv) << "\n";  
    std::cout << std::boolalpha << m.contains("four") << "\n";  
#endif  
}  
```  
  
Sidenote: The Boost Unordered/Boost Container/Boost MultiIndex have richer interfaces for heterogenous lookup/erase still (allowing one even to do heterogeneous lookups when the key_eq/key_hash aren't transparent).

---

## Comment 7

> Username: Lastique  
> Created at: 2022-04-29 13:27:33 UTC  
> Updated at: 2022-04-29 13:28:29 UTC  
> Url: https://github.com/boostorg/core/issues/110#issuecomment-1113310568  

> > IMHO, hash equivalence between different string types should not be guaranteed. Or even attempted to be achieved.  
> > @Lastique  
>   
> If `string_view` and `string` dont hash to the same value, then you can't achieve heterogeneous lookup in unordered maps, which makes the exercise of providing `hash_value` for `string_view` pointless.  
  
As far as I understand, even `std::string_view` and `std::string` are not required to be hash-compatible by the standard. They may *seem* to be compatible due to that invoking `std::hash<T>` would implicitly convert its argument to `T`, and `std::equal_to` would either convert or compare arguments of different types (and `std::string_view` and `std::string` support that). What really happens is that unordered containers with `std::hash` always compute the hash on the key type, even if you pass something else to `find()`.  
  
If you provide a custom hash function that accepts multiple key types, that function is supposed to produce the same hash value for equivalent arguments. But that is beyond the standard library guarantees, it is user's responsibility to maintain that.

---

## Comment 8

> Username: sehe  
> Created at: 2022-04-29 13:46:23 UTC  
> Updated at: 2022-04-29 13:47:10 UTC  
> Url: https://github.com/boostorg/core/issues/110#issuecomment-1113330869  

> As far as I understand, even std::string_view and std::string are not required to be hash-compatible by the standard.   
  
According to cppreference, [they are:](https://en.cppreference.com/w/cpp/string/basic_string_view/hash#:~:text=These%20hashes%20equal%20the%20hashes%20of%20corresponding%20std%3A%3Abasic_string%20classes)   
  
> These hashes equal the hashes of corresponding [std::basic_string](https://en.cppreference.com/w/cpp/string/basic_string) classes: If S is one of the standard basic_string types, SV is the corresponding string view type, and s is an object of type S, then `std::hash<S>()(s) == std::hash<SV>()(SV(s))`.

---

## Comment 9

> Username: Lastique  
> Created at: 2022-04-29 14:32:21 UTC  
> Url: https://github.com/boostorg/core/issues/110#issuecomment-1113394727  

Ok, I stand corrected then. But that doesn't invalidate my point re. conversion to the key type.

---

## Comment 10

> Username: sehe  
> Created at: 2022-05-02 05:13:58 UTC  
> Url: https://github.com/boostorg/core/issues/110#issuecomment-1114504030  

@pdimov I'd be happy with this hash implementation. It matches 100% with what Boost Utility's `boost::basic_string_view` has, which seems important since there is seamless interconversion between the two, and even more, Beast is switching from it to `core::string_view` so it would lead to least surprises with existing Beast users.

---

## Comment 11

> Username: madmongo1  
> Created at: 2022-05-02 05:33:10 UTC  
> Url: https://github.com/boostorg/core/issues/110#issuecomment-1114512410  

@Lastique not if the hash function is enabled for heterogeneous lookup.

---

## Comment 12

> Username: Lastique  
> Created at: 2022-05-02 09:19:32 UTC  
> Url: https://github.com/boostorg/core/issues/110#issuecomment-1114655350  

On May 2, 2022 8:33:22 AM Richard Hodges ***@***.***> wrote:  
  
> @Lastique not if the hash function is enabled for heterogeneous lookup.  
  
As I said, this would require a custom hash function. At which point hash   
compatibility is guaranteed by that custom function.

---

## Comment 13

> Username: pdimov  
> Created at: 2022-09-18 10:10:44 UTC  
> Url: https://github.com/boostorg/core/issues/110#issuecomment-1250235929  

> I usually do this for more complicated keys (like interprocess shared strings):  
  
`std::equal_to<>` is already transparent, so you can use it as-is.

---

## Comment 14

> Username: pdimov  
> Created at: 2022-09-18 10:13:43 UTC  
> Url: https://github.com/boostorg/core/issues/110#issuecomment-1250236467  

ContainerHash now has a test that the hash values of the various string/string_view types match:  
  
https://github.com/boostorg/container_hash/blob/60be3b131e5b4233fe7d2b0655851fcdcb48972e/test/hash_string_test2.cpp  
  
so I suppose this can be closed as completed.
