# #305 Prefer ptrdiff_t to express image dimensions instead of int [Closed]

> Username: mloskot  
> Created at: 2019-06-03 20:59:20 UTC  
> Updated at: 2019-06-06 07:42:22 UTC  
> Closed at: 2019-06-06 07:41:56 UTC  
> Url: https://github.com/boostorg/gil/pull/305  

The ptrdiff_t is de-facto standard convention used across GIL and any uses of int or size_t should be unified using ptrdiff_t.  
  
### Tasklist  
  
- [x] Ensure all CI builds pass  
- [ ] Review and approve

---

## Comment 1

> Username: stefanseefeld  
> Created_at: 2019-06-03 21:05:36 UTC  
> Url: https://github.com/boostorg/gil/pull/305#issuecomment-498426489  

I have to admit I'm not very enthusiastic about the idea of using `std::ptrdiff_t` in GIL. At least conceptually we are dealing with indices into images, not pointers into memory. The latter may be true depending on the specific representation (i.e., the channel type).

---

## Comment 2

> Username: mloskot  
> Created_at: 2019-06-03 21:18:09 UTC  
> Updated_at: 2019-06-05 07:25:23 UTC  
> Url: https://github.com/boostorg/gil/pull/305#issuecomment-498430288  

> I have to admit I'm not very enthusiastic about the idea of using std::ptrdiff_t in GIL.   
  
This PR just cultivates existing pattern established by original pattern.  
If the pattern needs to be changed, it should be done in separate PR.  
  
> At least conceptually we are dealing with indices into images, not pointers into memory.   
  
I personally don't consider `ptrdiff_t` as memory-specific, but I agree with this reationale from https://en.cppreference.com/w/cpp/types/ptrdiff_t  
  
```  
std::ptrdiff_t is used for pointer arithmetic and array indexing, if negative values are possible.  
```  
  
Iterating backward, counting and counting towards `-1` is a supported case in GIL, by design, AFAIU.

---

## Comment 3

> Username: stefanseefeld  
> Created_at: 2019-06-06 01:54:13 UTC  
> Url: https://github.com/boostorg/gil/pull/305#issuecomment-499318731  

Yes, exactly: "pointer arithmetic and array indexing" to me clearly evokes a vocabulary gravitating around the concept of memory.  
  
In GIL we are dealing with images and indices, so we should be looking for "index arithmetic" rather than "pointer arithmetic". While the underlying (C++) types could indeed be the same, I'd still prefer to keep the distinction clear.  
To illustrate a little more where I'm coming from, the VSIP standard (https://www.omg.org/spec/VSIPL++/1.3/PDF, which I had been worked on for many years, and which defines a C++ API for image and signal processing), defines these concepts:  
```  
typedef implementation-defined index_type;  
typedef signed-version-of-index_type index_difference_type;  
typedef signed-version-of-index_type stride_type;  
```  
(There is a whole section in the standard talking about "domains" and "domain-arithmetic" that deals with these concepts, in particular as in VSIPL++ some of that can be used at compile-time, notably in the context of expression templates.)   
So, while I'm not promoting the names "index_type" and "index_difference_type", I admit that I do have an aversion to the notion of "pointer" in this context.  
  
I do admit that `ptrdiff_t` is already used in `Boost.GIL`, and I'm not suggesting to change that (in this PR). All I'm saying is that we shouldn't make things any worse... :-)

---

## Comment 4

> Username: mloskot  
> Created_at: 2019-06-06 07:41:56 UTC  
> Updated_at: 2019-06-06 07:42:22 UTC  
> Url: https://github.com/boostorg/gil/pull/305#issuecomment-499385636  

An image in memory is modeled with array or matrix and accessing elements of an image does belong to notion of array or matrix indexing. I don't think anyone who works with images would disagree.  
  
I understand a standard like VSIPL++ may need to discuss and define notion of indexing in more elaborate fashion, but for basic needs like in GIL, it's just _not necessary_.  
  
I _insist we should avoid proliferation of custom typedefs for basic types_, especially in public interfaces of GIL, and strictly rely on C++ built-in fundamental types.  
The `ptrdiff_t` (as is `size_t`) should be familiar to any C++ user, and GIL (any library) should respect that and offer interfaces with qualities of being self-descriptive, discoverable, transparent and idiomatic where transitive knwoledge applies ("Quality Matters" case studies by Matthew Wilson for ACCU). i.e. `void foo(my_custom_index_type n)`; does not offer those qualities.  
  
It's just unfortunate there is this `ptr` in `ptrdiff_t`, what seems to be an itching aspect of this type, as this type offers all GIL requires and its users need.  
  
  
> All I'm saying is that we shouldn't make things any worse... :-)  
  
- `int`  is a bad type because it is not a type expected by *current* interface.  
- `ptrdiff_t` is a good type, in the context in hand, as it is expected by the *current* interface, but you consider it bad due to the naming ambiguity.  
  
Even if you argue this PR replaces a bad type with a bad type, *currently*, the `int` is 'worse bad type'. At least, until future GIL arrives.  
  
This PR does not make the situation worse.  
In fact, on contrary, it would make a task of searching and replacing even easier.

---
