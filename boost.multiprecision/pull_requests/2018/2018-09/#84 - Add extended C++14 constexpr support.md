# #84 Add extended C++14 constexpr support. [Closed]

> Username: jzmaddock  
> Created at: 2018-09-14 19:11:11 UTC  
> Updated at: 2021-01-30 16:09:35 UTC  
> Closed at: 2020-03-11 16:30:30 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/84  



---

## Comment 1

> Username: NAThompson  
> Created_at: 2018-10-31 19:29:15 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/84#issuecomment-434817642  

Just hit this problem; would love to see these guys merged. Anything I can do to help? Was trying to get the Daubechies filter coefficients to compile as `cpp_bin_float_100`:  
  
```cpp  
    static constexpr const std::array<Real, 4> filter_coefficients{ (1+root_three<Real>())*one_div_root_two<Real>()/4,  
                                                    (3+root_three<Real>())*one_div_root_two<Real>()/4,  
                                                    (3-root_three<Real>())*one_div_root_two<Real>()/4,  
                                                    (1-root_three<Real>())*one_div_root_two<Real>()/4};  
```

---

## Comment 2

> Username: jzmaddock  
> Created_at: 2018-11-01 09:06:36 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/84#issuecomment-434977929  

This is a tricky one - the compiler tech is so tantalisingly close but not quite there yet :(  
  
There are 2 main blockers relating to cpp_int constexpr support (cpp_bin_float should be easier once the underlying integer type is done):  
  
1) There is an important use case in computational geometry where "slightly larger than normal" integers are constructed just in case an overflow would occur.  The main bottleneck is in constructing the big-integer from intmax_t.  On little endian machines we can cast (type pun) the underlying array of limbs to a single intmax_t and just do a straight assign (64 or 128 bit move).  But this is absolutely forbidden in constexpr contexts and we have instead to decompose the intmax_t into 2 halves with bit masks and shifts :(  
2) There are a bunch of things where we use compiler intrinsics to speed up code (bit shifting and scanning stuff) which are also not constexpr :(  
  
Some of the above can be mitigated if we can detect whether we have a constexpr context or not, there is a proposal to add this to the std for C++20: http://www.open-std.org/jtc1/sc22/wg21/docs/papers/2017/p0595r0.html but no compilers seem to support it yet and the exact syntax of the operator is still up for grabs I believe.  GCC has __builtin_constant_p which almost does what we need, but although clang compiles that operator it doesn't give meaningful answers, and msvc doesn't have anything at all.  
  
Failing that, I'm hoping that compiler optimisation tech has evolved enough for this not to matter anymore, but I'm not holding my breath!  
  
I also need to digest https://stackoverflow.com/questions/13299394/is-is-constexpr-possible-in-c11 better to see if there's anything "good enough" that can be cobbled together.

---

## Comment 3

> Username: jzmaddock  
> Created_at: 2018-11-01 10:47:34 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/84#issuecomment-435003293  

Forgot to say.... if you're looking for something to do you could try making the cpp_bin_float members and eval_* non-members constexpr with liberal use of BOOST_MP_CXX14_CONSTEXPR and see how far you get?  
  
Note however, that in general, we can't depend on all floating point types being constexpr - particularly if they rely on third party libraries and/or need memory allocations.

---

## Comment 4

> Username: NAThompson  
> Created_at: 2018-11-01 18:15:26 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/84#issuecomment-435134896  

Yikes, yet again, a seemingly simple problem contorts itself into a massive undertaking. Imma stay in my lane and content myself with `const` . . .

---

## Comment 5

> Username: jzmaddock  
> Created_at: 2020-03-11 16:30:30 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/84#issuecomment-597735267  

Obsoleted.

---
