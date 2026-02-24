# #424 - Feature Request: SFINAE-friendliness [Open]

> Username: apmccartney  
> Created at: 2018-11-28 19:28:11 UTC  
> Updated at: 2018-11-28 21:48:07 UTC  
> Url: https://github.com/boostorg/hana/issues/424  

Firstly, thank you for hana. It's simply fantastic.  
  
Currently, `unpack`, `transform`, along with the callable objects returned from `partial`, `reverse_partial` are not SFINAE-friendly. Is this behavior intentional?   
  
If not, would a PR to provide this functionality be welcome?

---

## Comment 1

> Username: pfultz2  
> Created at: 2018-11-28 19:58:06 UTC  
> Url: https://github.com/boostorg/hana/issues/424#issuecomment-442583833  

I am not sure how possible it is to do everywhere in hana since its common to use `static_assert` to report errors.  
  
[Boost.HOF](https://github.com/boostorg/hof) does provide SFINAE-friendly versions for some of those already(such as [`unpack`](https://boost-hof.readthedocs.io/en/latest/include/boost/hof/unpack.html) and [`partial`](https://boost-hof.readthedocs.io/en/latest/include/boost/hof/partial.html)).

---

## Comment 2

> Username: ricejasonf  
> Created at: 2018-11-28 20:29:48 UTC  
> Url: https://github.com/boostorg/hana/issues/424#issuecomment-442593603  

@pfultz2   
`hof::unpack` does not appear to support `hana::Foldable` AFAICT.  
  
If Boost.Hof doesn't have it already, perhaps a function to create a SFINAE friendly wrapper would be useful. It could take predicates to apply to each argument.  
  
```cpp  
constexpr auto unpack = make_friendly(hana::unpack,  
                                      requires_<Foldable>,  
                                      always(hana::true_));  
```  
... or something like that.

---

## Comment 3

> Username: apmccartney  
> Created at: 2018-11-28 21:37:40 UTC  
> Url: https://github.com/boostorg/hana/issues/424#issuecomment-442613921  

I was unaware of Boost.HoF. It looks like an impressive piece of work!  
  
At the moment, the code base I have in mind is pretty heavily invested in hana. Given HoF seems to be oriented towards the standard library, it appears there would be a bit of lifting required to implement hana interoperabilty. At this point, it's not clear to me that the necessary level of effort would be signicantly less than reimplementing the hana higher order functions in question.  
  
That said, I very much intend to give the library a spin on my next project.   
  
> I am not sure how possible it is to do everywhere in hana since its common to use `static_assert` to report errors.  
  
It very well may not be applicable in general, but it's a nice feature to have where it could be reasonably implemented. For instance, in the case of `partial` and `reverse_partial`, SFINAE could be implemented trivially using a trailing return type. Borrowing from the [Niebler/Carter ranges library](https://github.com/ericniebler/range-v3/blob/87a6c31cdd9d13f0673da4f650b44c2171cb2744/include/range/v3/detail/config.hpp#L91)  
  
```cpp  
#define HANA_DECLTYPE_AUTO_RETURN(...)   \  
    -> decltype(__VA_ARGS__)             \  
{ return (__VA_ARGS__); }   
```  
  
The [`partial`](https://github.com/boostorg/hana/blob/490dbf656d36dc183f492ab973fd2df19c83841d/include/boost/hana/functional/partial.hpp#L77) closure's call operator could be rewritten as   
  
```cpp  
template <typename ...Y>  
constexpr auto operator()(Y&& ...y) const&  
HANA_DECLTYPE_AUTO_RETURN(  
  hana::at_c<0>(storage_)(  
    hana::at_c<n+1>(storage_)...,  
    static_cast<Y&&>(y)...  
  )  
)  
```

---

## Comment 4

> Username: pfultz2  
> Created at: 2018-11-28 21:48:07 UTC  
> Url: https://github.com/boostorg/hana/issues/424#issuecomment-442617343  

> hof::unpack does not appear to support hana::Foldable AFAICT.  
  
To support `Foldable`, hana would need to specialize [`unpack_sequence`](https://boost-hof.readthedocs.io/en/latest/include/boost/hof/unpack_sequence.html) so it can be unpacked by `boost::hof::unpack`.
