# #763 - Types now in namespace `std` break Gil [Closed]

> Username: ckormanyos  
> Created at: 2025-04-18 17:22:20 UTC  
> Updated at: 2025-04-18 20:17:41 UTC  
> Closed at: 2025-04-18 20:17:40 UTC  
> Url: https://github.com/boostorg/gil/issues/763  

### Actual behavior  
  
I have found that the subtle change in [this commit](https://github.com/boostorg/gil/commit/dc07bcadcd7183172c0cbaa98d59c501b050f479) now requires decorating `gil::float32_t` and `gil::float64_t` with the boost namespace.  
  
A bunch of my project's [nightly and/or push builds](https://github.com/ckormanyos/mandelbrot/actions/runs/14535472802) clone and use small parts of modular Boost. And I find that this change broke my Mandelbrot.  
  
It's broken on pretty much every single compiler on GHA at the moment. This is definitely needing correction.  
  
So we need something like this:  
  
```cpp  
template<> struct is_floating_point<::boost::gil::float32_t> : std::true_type {};  
template<> struct is_floating_point<::boost::gil::float64_t> : std::true_type {};  
```  
  
### Environment  
  
Everywhere when you do _not_ _use_ `namespace` `boost`, so in other words it is broken everywhere now, and this seems rather meaningful.

---

## Comment 1

> Username: ckormanyos  
> Created at: 2025-04-18 17:31:41 UTC  
> Url: https://github.com/boostorg/gil/issues/763#issuecomment-2815897389  

In other words, there is no such namespace as `std::gil`.

---

## Comment 2

> Username: ckormanyos  
> Created at: 2025-04-18 17:44:50 UTC  
> Url: https://github.com/boostorg/gil/issues/763#issuecomment-2815917876  

See also #764

---

## Comment 3

> Username: ckormanyos  
> Created at: 2025-04-18 20:17:40 UTC  
> Url: https://github.com/boostorg/gil/issues/763#issuecomment-2816129298  

Fixed by #764
