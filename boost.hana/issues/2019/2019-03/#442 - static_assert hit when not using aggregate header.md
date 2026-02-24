# #442 - static_assert hit when not using aggregate header [Closed]

> Username: NAThompson  
> Created at: 2019-03-05 04:34:49 UTC  
> Updated at: 2019-07-25 15:03:43 UTC  
> Closed at: 2019-07-25 15:03:43 UTC  
> Url: https://github.com/boostorg/hana/issues/442  

If I compile the following code:  
  
```cpp  
#include <utility>  
#include <boost/hana/for_each.hpp>  
  
template<int n>  
void foo()  
{  
    // do nothing  
}  
  
int main()  
{  
       boost::hana::for_each(std::make_index_sequence<13>(), [&](auto idx){  
            foo<idx>();  
       });  
}  
```  
  
with the following commands:  
  
```  
$ g++-8 -I../hana/include -Wfatal-errors --std=gnu++17 reproduce.cpp  
```  
  
I get the compile error:  
  
```  
In file included from reproduce.cpp:2:  
../hana/include/boost/hana/for_each.hpp: In instantiation of 'constexpr void boost::hana::for_each_t::operator()(Xs&&, F&&) const [with Xs = std::integer_sequence<long unsigned int, 0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12>; F = main()::<lambda(auto:1)>]':  
reproduce.cpp:14:9:   required from here  
../hana/include/boost/hana/for_each.hpp:31:23: error: static assertion failed: hana::for_each(xs, f) requires 'xs' to be Foldable  
         static_assert(hana::Foldable<S>::value,  
                       ^~~~  
compilation terminated due to -Wfatal-errors.  
```  
  
If I include all of `hana.hpp`, then the error goes away:   
  
```cpp  
#include <utility>  
#include <boost/hana.hpp>  
  
template<int n>  
void foo()  
{  
    // do nothing  
}  
  
int main()  
{  
       boost::hana::for_each(std::make_index_sequence<13>(), [&](auto idx){  
            foo<idx>();  
       });  
}  
```

---

## Comment 1

> Username: ricejasonf  
> Created at: 2019-03-05 06:15:39 UTC  
> Url: https://github.com/boostorg/hana/issues/442#issuecomment-469553493  

You need `#include <boost/hana/ext/std/integer_sequence.hpp>` to get the definitions for `std::index_sequence`. The `ext` folders are not supposed to be a part of `hana.hpp`.  
  
It just happens that `detail/hash_table.hpp` uses it.

---

## Comment 2

> Username: NAThompson  
> Created at: 2019-03-05 18:17:52 UTC  
> Url: https://github.com/boostorg/hana/issues/442#issuecomment-469792712  

Ok, so the contention is that this is not a bug? It seemed reading through the documentation that my failing test was exactly following the documentation . . .

---

## Comment 3

> Username: ricejasonf  
> Created at: 2019-03-05 18:50:35 UTC  
> Url: https://github.com/boostorg/hana/issues/442#issuecomment-469811608  

It's probably a documentation issue then. Is there a specific example you are referring to?  
  
Note that the input to `for_each` must be [`hana::Foldable`](http://boostorg.github.io/hana/group__group-Foldable.html) whose "minimal complete definition" for `std::index_sequence` resides in that `ext` include file which must be included explicitly.

---

## Comment 4

> Username: ldionne  
> Created at: 2019-04-01 17:36:18 UTC  
> Url: https://github.com/boostorg/hana/issues/442#issuecomment-478673465  

@NAThompson Can you please point out where your example is taken from?  
  
The documentation talks about bridge headers for external adapters:  
http://boostorg.github.io/hana/index.html#tutorial-ext

---

## Comment 5

> Username: ricejasonf  
> Created at: 2019-04-01 17:49:17 UTC  
> Url: https://github.com/boostorg/hana/issues/442#issuecomment-478677981  

Maybe add `std::integral_constant` and `std::index_sequence` to `hana.hpp`. The only added cost would be the customization declarations as `<type_traits>` and `<utility>` are bound to be included anyways.

---

## Comment 6

> Username: ldionne  
> Created at: 2019-04-01 18:06:05 UTC  
> Url: https://github.com/boostorg/hana/issues/442#issuecomment-478683939  

That would be an option, but right now external adapters are consistently never being defined in `<hana.hpp>`. We could do an exception for the standard library, but IDK.

---

## Comment 7

> Username: NAThompson  
> Created at: 2019-04-01 18:12:19 UTC  
> Url: https://github.com/boostorg/hana/issues/442#issuecomment-478686087  

@ldionne : My bad, I just spent some time looking for the example I thought I recalled following, and couldn't find it.
