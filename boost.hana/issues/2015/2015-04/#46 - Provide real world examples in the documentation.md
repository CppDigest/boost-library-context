# #46 - Provide real world examples in the documentation [Closed]

> Username: ldionne  
> Created at: 2015-04-17 16:21:49 UTC  
> Updated at: 2015-06-02 01:14:38 UTC  
> Closed at: 2015-06-02 01:14:38 UTC  
> Url: https://github.com/boostorg/hana/issues/46  

The current documentation uses petty problems to showcase the library. Instead, we could present some more advanced examples.

---

## Comment 1

> Username: MarisaKirisame  
> Created at: 2015-04-17 16:34:48 UTC  
> Url: https://github.com/boostorg/hana/issues/46#issuecomment-94022696  

I am currently transforming one of my project into hana-powered. I am more than happy if you want it (but I have not finished migration yet)

---

## Comment 2

> Username: ldionne  
> Created at: 2015-04-17 16:36:53 UTC  
> Url: https://github.com/boostorg/hana/issues/46#issuecomment-94023113  

I am definitely interested. Do you have a link to the project?

---

## Comment 3

> Username: MarisaKirisame  
> Created at: 2015-04-17 17:51:07 UTC  
> Url: https://github.com/boostorg/hana/issues/46#issuecomment-94038385  

github.com/lolisa/first_order_logic_prover, still in migration :)

---

## Comment 4

> Username: ldionne  
> Created at: 2015-04-26 15:52:06 UTC  
> Url: https://github.com/boostorg/hana/issues/46#issuecomment-96402217  

I looked at your project. It seems interesting but it's pretty complex too. I am not sure if the project as a whole would be well suited as a real-world example, because it requires too much domain-specific knowledge. However, there are certainly parts of it that would lend themselves to a case study. I see that it's been a while since you committed to that repo; did you pursue the conversion to Hana?

---

## Comment 5

> Username: MarisaKirisame  
> Created at: 2015-04-26 16:05:51 UTC  
> Url: https://github.com/boostorg/hana/issues/46#issuecomment-96403351  

I am still trying to figure out how to convert an hana::tuple into boost::mpl::vector like data structure... I am just taking a little break by learning a bit about formal semantic, I will reply when I get it converted completely

---

## Comment 6

> Username: ldionne  
> Created at: 2015-04-26 16:26:04 UTC  
> Url: https://github.com/boostorg/hana/issues/46#issuecomment-96404740  

Given any `Foldable` `xs` which contains `Type`s only, you can convert `xs` to a MPL vector as follows:  
  
``` cpp  
auto mpl_vector = hana::unpack(xs, [](auto ...types) {  
    return mpl::vector<typename decltype(types)::type...>{};  
});  
```  
  
Now, you probably want the `mpl::vector<....>` type itself, not an object of that type. You can just use `decltype` for that. Here's a minimal working example:  
  
``` cpp  
#include <boost/hana/tuple.hpp>  
#include <boost/hana/type.hpp>  
  
#include <boost/hana/ext/boost/mpl/vector.hpp>  
#include <boost/mpl/vector.hpp>  
  
#include <type_traits>  
namespace hana = boost::hana;  
namespace mpl = boost::mpl;  
  
  
int main() {  
    auto xs = hana::tuple_t<int, char, float>;  
  
    auto mpl_vector = hana::unpack(xs, [](auto ...types) {  
        return mpl::vector<typename decltype(types)::type...>{};  
    });  
  
    using mpl_vector_type = decltype(mpl_vector);  
  
    static_assert(std::is_same<  
        mpl_vector_type,  
        mpl::vector<int, char, float>  
    >{}, "");  
}  
```  
  
If you want to go from a `Tuple` to a MPL vector of the types of the objects in that `Tuple`, simply map `decltype_` on the tuple:  
  
``` cpp  
auto xs = hana::make_tuple(1, 'c', 2.3f);  
auto ts = hana::transform(xs, hana::decltype_);  
  
auto mpl_vector = hana::unpack(ts, [](auto ...types) {  
    return mpl::vector<typename decltype(types)::type...>{};  
});  
```  
  
I'll consider adding a conversion from any `Foldable` containing `Type`s to a MPL vector. By the way, may I ask why you need to convert a Hana `Tuple` back to a MPL vector?

---

## Comment 7

> Username: ldionne  
> Created at: 2015-04-26 16:29:54 UTC  
> Url: https://github.com/boostorg/hana/issues/46#issuecomment-96405080  

Actually, there's a better way of doing what I wrote above. Here you go:  
  
``` cpp  
#include <boost/hana/tuple.hpp>  
#include <boost/hana/type.hpp>  
  
#include <boost/hana/ext/boost/mpl/vector.hpp>  
#include <boost/mpl/vector.hpp>  
  
#include <type_traits>  
namespace hana = boost::hana;  
namespace mpl = boost::mpl;  
  
  
int main() {  
    auto xs = hana::make_tuple(1, 'c', 2.3f);  
    auto ts = hana::transform(xs, hana::decltype_);  
  
    using mpl_vector_type = decltype(  
        hana::unpack(ts, hana::template_<mpl::vector>)  
    )::type;  
  
    static_assert(std::is_same<  
        mpl_vector_type,  
        mpl::vector<int, char, float>  
    >{}, "");  
}  
```

---

## Comment 8

> Username: ldionne  
> Created at: 2015-04-26 18:23:26 UTC  
> Url: https://github.com/boostorg/hana/issues/46#issuecomment-96418052  

Conversion from `Foldable` to `mpl::vector` is implemented by 9a412f9c391bff0d377cdf38f1ae06aa1444038a.

---

## Comment 9

> Username: MarisaKirisame  
> Created at: 2015-04-26 22:02:43 UTC  
> Url: https://github.com/boostorg/hana/issues/46#issuecomment-96439404  

Thx.

---

## Comment 10

> Username: grisumbras  
> Created at: 2015-05-07 05:55:42 UTC  
> Url: https://github.com/boostorg/hana/issues/46#issuecomment-99730188  

Probably, you can just borrow ideas for examples from existing FP resources (Haskell books, etc.).

---

## Comment 11

> Username: ldionne  
> Created at: 2015-05-07 15:57:39 UTC  
> Url: https://github.com/boostorg/hana/issues/46#issuecomment-99921401  

One problem is that Hana is a metaprogramming library, so typical usage examples are not the same as in normal programming. That being said, I've got some O.K. examples and I'll update the documentation when I get some time.
