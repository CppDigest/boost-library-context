# #285 - Access tuple member by type: `hana::at(hana::type_c<T>, xs)` [Closed]

> Username: vittorioromeo  
> Created at: 2016-05-31 19:20:38 UTC  
> Updated at: 2017-04-05 03:47:58 UTC  
> Closed at: 2016-10-30 21:03:19 UTC  
> Url: https://github.com/boostorg/hana/issues/285  

Sometimes I have an `hana::tuple<A, B, C, D>` and I want to access members by type, like `std::get<T>` for tuples. Ideally, I would like to say `hana::at(hana:.type_c<A>, some_tuple)`. I currently found two workarounds:   
- Find the index of `A` inside the tuple, then access by index.  
- Create a `{type_c<A> -> A; type_c<B> -> B; ...}` map from the tuple and use that instead.  
  
Is not having a `hana::at(hana::type_c<T>, xs)` a design choice? If not, I think it would be a beneficial feature for accessing tuples more easily and supporting more familiar `std::tuple` operations.

---

## Comment 1

> Username: ricejasonf  
> Created at: 2016-05-31 20:10:19 UTC  
> Url: https://github.com/boostorg/hana/issues/285#issuecomment-222805106  

`hana::at` is a generic function for the `hana::Iterable` concept. It isn't a function specifically for tuples.  
  
I'd suggest `hana::find_if`, but it fails at returning references. See https://github.com/boostorg/hana/issues/175.  
  
The map solution is my favourite. If you need a tuple you can always create a map to the indices.  
  
Or perhaps something like a function called `hana::get` for the `hana::Searchable` concept would provide the convenience and familiarity that is needed here. Consider the following pseudo-implementation:  
  
``` cpp  
#include <boost/hana.hpp>  
#include <utility>  
  
namespace hana = boost::hana;  
  
struct A { int value; };  
struct B { int value; };  
struct C { int value; };  
struct D { int value; };  
  
template <typename T, typename Searchable>  
auto get(Searchable&& xs)  
{  
  return *hana::find_if(std::forward<Searchable>(xs), [](auto const& x)  
  {  
    return hana::equal(hana::decltype_(x), hana::type_c<const T>);  
  });  
}  
  
int main()  
{  
  auto my_tuple = hana::make_tuple(  
    A{1},  
    B{2},  
    C{3},  
    D{4}  
  );  
  
  BOOST_HANA_RUNTIME_ASSERT(get<A>(my_tuple).value == 1);  
  BOOST_HANA_RUNTIME_ASSERT(get<B>(my_tuple).value == 2);  
  BOOST_HANA_RUNTIME_ASSERT(get<C>(my_tuple).value == 3);  
  BOOST_HANA_RUNTIME_ASSERT(get<D>(my_tuple).value == 4);  
}  
```  
  
A real implementation would obviously be a bit messier to be more performant and support returning references.

---

## Comment 2

> Username: ldionne  
> Created at: 2016-06-04 18:56:32 UTC  
> Url: https://github.com/boostorg/hana/issues/285#issuecomment-223772010  

Not supporting `at` with types is a design choice, since `at` is supposed to be used to access sequential data structures (`Iterable`s), where accessing by type does not necessarily make sense. In general, I am not convinced about accessing a tuple by type, since it fails when multiple elements have the same type. While I see how this can be convenient in some cases, I also think that overloading `at` (or `get`, for that matter) weakens the consistency of the library.  
  
The solution would really be to allow returning references from `find_if`, and then it would be pretty easy to find something by its type. Now, as a temporary solution, you might want to write a `find_by_type` function that finds the index and then uses the normal `at` function to access the element. But if you access by type often, the right thing to do is to create a map.  
  
We could also consider providing `find_by_type` as part of the `Searchable` concept, but it would not really make sense for e.g. maps (because we usually don't care about the type of the key in the map).

---

## Comment 3

> Username: ricejasonf  
> Created at: 2017-04-05 03:46:16 UTC  
> Updated at: 2017-04-05 03:47:58 UTC  
> Url: https://github.com/boostorg/hana/issues/285#issuecomment-291744350  

I know I'm digging on some old stuff, but perhaps `Sequence`'s `Searchable` implementation could take a `hana::type` of the element as a key.  
  
I don't know how bad of a breaking change that would be for some algorithms like `find_if`, but it would make `at_key` more useful at least.  
  
Just an idea :bulb:   
  
err maybe this would be a case for `index_if`
