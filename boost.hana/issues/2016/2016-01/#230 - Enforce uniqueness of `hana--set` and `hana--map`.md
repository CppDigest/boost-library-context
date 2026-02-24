# #230 - Enforce uniqueness of `hana::set` and `hana::map` [Closed]

> Username: m-j-w  
> Created at: 2016-01-03 18:33:08 UTC  
> Updated at: 2016-01-06 18:28:20 UTC  
> Closed at: 2016-01-06 18:28:20 UTC  
> Url: https://github.com/boostorg/hana/issues/230  

Seems like the list of open issues is getting rather short. So let's change that ;-)  
  
The documentation states for `hana::set` and `hana::map` the uniqueness of elements and keys, respectively. For `hana:set` it also states the uniqueness to be enforced. This, however, is currently not true:  
  
``` cpp  
#include <boost/hana.hpp>  
#include <boost/hana/experimental/printable.hpp>  
#include <iostream>  
namespace hana = boost::hana;  
using namespace hana::experimental;  
using namespace hana::literals;  
  
int main() {  
    // Check hana::set for uniqueness of elements  
  auto s = hana::make_set(0_c, hana::type_c<char>, 1_c, 1_c);  
  std::cout << print(s) << '\n';  // prints {0, type<char>, 1, 1}  
    // Check hana::map for uniqueness of keys  
  auto m = hana::make_map( hana::make_pair( 0_c, '0' ), hana::make_pair( 0_c, '1' ) );  
  std::cout << print(m) << '\n';  // prints {0 => 0, 0 => 1}  
}  
```  
  
Now, it doesn't make much sense to check uniqueness every time a set or map is constructed, in particular if it is constructed from an already existing one. I think there are two possibilities:  
1. Misuse the two different make versions for differentiation: Use `make<set_tag>` and `make<map_tag>` to construct without enforcing uniqueness, basically as the variant to be used in lower-level code; and, use `make_set` and `make_map` as the variants to always enforcing uniqueness as the default version for the user.  
2. Extend `make<...>` (or a helper) with a template parameter `when<is_truly_unique<keys>::value>` which would then only be have to evaluated once. All subsequent modifications (insert, append, delete) would already be the `when<true>` case. The `when<false>` could raise the appropriate static_assert error.   
  
Why implement that at all ? Ensuring uniqueness is somewhat intrinsically implied to the concept of the both, and hence there should be a default implementation which is also enforced if not explicitly omitted.  
  
Also, if `hana::set` were to actually enforce uniqueness, `hana::map` might be implemented instead of a tuple of key/value pairs as a pair of set-of-keys/tuple-of-values of same size.  
This would accordingly reduce the effort for an an efficient uniqueness implementation to just one location in the library. Furthermore, assuming that implementation to be really efficient, both containers would always benefit equally from performance improvements and would behave exactly same regarding uniqueness verification.  
Lastly, the separation into a keys and a value list might give run-time performance since the data keys would not be interspersed in memory by the values (cache-locality).  
  
I have no idea how that fits in to #223 and #226. Please also see my next issue on the `equal` comparator.  
  
Btw: Thanks for the `Printable` - makes life a whole lot easier...

---

## Comment 1

> Username: ricejasonf  
> Created at: 2016-01-04 19:01:14 UTC  
> Url: https://github.com/boostorg/hana/issues/230#issuecomment-168770369  

Would it be acceptable to have the duplicate key/values continue to be stored but not accessible? This would put a (probably) small hit on `unpack` to ensure uniqueness (probably by using `at_key`).   
  
A clean, unique, associative constainer can still be constructed with `hana::insert`.

---

## Comment 2

> Username: m-j-w  
> Created at: 2016-01-04 21:05:03 UTC  
> Url: https://github.com/boostorg/hana/issues/230#issuecomment-168808429  

From an implementation point of view that would certainly make life easier. However, from a conceptual point of view I think that would allow ambiguity. Which of the duplicates would be the one; the first, the last, some arbitrary other? User code would then depend on the implementation details. Also, adding a duplicate should in my opinion raise an immediate error, since that is conceptually a misuse of a set or map and would certainly indicate an error on the user side. (I'd consider adding and replacing an element to be different in that sense).  
  
Personally, I'd probably favour option 2 of the above: A single check on first creation. Subsequent insertations would not have to repeat the full uniqueness check.  
(I know the following doesn't really work, but I don't know a name for that design pattern... Conditional tag dispatching ?)  
  
``` cpp  
template <typename... Xs, bool Condition = is_truly_unique<Xs...>::value>  
struct make_uniq_helper<set_tag> : make_uniq_helper<set_tag, when<Condition>> { };  
  
template <>  // elements are unique, create the set.  
struct make_uniq_helper<set_tag, when<true>> { /* do stuff */ };  
  
template <>  // elements are not unique, give error.  
struct make_uniq_helper<set_tag, when<false>> { /* raise error */ };  
```  
  
The `is_truly_unique` would 'just' have to count occurences for each elem in elements, compare against 1, and (although redundant) check the sum of all 1's against the size of elements. But you most likely know that already... So it shouldn't be _that_ expensive after all, also considering your recent efforts in map_elt...

---

## Comment 3

> Username: ldionne  
> Created at: 2016-01-04 21:11:57 UTC  
> Url: https://github.com/boostorg/hana/issues/230#issuecomment-168810171  

I think the issue here is just that the documentation is a bit misleading. The documentation for `set` currently says:  
  
> A set is an unordered container that can hold heterogeneous objects. As usual, a set requires (and ensures) that no duplicates are present.  
  
It is false that `hana::set` itself checks for uniqueness, in the sense that it won't yell at you if you create one with duplicate elements. **However**, there are basically two ways of creating a `hana::set`. The first way is to use `hana::make_set` (or `hana::make<hana::set_tag>` if you like typing). But `hana::make<hana::set_tag>` clearly states the following (emphasis mine):  
  
> Given zero or more values `xs...`, `make<set_tag>` returns a set containing those values. The values must all be compile-time `Comparable`, **and no duplicate values may be provided**. To create a set from a sequence with possible duplicates, use `to<set_tag>` instead.  
  
The second way of creating a set is to use `hana::insert` (or `hana::to_set`, which uses `hana::insert`). However, `hana::insert` will only insert the element if it is not already in the set, thus ensuring uniqueness.  
  
Hence, because of the way `hana::set` can be used is restricted, `hana::set` ensures the uniqueness of its elements (by construction). This is how I saw it when I wrote the documentation. Perhaps we should rewrite it more clearly?  
  
Given my interpretation, do you still think this is an actual issue?

---

## Comment 4

> Username: m-j-w  
> Created at: 2016-01-04 21:26:38 UTC  
> Url: https://github.com/boostorg/hana/issues/230#issuecomment-168814456  

Personally, I'd prefer the yelling variant, also because `make<...>` is probably the more intuitive way since being used for practically everything. But, of course, changing the documentation is also feasible, and requires less maintenance...

---

## Comment 5

> Username: ldionne  
> Created at: 2016-01-04 21:47:45 UTC  
> Url: https://github.com/boostorg/hana/issues/230#issuecomment-168820307  

To make sure I understand, you would prefer `hana::make_set` to trigger an error if duplicate elements are found?  
  
The reason why it was done the way it is right now is that we crucially need an efficient way of creating `hana::set`s and `hana::map`s, i.e. one that does not check for uniqueness. Also, I assumed that in most cases, a sequence that _might_ contain duplicates would come from a tuple and not a variadic parameter pack anyway, and in this case writing `hana::to_set(sequence)` makes much sense.  
  
I am quite reluctant to add a special-purpose `make` function just to check for uniqueness (or to _not_ check for uniqueness, and to have the default do a check). Doing so makes the library interface more complex for a questionable gain.  
  
Without introducing a new specific function for your use case, I see the following options.  
  
First, you can use `hana::to_set(hana::make_tuple(args...))`. However, this might be more verbose than you'd like, and it also requires creating an intermediate tuple.   
  
Secondly, we could provide the `hana::variadic` combinator that was ditched in #85, and then we could use `hana::variadic(hana::to_set)(args...)`. However, this is not much shorter, and it would probably also create an intermediate tuple under the hood.  
  
Thirdly, we could add a check for uniqueness in `hana::make_set`, but this check would only be performed when some configuration macro is defined. This configuration macro could be something like `BOOST_HANA_CONFIG_DISABLE_PRECONDITIONS`, and would be very similar in effect to `BOOST_HANA_CONFIG_DISABLE_CONCEPT_CHECKS`, except for preconditions and other advanced (and perhaps costly) checks.  
  
Off the top of my head, I tend towards the third option.

---

## Comment 6

> Username: m-j-w  
> Created at: 2016-01-04 22:05:05 UTC  
> Url: https://github.com/boostorg/hana/issues/230#issuecomment-168825512  

> To make sure I understand, you would prefer hana::make_set to trigger an error if duplicate elements are found?  
  
Yepp, exactly my point. Same goes for the keys in `hana::set`.  
  
> I am quite reluctant to add a special-purpose make function...  
  
I would be too. Wouldn't really be long-term sustainable.  
  
> Off the top of my head, I tend towards the third option.  
  
That one I like! In particular since costly checks can then be deactivated when not actively developing a piece of code where maps/sets are constructed.

---

## Comment 7

> Username: ldionne  
> Created at: 2016-01-04 22:27:45 UTC  
> Url: https://github.com/boostorg/hana/issues/230#issuecomment-168830801  

Yes, and we could include the current checks that all keys are `Comparable` in such a macro. These checks are also quite expensive.

---

## Comment 8

> Username: m-j-w  
> Created at: 2016-01-05 13:53:35 UTC  
> Url: https://github.com/boostorg/hana/issues/230#issuecomment-169007277  

Excellent !

---

## Comment 9

> Username: ricejasonf  
> Created at: 2016-01-06 07:19:33 UTC  
> Url: https://github.com/boostorg/hana/issues/230#issuecomment-169255493  

For most use cases enforcing uniqueness isn't necessary. For cases that need it, I think it should be up to the user to add the assertion.  
  
Also, it doesn't appear that there is precedence for enforcing uniqueness in these types of containers:  
- http://stackoverflow.com/questions/9820363/why-does-boost-mpl-set-allow-non-unique-types  
- http://stackoverflow.com/questions/20647161/why-doesnt-boostfusionas-set-remove-duplicates-from-a-boostfusionvector  
  
On another note, if `hana::map` keys were strictly types, couldn't we then add a superficial base class for each type and let the compiler explode when it encounters a duplicate base class? I mean, that sounds like it would be fast. :stuck_out_tongue:

---

## Comment 10

> Username: ldionne  
> Created at: 2016-01-06 14:28:43 UTC  
> Url: https://github.com/boostorg/hana/issues/230#issuecomment-169336341  

The fact that MPL and Fusion do not enforce uniqueness is, IMO, a design flaw. Having used these libraries quite a lot in the past, I explicitly wanted Hana to have proper associative sequences, i.e. ones that ensure the uniqueness of elements. The issue that we're looking at right now is simply that of how to ensure this requirement, but I'm about to push a commit that implements it.
