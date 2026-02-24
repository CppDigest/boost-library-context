# #230 - std::is_integral_v vs std::numeric_limits::is_integer [Open]

> Username: Silicomancer  
> Created at: 2020-05-01 22:51:39 UTC  
> Updated at: 2021-05-12 16:40:27 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/230  

`std::is_integral_v<myuint256>` returns `0` while `std::numeric_limits<myuint256>::is_integer` returns `1`.  
(`using myuint256 = boost::multiprecision::number<boost::multiprecision::cpp_int_backend<256, 256, boost::multiprecision::unsigned_magnitude, staticSelect<gDebug> boost::multiprecision::checked, boost::multiprecision::unchecked), void>>;`)  
  
It is usually assumed that both calls are equivalent.  
Is this desired behavior or can it be considered a bug or missing feature?

---

## Comment 1

> Username: jzmaddock  
> Created at: 2020-05-02 09:29:36 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/230#issuecomment-622925725  

This is intended and correct behaviour: `is_integral` returns information about the nature of the type and is true only for "native" integer types, it should never be true for class types.  Which is to say `is_integer` and `is_class` are mutually exclusive.  
  
`numeric_limits` on the other hand returns information on the _behaviour_ of a type - whether it models a particular concept if you will - and so _should_ be specialized for UDT's.  Note that specializing `is_integer` for UDT's would break code, since `is_integer` implies that a lot of other things are also true like trivial move/copy/init etc.

---

## Comment 2

> Username: Silicomancer  
> Created at: 2020-05-02 19:32:14 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/230#issuecomment-623002778  

Thank you for the explanation. Could you maybe give me a link where I can read more about the topic?

---

## Comment 3

> Username: pabristow  
> Created at: 2020-05-05 15:37:30 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/230#issuecomment-624129926  

Should this useful info be added to the multiprecision documentation? This is a significant difference between fundamental integer types and multiprecision types and the naming is confusing.

---

## Comment 4

> Username: ckormanyos  
> Created at: 2021-02-26 15:29:53 UTC  
> Updated at: 2021-02-26 15:30:29 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/230#issuecomment-786716854  

> Should this useful info be added to the multiprecision documentation?  
  
Yes. I believe it is worth documenting this. Admittedly, I overlooked this post for quite some time.  
  
I will be happy to update the docs accordingly (i.e., clarify behavior of `std::is_integral` vs. `std::numeric_limits<>::is_integer`).  
However, I'd prefer to get this into 1.77, as I think we need to concentrate on perfecting 1.76 at the moment.

---

## Comment 5

> Username: ckormanyos  
> Created at: 2021-05-11 18:50:02 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/230#issuecomment-838991906  

Restarting the discussion on this post.  
  
My understanding is that the types in multiprecision such as `cpp_int` and `cpp_bin_float` do not return `true` from the library's type traits such as `std::is_integral`, `std::is_floating_point`, and of course not `std::is_fundamental` (and similar). But these should return `true`for `is_class`, and `false` for `is_union` and maybe a few more worth mentioning.  
  
The open issue is to document this behavior for 1.77.  
  
Where to document?  
There is a chapter on [`numeric_limits`](https://www.boost.org/doc/libs/1_76_0/libs/multiprecision/doc/html/boost_multiprecision/tut/limits.html). Perhaps a new, similar chapter on `<type_traits>` would be good.  
  
Alternativley (my preference), we extend the chapter on `numeric_limits` to also incoude information on `<type_traits>` and reflect this in the title of this chapter as well.  
  
Thoughts?

---

## Comment 6

> Username: jzmaddock  
> Created at: 2021-05-12 16:40:27 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/230#issuecomment-839929585  

I was tempted to say that this is too trivial to be that bothered by, but if we're going for it, then I would suggest a new section in the tutorial (maybe right after the numeric_limits stuff) entitled "Traits Class Support", that mentions the std type triats, and also explains our own traits:  
  
```  
template <class T>  
struct component_type;  
template <class T>  
struct number_category;  
template <class T>  
struct is_number;  
template <class T>  
struct is_number_expression;  
```  
  
which are documented in the reference, but might not be noticed there.
