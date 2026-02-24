# #925 Fix for Scipy Issue 16079 [Closed]

> Username: mborland  
> Created at: 2023-01-23 18:00:30 UTC  
> Updated at: 2023-01-29 12:20:55 UTC  
> Closed at: 2023-01-29 12:20:54 UTC  
> Url: https://github.com/boostorg/math/pull/925  

Sharing between platforms

---

## Comment 1

> Username: jzmaddock  
> Created_at: 2023-01-23 19:09:39 UTC  
> Url: https://github.com/boostorg/math/pull/925#issuecomment-1400841286  

Note that this would be a breaking change, since we promised a constexpr prime :(

---

## Comment 2

> Username: mborland  
> Created_at: 2023-01-23 19:13:23 UTC  
> Url: https://github.com/boostorg/math/pull/925#issuecomment-1400846693  

> Note that this would be a breaking change, since we promised a constexpr prime :(  
  
It is. I am not sure I am ready to revisit #400, but I think the sieve of eratosthenes could be made constexpr in C++14.

---

## Comment 3

> Username: mborland  
> Created_at: 2023-01-24 00:46:14 UTC  
> Url: https://github.com/boostorg/math/pull/925#issuecomment-1401215338  

With the inclusion of the structs I get:  
  
```  
./hypergeom1  0.53s user 0.01s system 77% cpu 0.691 total  
```  
  
Using C++14 but we also pass the constexpr include test

---

## Comment 4

> Username: jzmaddock  
> Created_at: 2023-01-24 13:10:56 UTC  
> Url: https://github.com/boostorg/math/pull/925#issuecomment-1401926227  

Nice bit of lateral thinking there, good fix!  
  
Looks like the dummy classes need to be artificially made templates to avoid ODR violations, then this should be good to go.  Also if you do a quick search for uses of `BOOST_MATH_HAVE_CONSTEXPR_TABLES`, you'll find a couple of other table-driven functions with the same issue.

---

## Comment 5

> Username: mborland  
> Created_at: 2023-01-24 19:11:00 UTC  
> Updated_at: 2023-01-24 21:23:14 UTC  
> Url: https://github.com/boostorg/math/pull/925#issuecomment-1402456534  

@jzmaddock Am I missing something moving to templates? I specialized using an `int` and added a definition as prescribed by https://en.cppreference.com/w/cpp/language/static#Constant_static_members but I still oscillate between missing symbol and[ duplicate symbol](https://github.com/boostorg/math/actions/runs/3999260482/jobs/6862940073#step:17:858)  
  
Edit: Looks like MSVC 14.0 is not standards conformant since `BOOST_MATH_CONSTEXPR_TABLE_FUNCTION` had a workaround for it.

---

## Comment 6

> Username: mborland  
> Created_at: 2023-01-24 21:25:34 UTC  
> Url: https://github.com/boostorg/math/pull/925#issuecomment-1402687808  

In this most recent run I disabled `[ run test_instantiate1.cpp test_instantiate2.cpp  ]` it looks like all of the failures are stemming from a collision between the two object files.

---

## Comment 7

> Username: jzmaddock  
> Created_at: 2023-01-25 11:52:29 UTC  
> Url: https://github.com/boostorg/math/pull/925#issuecomment-1403491698  

@mborland : This a very old C+03 trick for dumping static data into headers, but you need to be using something like:  
  
```  
template <bool>  
struct prime_data_imp  
{  
         static constexpr std::array<unsigned char, 54> a1 {{  
         2u, 3u, 5u, 7u, 11u, 13u, 17u, 19u, 23u, 29u, 31u,   
         37u, 41u, 43u, 47u, 53u, 59u, 61u, 67u, 71u, 73u,   
         79u, 83u, 89u, 97u, 101u, 103u, 107u, 109u, 113u,   
         127u, 131u, 137u, 139u, 149u, 151u, 157u, 163u,   
         167u, 173u, 179u, 181u, 191u, 193u, 197u, 199u,   
         211u, 223u, 227u, 229u, 233u, 239u, 241u, 251u  
      }};  
static constexpr std::array<std::uint16_t, 6488> a2 {{  
         257u, 263u, 269u, 271u, 277u, 281u, 283u, 293u,   
         307u, 311u, 313u, 317u, 331u, 337u, 347u, 349u, 353u,   
         359u, 367u, 373u, 379u, 383u, 389u, 397u, 401u, 409u, // etc }};  
// etc  
};  
using prime_data = prime_data_imp<true>;  
```  
  
Then reference the data as `prime_data::a1` etc in the code.

---

## Comment 8

> Username: mborland  
> Created_at: 2023-01-25 16:30:54 UTC  
> Url: https://github.com/boostorg/math/pull/925#issuecomment-1403890741  

> @mborland : This a very old C+03 trick for dumping static data into headers, but you need to be using something like:  
>   
> ```  
> template <bool>  
> struct prime_data_imp  
> {  
>          static constexpr std::array<unsigned char, 54> a1 {{  
>          2u, 3u, 5u, 7u, 11u, 13u, 17u, 19u, 23u, 29u, 31u,   
>          37u, 41u, 43u, 47u, 53u, 59u, 61u, 67u, 71u, 73u,   
>          79u, 83u, 89u, 97u, 101u, 103u, 107u, 109u, 113u,   
>          127u, 131u, 137u, 139u, 149u, 151u, 157u, 163u,   
>          167u, 173u, 179u, 181u, 191u, 193u, 197u, 199u,   
>          211u, 223u, 227u, 229u, 233u, 239u, 241u, 251u  
>       }};  
> static constexpr std::array<std::uint16_t, 6488> a2 {{  
>          257u, 263u, 269u, 271u, 277u, 281u, 283u, 293u,   
>          307u, 311u, 313u, 317u, 331u, 337u, 347u, 349u, 353u,   
>          359u, 367u, 373u, 379u, 383u, 389u, 397u, 401u, 409u, // etc }};  
> // etc  
> };  
> using prime_data = prime_data_imp<true>;  
> ```  
>   
> Then reference the data as `prime_data::a1` etc in the code.  
  
@jzmaddock This yields similar linker errors:  
  
```  
Undefined symbols for architecture arm64:  
  "boost::math::detail::prime_data_imp<true>::a1", referenced from:  
      unsigned int boost::math::prime<boost::math::policies::policy<boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy> >(unsigned int, boost::math::policies::policy<boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy> const&) in test_hypergeometric_dist.o  
  "boost::math::detail::prime_data_imp<true>::a2", referenced from:  
      unsigned int boost::math::prime<boost::math::policies::policy<boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy> >(unsigned int, boost::math::policies::policy<boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy> const&) in test_hypergeometric_dist.o  
  "boost::math::detail::prime_data_imp<true>::a3", referenced from:  
      unsigned int boost::math::prime<boost::math::policies::policy<boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy> >(unsigned int, boost::math::policies::policy<boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy> const&) in test_hypergeometric_dist.o  
ld: symbol(s) not found for architecture arm64  
clang: error: linker command failed with exit code 1 (use -v to see invocation)  
```  
  
I can roll this back to everything being `static const` because we know it works, or we can start dropping toolchains (e.g. MSVC 14.0 and likely GCC-5) for non-conformance to C++14. I would say that's your call.

---

## Comment 9

> Username: jzmaddock  
> Created_at: 2023-01-25 18:03:25 UTC  
> Url: https://github.com/boostorg/math/pull/925#issuecomment-1404021144  

Let me investigate locally.

---

## Comment 10

> Username: mborland  
> Created_at: 2023-01-26 16:51:07 UTC  
> Url: https://github.com/boostorg/math/pull/925#issuecomment-1405300769  

@jzmaddock were you able to find anything?

---

## Comment 11

> Username: jzmaddock  
> Created_at: 2023-01-26 17:43:55 UTC  
> Url: https://github.com/boostorg/math/pull/925#issuecomment-1405368640  

Yes, I just need to do all the editing, and I'm about to pop out for the night, but hopefully I'll have a fix shortly.

---

## Comment 12

> Username: jzmaddock  
> Created_at: 2023-01-29 12:20:54 UTC  
> Url: https://github.com/boostorg/math/pull/925#issuecomment-1407648927  

Closed via alternate fix in develop.

---
