# #205 - boost flat_map 1.78.0 does not compile with ranges-v3 0.11.0 [Closed]

> Username: AlexandreBossard  
> Created at: 2021-12-20 17:04:41 UTC  
> Updated at: 2022-01-07 21:14:22 UTC  
> Closed at: 2022-01-07 21:14:22 UTC  
> Url: https://github.com/boostorg/container/issues/205  

I have just upgrade to boost 1.78.0 and I have compilation errors with ranges. It seems that boost flat_map defines `std::iterator_contiguous_tag` as an `iterator_concept`. This tag does not exist in C++17 and is forward declared by boost, which is pick-up by range and failed for I suppose lack or proper definition.  
  
[the godbolt](https://godbolt.org/#z:OYLghAFBqd5QCxAYwPYBMCmBRdBLAF1QCcAaPECAMzwBtMA7AQwFtMQByARg9KtQYEAysib0QXACx8BBAKoBnTAAUAHpwAMvAFYTStJg1DIApACYAQuYukl9ZATwDKjdAGFUtAK4sGIAMxmpK4AMngMmAByPgBGmMQgABykAA6oCoRODB7evgFBaRmOAmER0SxxCcl2mA5ZQgRMxAQ5Pn6Btpj2xQwNTQSlUbHxSbaNza15HQrjA%2BFDFSOJAJS2qF7EyOwc5v7hyN5YANQm/m7EhsCYAPQAbv53eJgA7teNANaYAHQIKSmn2BMGgAgkDQWY9gwDl5jqc3DFUOkCNc0IImPNiNcqAYCAB9FhMFI/P4AsFg8IEI4E8IQZZkgDsVhBRyOCKRIBQsnREQSIGxTDxBP%2BZwppCOFIBRxSTCopyZwJZ0qoJ3pbiOFyMmAUHNuT2e2pAH0wEC4dP88pM9IAIhxVrROABWXh%2BDhaUioThuazWI4KdabTAnCE8UgETS21bvCSJL70sxSDRcekaB3xgBsGgAnA79JxJM7w%2B7OLxtRpQ%2BHVnBYEg0CwUnR4uRKLX6/QEsAuBp/Hw6AR4tqIDFCzFwk0AJ6cEO1tiCADyDFoE9dvCwBKM4mXpHwxFqjluWsLmFUtS8fcnvApXULtDwMQuxDHHiwhYIxDwLHPq35wAUADU9bOKSMOeMiCCIYjsFIoHyEoaiFro0gGEYKDepY%2Bi3tqsDMGwIBsjMpD7gk3D0oktqkBA2HsBqVwKPcBEjK6XCdhG7opD02ocAAtLO/hHJxMzoKcVqmJY1hJiWXS7lkLgMO4nhtCAkg5qE8zlJUASpOkmQCJMfhKZpRRZIMakjN2NR1AIfQTPJeT6eZPRWXMZTDAkZmzLpik5jM/TGS5ASrH6GxbHor6YNsPB2o6Babh6HCqIkaacWmkhHMAyDIEcnZfLxEBeqJaFHLghAkEG/hcGKHh1g2xClZIyy8GGy7LJGEhmLGZWSIkmb0g6iRJok/j0vSuYcPmpAum6sUliAZaNVolYwIgIDrAQKSnk2EAttVinBPgRAJOV/BgaI4hQUdMEqOom6heFvDPBcKSfiNTrjYWsWzqea2UqgyrxYlyWpelmUaNlRy5agVVtrV9Xlk1LVcDGDqSPSnaJA6/hphmDpIyNY0TbwU22DNsPzeRi0QDWEOto2FCbVT20HJcnaJj2tB9sQA5DpuI7MA%2BIHTowBDzouharpcG5utuUn7hxbpHieZ7cBeghXpuN53uOT7bG6r7vk935/gBQEuiG53gad0jnYol3wXoSHGKhNjq5hUCUbhiL4YRk4kWRFGsFRlxanRXuMcxzWsexnDcbx/EEIJ/jCY74mdN00kQK4Hn6SpzmLK5BnadkNl6TmhQF75ucafZ9TuUXnkp1JlmzOX6luf0mdeU3ql%2Bf4AX%2BsF5U3U99ocC9%2BNFnFCVJSljNGMDXwg1wYN5VYBVFftpXlUclXUzVuymg1Fbw2mXyY6fZ9n8Nw9429xZE7NFZk/AFPLZ96201tbZ6Jge0kHoZsnZBS2shrZwWujuW6pB7qEiHlFV6MVOAfVWqeI4P0jh/SnkcGewA54LyXvTKGe8YZzRYlGLgx9z4UMxpfPM0VJq31LCTX25NKaQxps2fBIwsFcDMCzGgbN%2ByUC5m6Hm45%2BYQxnELBcS5JaYDXMACWK48A7jqDLQ8x5kCnggZeYebp1b3kfBgbWDU3wfiVl%2BAwP5/wvEAsBJW0FzaAOgiAq6bpdBBHtihfKTsMJPzdnhAg9EiL9V9m7aiQduwh0TGHVYqA2JZA4tHPiAkhIiRXhYZOVdnDp1kh5cq2cFjqXKqXHouT849GbiMcqmTeg11yH4KpkkLI1J8l3CuVTakKXaS0nOhTe5BUgqGcBMCR60IJpwdBAMsGZTarg3KjsxRrxKnvCqHDd4QjMEQw%2BpBSHkMoRQxCNC4F0I4NNe%2BcMRpmFGePA%2B5zCIZGcJIIAA%3D)

---

## Comment 1

> Username: AlexandreBossard  
> Created at: 2022-01-03 10:49:40 UTC  
> Url: https://github.com/boostorg/container/issues/205#issuecomment-1004009137  

seams to be related to #195

---

## Comment 2

> Username: igaztanaga  
> Created at: 2022-01-07 21:13:43 UTC  
> Updated at: 2022-01-07 21:14:20 UTC  
> Url: https://github.com/boostorg/container/issues/205#issuecomment-1007745022  

Duplicate of #195.  
  
I've just checked that with the mentioned commits on Boost.Move and Boost.Container makes your example well-defined with GCC 11 and Clang 13 in C++17 and C++20 modes.
