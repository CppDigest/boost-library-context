# #67 - Provide a way of taking a specialization into a type list [Open]

> Username: ldionne  
> Created at: 2015-05-04 17:07:43 UTC  
> Updated at: 2015-11-12 20:51:24 UTC  
> Url: https://github.com/boostorg/hana/issues/67  

Specifically, it should be possible to have something similar to  
  
``` cpp  
template <typename ...>  
struct T;  
  
to_type_list(type<T<int, char, float>>) == tuple_t<int, char, float>  
```  
  
The exact semantics and name of this utility are still to determine. Here's a dump of something I had in my workspace:  
  
``` cpp  
#include <boost/hana.hpp>  
#include <type_traits>  
  
template <typename Specialization>  
constexpr auto untemplate = sizeof(Specialization) == 1 ? 1 : throw "hana::untemplate must be used with a specialization";  
  
template <template <typename ...> class Template, typename ...T>  
constexpr auto untemplate<Template<T...>> = hana::tuple_t<T...>;  
  
  
template <typename ...>  
struct F;  
  
  
int main() {  
    untemplate<F<int, char, float>>;  
}  
```

---

## Comment 1

> Username: ldionne  
> Created at: 2015-07-03 05:01:11 UTC  
> Url: https://github.com/boostorg/hana/issues/67#issuecomment-118233752  

I think adding a separate function for that is overkill, since that's probably too specific. If this can be made to work with existing machinery, then OK, but otherwise this should be dropped in favour of keeping the library more simple.

---

## Comment 2

> Username: samkellett  
> Created at: 2015-11-12 20:11:45 UTC  
> Url: https://github.com/boostorg/hana/issues/67#issuecomment-156220994  

can this be a generic fall-through for the hana::to function? like if not specialised (ala mpl::vector/mpl::list) then just pull out all the types.  
  
so:  
  
```  
static_assert(hana::to<hana::tuple_tag>(meta::list<int, bool, long>{}) == hana::tuple_t<int, bool, long>);  
static_assert(hana::to<hana::tuple_tag>(eggs::variant<int, bool, long>{1}) == hana::tuple_t<int, bool, long>);  
// and even  
static_assert(hana::to<hana::tuple_tag>(std::vector<int>{}) == hana::tuple_t<int, std::allocator<int>>);  
```  
  
should all 'just work'

---

## Comment 3

> Username: ldionne  
> Created at: 2015-11-12 20:51:24 UTC  
> Url: https://github.com/boostorg/hana/issues/67#issuecomment-156230325  

From a theoretical perspective, special casing `to` in this way is very unclean. In theory, `hana::to` is a structure-preserving conversion function, and special-casing it in this way breaks the conceptual integrity of the library.  
  
Also, from a practical standpoint, I think that something like `hana::to<hana::tuple_tag>(std::vector<int>{})` is very unintuitive, since a lot of people would expect it to return a tuple with the content of the vector. Of course, this can't be implemented (because the number of elements in the vector is only known at runtime), but newbies would probably fall into the trap. I think this, perhaps more than the theoretical argument, is a good hint that this would be a bad design choice.  
  
Instead of binding the core of Hana to achieve this (arguably rare) use case, I would much rather introduce a specific utility to express what a template specialization is in Hana's language, as:  
  
``` c++  
template <typename>  
struct specialization;  
  
template <template <typename ...> class Template, typename ...T>  
struct specialization<Template<T...>> {  
    template <typename F>  
    static constexpr auto operator()(F&& f) const {  
        return static_cast<F&&>(f)(hana::type<T>...);  
    }  
};  
  
using my_variant = boost::variant<int, bool, char>;  
constexpr auto my_tuple = hana::specialization<my_variant>(hana::make_tuple);  
```  
  
Another way to go would be to provide a `specialization` object which would be a `Foldable` (and probably more). This way, we could still use   
  
``` c++  
using my_variant = boost::variant<int, bool, char>;  
constexpr auto my_tuple = hana::to<hana::tuple_tag>(hana::specialization<my_variant>);  
```  
  
since any `Foldable` can be converted to a `Sequence`. However, we could now also write anything that only requires a `Foldable`, such as:  
  
``` c++  
using my_variant = boost::variant<int, bool, char>;  
auto result1 = hana::unpack(hana::specialization<my_variant>, [](auto ...types) {  
    // whatever  
});  
  
// Finds the type with the largest size in the specialization  
auto result2 = hana::maximum(hana::specialization<my_variant>, [](auto t, auto u) {  
    return hana::sizeof_(t) < hana::sizeof_(u);  
});  
  
// Finds the number types the template was specialized with  
auto n = hana::length(hana::specialization<my_variant>);  
```  
  
This way, specializations would be truly integrated with Hana, and not only supported in a special case. Thoughts?
