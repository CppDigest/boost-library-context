# #234 - [map][set] Cross-type comparison yields surprising behaviour [Closed]

> Username: ldionne  
> Created at: 2016-01-06 14:50:38 UTC  
> Updated at: 2016-02-16 08:01:44 UTC  
> Closed at: 2016-02-16 08:01:44 UTC  
> Url: https://github.com/boostorg/hana/issues/234  

Hana does not allow comparing types when doing so requires an implicit conversion that is _lossy_. To check this, we have a trait called `hana::is_embedded<From, To>`, which returns true if `From` can be converted without loss to `To`. Whether a conversion is lossy must be specified explicitly by users when specializing the `hana::to` function. If we're trying to compare two objects that do not have an embedding, and if no comparison function was user-specified for these types, we trigger a `static_assert`.  
  
This usually makes sense, since it disallows unsafe things like comparing `unsigned int` with `int` (these types are marked as non-embedded by Hana). However, this can lead to surprising behaviour in some cases:  
  
``` c++  
#include <boost/hana/contains.hpp>  
#include <boost/hana/integral_constant.hpp>  
#include <boost/hana/map.hpp>  
#include <boost/hana/pair.hpp>  
#include <boost/hana/set.hpp>  
  
  
int main() {  
    {  
        auto set = hana::make_set(hana::int_c<1>, hana::char_c<'x'>);  
        auto result = hana::contains(set, hana::char_c<'x'>);  
    }  
  
    {  
        auto map = hana::make_map(  
            hana::make_pair(hana::int_c<1>, 1),  
            hana::make_pair(hana::char_c<'x'>, 'x')  
        );  
        auto result = hana::contains(map, hana::char_c<'x'>);  
    }  
}  
```  
  
In both cases, the problem is that `char_`s and `int_`s can't be compared because they do not have a common embedding. Unfortunately, they are compared when evaluating both calls to `hana::contains`. Also note that with the Debug mode enabled, this will actually fail when creating the `set`/`map`, because we will compare them when doing the check for uniqueness. By the way, this is how this issue was uncovered.  
  
Now, the issue might just be that `char` and `int` should be considered as embeddings (why aren't they?). But surely, the current behaviour is surprising. It would also be nice to find a different use case in which this failure shows up, but where the two types involved should definitely not be embedded (if possible). If such a use case exists, then it would give us a hint that the fix is not simply to make `char` and `int` embeddable.

---

## Comment 1

> Username: ricejasonf  
> Created at: 2016-01-19 23:33:07 UTC  
> Updated at: 2016-01-19 23:33:46 UTC  
> Url: https://github.com/boostorg/hana/issues/234#issuecomment-173022956  

I've encountered this problem before and not just with maps.  
  
``` cpp  
#include<boost/hana.hpp>  
  
namespace hana = boost::hana;  
  
  
int main() {  
    constexpr int x = 5;  
    constexpr std::size_t y = 5;  
    static_assert(x == y, "This works.");  
    //this explodes  
    BOOST_HANA_CONSTANT_ASSERT(hana::int_c<5> == hana::size_c<5>);  
}  
  
```

---

## Comment 2

> Username: ldionne  
> Created at: 2016-01-20 16:54:24 UTC  
> Url: https://github.com/boostorg/hana/issues/234#issuecomment-173270558  

The above behaviour is actually intended. The basic idea is that if an operation can be unsafe, Hana will not allow for it. For example, what should happen if you wrote   
  
``` c++  
hana::int_c<-3> == hana::size_c<std::numeric_limits<int>::max() + 1>  
```  
  
Since `-3` can't be represented in a `std::size_t`, and since `std::numeric_limits<int>::max() + 1` can't be represented in `int`, there's no way to make this comparison meaningful. And indeed, if you do the comparison, the compiler will warn you about comparing integers with different signs. Hana takes this one step further and produces an error.  
  
The reason for this is that I wanted to axiomatize Hana as much as possible when I initially wrote it (I'm a mathematician by formation :-). I wanted operations to have well-defined semantics, and to be able to reason about programs written with Hana with as much rigor as possible. This is what lead to the introduction of concepts with semantic laws, bits of category theory and the more "abstract" part of Hana.  
  
That being said, one could argue that ensuring the correctness of basic stuff like comparison is beyond the scope of Hana (since it's advertised as a library of heterogeneous containers), and it would be a valid point. Perhaps it would be a better design to stick more closely to the C++ language (and its flaws), instead of adopting a surprising behaviour for the sake of mathematical correctness. I'm not sure. However, what I'm sure of is that this would represent a significant change to the library, and I don't think it would be wise to carry such a change right now. This is something I would definitely consider for a 2.0 rewrite, but right now I'm still just trying to hit 1.0.

---

## Comment 3

> Username: ldionne  
> Created at: 2016-02-15 19:50:36 UTC  
> Url: https://github.com/boostorg/hana/issues/234#issuecomment-184362000  

It turns out that this issue was fixed in 7f7c7a19a9562b434f9b216dcd10738d8a0cdff7, when I added embeddings from `char` to any integral type with the proper signedness. Furthermore, since we're about to move to hash-based containers, and since `hana::hash` will ensure that two keys that fall into the same bucket can be compared, this should be a non-issue in the future.
