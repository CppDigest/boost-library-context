# #269 static_assert on the constructibility of the containers' types [Merged]

> Username: k3DW  
> Created at: 2024-07-25 19:06:21 UTC  
> Updated at: 2024-08-08 16:57:25 UTC  
> Merged at: 2024-08-08 16:57:22 UTC  
> Closed at: 2024-08-08 16:57:22 UTC  
> Url: https://github.com/boostorg/unordered/pull/269  

Closes #191   
  
It turns out `type_policy::construct()` is the common place where we hit errors when the types are not move-constructible. This change encompasses all problematic places like `.emplace()` or `.insert()`, but this does not affect the non-problematic places like the default constructor.

---

## Comment 1

> Username: cmazakas  
> Created_at: 2024-07-26 14:38:58 UTC  
> Updated_at: 2024-07-26 14:40:36 UTC  
> Url: https://github.com/boostorg/unordered/pull/269#issuecomment-2252911606  

Hmm, maybe I'm missing something but doesn't it make more sense to just do like:  
```cpp  
template<class Key, ....>  
class unordered_flat_map   
{  
    static_assert(std::is_move_constructible<value_type>::value, "");  
};  
```  
?  
  
Note the original issues says:  
> Unfortunately, if you use a type that is not move-constructible, the compiler generates an enormous wall of cryptic errors instead... because the compilation failure occurs inside of a deep chain of template instantiations.  
  
I think putting the static_assert() far away from the deep internal implementation details might lead to nicer error messages but I haven't tested locally.  
  
Braden, do you know what some of  the error output looks like with your current PR vs what it'd look like with my suggestion?

---

## Comment 2

> Username: k3DW  
> Created_at: 2024-07-26 14:46:40 UTC  
> Url: https://github.com/boostorg/unordered/pull/269#issuecomment-2252926563  

> Braden, do you know what some of the error output looks like with your current PR vs what it'd look like with my suggestion?  
  
The error output would look the same, but errors would occur in more places with your suggestion. Things like the default constructor, calling `.size()`, etc, all of these functions don't currently cause a compilation error, and that behaviour is maintained with my suggestion in this PR. The functions that *do* cause compilation errors with cryptic error messages instead get this nice `static_assert`.  
  
I was under the assumption that I should maintain the current behaviour, but I'm open to putting the `static_assert` in the class body instead.

---

## Comment 3

> Username: cmazakas  
> Created_at: 2024-07-26 16:41:02 UTC  
> Url: https://github.com/boostorg/unordered/pull/269#issuecomment-2253117612  

Thinking about it some more, I think we need:  
  
```cpp  
          template <class A, class... Args>  
          static void construct(A& al, init_type* p, Args&&... args)  
          {  
            // note we're checking `init_type` here  
            static_assert(std::is_move_constructible<init_type>::value,  
              "key_type and mapped_type must be move constructible");  
            boost::allocator_construct(al, p, std::forward<Args>(args)...);  
          }  
  
          template <class A, class... Args>  
          static void construct(A& al, value_type* p, Args&&... args)  
          {  
            static_assert(std::is_move_constructible<value_type>::value,  
              "key_type and mapped_type must be move constructible");  
            boost::allocator_construct(al, p, std::forward<Args>(args)...);  
          }  
  
          template <class A, class... Args>  
          static void construct(A& al, key_type* p, Args&&... args)  
          {  
            static_assert(std::is_move_constructible<key_type>::value,  
              "key_type must be move constructible");  
            boost::allocator_construct(al, p, std::forward<Args>(args)...);  
          }  
```  
  
Note that the docs also cite `value_type` must be move-constructible: https://www.boost.org/doc/libs/develop/libs/unordered/doc/html/unordered.html#compliance_open_addressing_containers  
  
I think it's only valid to check the key_type by itself for the last overload of `construct()`.  
  
It also seems like the STL will delay the static_assert() in a similar fashion. I suppose the motivation is to simply not break builds, regardless of how silly the code winds up being.

---

## Comment 4

> Username: k3DW  
> Created_at: 2024-07-26 17:29:45 UTC  
> Updated_at: 2024-07-26 17:38:17 UTC  
> Url: https://github.com/boostorg/unordered/pull/269#issuecomment-2253186853  

> Thinking about it some more, I think we need: ...  
  
I tried this at first, but it broke some of the tests. Sometimes `value_type` is not move constructible even when `key_type` is, so checking for `std::is_move_constructible<value_type>` will be incorrect ([Compiler Explorer link](https://godbolt.org/z/97P9r168e)). However, it needs to be checked in the `value_type` overload of `construct()` otherwise it doesn't catch the errors in the right places.  
  
If anything, I'd suggest we check for both `key_type` and `mapped_type` being move constructible in all 3 places. Those are the actual constraints, and anything else just falls out of that.  
  
> Note that the docs also cite value_type must be move-constructible:  
  
Ah I see. Well I've found that this isn't true. I think the `key_type` and `mapped_type` must be move constructible, but the `value_type` sometimes isn't.

---

## Review 5 [Commented]

> Username: joaquintides  
> Created_at: 2024-07-29 09:38:37 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/unordered/pull/269#pullrequestreview-2204445929  

Several observations about the current status of this PR:  
  
* Type policies are the right place to locate these assertions, as key/value construction always go through them. Note, however, that node policies `node_map_types.hpp` and `node_set_types.hpp` haven't been instrumented (and they should).  
* Strictly speaking, the containers (as all STL containters) do not impose, say, [move constructibility](https://en.cppreference.com/w/cpp/named_req/MoveConstructible) but [_move insertability_](https://en.cppreference.com/w/cpp/named_req/MoveInsertable), involving a construction call to the allocator. In general, we can't static assert with any allocator cause their internals are opaque to us, so these assertions need be restricted to `std::allocator` or, if we want to be exquisite, to allocators without a defined `constuct` member function.   
  
I'd organize all of this as follows:  
* Have all four type policies delegate assertion to two static assertion facilities (one for maps, one for sets).  
* These facilities won't do anything if the allocator is not `std::allocator` (or, as mentioned above, if the allocator has a `construct` member function).  
* They are passed the types `Allocator`, `ConstructedType` (any of `init_type`, `value_type` or `key_type`), and `Args...`. We can then provide as many specializations/overloads as required, for instance:  
  * `std::allocator`, `key_type`, `key_type&&` --> assert `key_type` move constructibility.  
    
Happy to discuss further details/doubts about this approach.

---

## Comment 6

> Username: k3DW  
> Created_at: 2024-07-31 15:20:40 UTC  
> Url: https://github.com/boostorg/unordered/pull/269#issuecomment-2260778757  

I think we need to discuss this more outside of these comments, I have some follow-up questions.

---

## Comment 7

> Username: k3DW  
> Created_at: 2024-08-02 18:14:26 UTC  
> Url: https://github.com/boostorg/unordered/pull/269#issuecomment-2265906587  

Sorry for all the pushes, I forgot my branch was linked to this PR.

---

## Review 8 [Changes requested]

> Username: joaquintides  
> Created_at: 2024-08-04 10:18:27 UTC  
> State: CHANGES_REQUESTED  
> Url: https://github.com/boostorg/unordered/pull/269#pullrequestreview-2217554164  

I decided to provide a general review rather than in-code comments, I think it's going to be clearer this way:  
  
* In `node_(set|map)_types.hpp` you're not ever invoking the checker when constructing `element_type`s, only when the user-related `init_type`, `value_type` etc. are constructed. This is correct.  
* Now, there is no need to have different checkers for flat and node scenarios, as `node_(set|map)_types.hpp` already make `element_type` invisible to the checker. That is, you can have one map checker and one set checker alone.  
* There's a low of duplication of typedefs as in:  
```cpp  
using key_type = Key;  
using mapped_type = T;  
using raw_key_type = typename std::remove_const<Key>::type;  
using raw_mapped_type = typename std::remove_const<T>::type;  
```  
This is a code smell. Instead, you can have the checker be parameterized by `TypePolicy` and extract the typedefs from there, without redefinition.  
* `static_assert(std::is_constructible<X, Args...>::value, ...)` should be `static_assert(std::is_constructible<X, Args&&...>::value, ...)` (several places).  
* The map checker is too coarse. For instance, this is not detected by the checker:  
```cpp  
boost::unordered_node_map<nonmoveable,int> x;  
x.emplace(nonmoveable{},0);  
```  
This is because emplacement is done through [`std::piecewise_construct`](https://github.com/k3DW/unordered/blob/191/include/boost/unordered/detail/foa/core.hpp#L2128-L2132), and the coresponding `std::pair` constructor is not SFINAE-enabled (at least in MSVC), so the checker happily passes it. To make the map checker really powerful, we need to provide `check` overloads for  
```cpp  
(value_type*, Arg1&&, Arg2&&)  
(value_type*, const std::pair<Arg1, Arg2>&)  
(value_type*, std::pair<Arg1, Arg2>&&)  
(value_type*, std::piecewise_construct&&, std::tuple<Arg1>&&, std::tuple<Arg2>&&,)  
(value_type*, std::piecewise_construct&&, std::tuple<Arg1>&&, std::tuple<Args2...>&&,)  
(value_type*, std::piecewise_construct&&, std::tuple<Args1...>&&, std::tuple<Args2>&&,)  
```  
(and same for `init_type*`) and then internally derive to individual checkers for the key and mapped part. Incidentally, this strategy renders the current `(value_type*, moved_type&&)` and `(init_type*, moved_type&&)` overloads superfluous and will provide more informative error messages than "value_type must be move constructible".

---

## Comment 9

> Username: k3DW  
> Created_at: 2024-08-05 21:23:22 UTC  
> Url: https://github.com/boostorg/unordered/pull/269#issuecomment-2269943208  

Alright, I implemented something similar to what you suggested. I wanted to ensure your non-functioning example not only became functional, but also that I specifically give error messages about invalid move construction separately from the other error messages. I'm sure that's the most common error of this type to be hit. I think the users will appreciate seeing `key_type is not move constructible`, instead of seeing `key_type is not constructible from Args` when `Args` is just `key_type&&` anyway.

---

## Review 10 [Changes requested]

> Username: joaquintides  
> Created_at: 2024-08-07 17:06:16 UTC  
> State: CHANGES_REQUESTED  
> Url: https://github.com/boostorg/unordered/pull/269#pullrequestreview-2221691136  

📁 include/boost/unordered/detail/foa/types_constructibility.hpp

```diff
   9 |+ #include <type_traits>
  10 |+ #include <utility>
  11 |+ 
```

> Username: joaquintides  
> Created_at: 2024-08-07 16:50:30 UTC  
> Updated_at: 2024-08-07 17:06:16 UTC  
> Url: https://github.com/boostorg/unordered/pull/269#discussion_r1707440561  

Missing `<tuple>` include (though `std::pair` needs to include it internally.)

---

📁 include/boost/unordered/detail/foa/types_constructibility.hpp

```diff
  35 |+             "mapped_type must be move constructible");
  36 |+         };
  37 |+ 
```

> Username: joaquintides  
> Created_at: 2024-08-07 17:04:26 UTC  
> Updated_at: 2024-08-07 17:06:16 UTC  
> Url: https://github.com/boostorg/unordered/pull/269#discussion_r1707467033  

It'd be nice to add a `check_mapped_type_t<Mapped>`  specialization (default construction) to cover this:  
```cpp  
#include <boost/unordered/unordered_flat_map.hpp>  
  
struct X  
{  
  X(int){}  
};  
  
std::size_t hash_value(const X&){return 0;}  
bool operator==(const X&,const X&){return true;}  
  
int main()  
{  
  boost::unordered_flat_map<int,X> m;  
  m[0]=X(0);  
}  
```

> Username: k3DW  
> Created_at: 2024-08-07 19:12:47 UTC  
> Updated_at: 2024-08-07 19:15:47 UTC  
> Url: https://github.com/boostorg/unordered/pull/269#discussion_r1707717573  

Thanks. I'd prefer to add a similar specialization for `check_key_type_t<Key>` as well, so I'll do that too


> Username: joaquintides  
> Created_at: 2024-08-07 17:01:02 UTC  
> Updated_at: 2024-08-07 17:06:16 UTC  
> Url: https://github.com/boostorg/unordered/pull/269#discussion_r1707460551  

L75: missing a check on copy construction of `value_type`. Same for `node_set_types.hpp`.

> Username: k3DW  
> Created_at: 2024-08-07 19:11:24 UTC  
> Url: https://github.com/boostorg/unordered/pull/269#discussion_r1707715021  

This is not missing. It's calling into a different overload of `construct()` which _does_ have the check, namely the overload on L100. I don't think it makes sense to add the check twice.

> Username: joaquintides  
> Created_at: 2024-08-07 19:32:55 UTC  
> Url: https://github.com/boostorg/unordered/pull/269#discussion_r1707751583  

Oh, you're right, sorry for the confusion.


---

## Review 11 [Commented]

> Username: joaquintides  
> Created_at: 2024-08-07 17:10:37 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/unordered/pull/269#pullrequestreview-2225753962  

📁 include/boost/unordered/detail/foa/types_constructibility.hpp

```diff
  24 |+             "key_type must be move constructible");
  25 |+         };
  26 |+ 
```

> Username: joaquintides  
> Created_at: 2024-08-07 17:10:37 UTC  
> Updated_at: 2024-08-07 17:11:31 UTC  
> Url: https://github.com/boostorg/unordered/pull/269#discussion_r1707481534  

It'd be nice to add a `check_key_type_t<Key, const Key&>` specialization to cover this:  
```cpp  
#include <boost/unordered/unordered_flat_map.hpp>  
  
struct X  
{  
  X(){}  
  X(const X&)=delete;  
};  
  
std::size_t hash_value(const X&){return 0;}  
bool operator==(const X&,const X&){return true;}  
  
int main()  
{  
  boost::unordered_flat_map<X,X> m;  
  auto m2 = m;  
}  
```  
Current message is 'key_type must be constructible from Args', which is not so useful (the user has to find out `Args...`  is `const key_type&`). Same for `check_mapped_type_t`.


---

## Comment 12

> Username: k3DW  
> Created_at: 2024-08-08 16:55:57 UTC  
> Url: https://github.com/boostorg/unordered/pull/269#issuecomment-2276267014  

I got a verbal approval from @cmazakas outside of these comments. I'll go ahead and merge.

---
