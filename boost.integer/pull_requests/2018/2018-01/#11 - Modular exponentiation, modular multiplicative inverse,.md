# #11 Modular exponentiation, modular multiplicative inverse, [Merged]

> Username: NAThompson  
> Created at: 2018-01-28 20:50:18 UTC  
> Updated at: 2018-11-04 18:17:36 UTC  
> Merged at: 2018-11-03 19:46:30 UTC  
> Closed at: 2018-11-03 19:46:31 UTC  
> Url: https://github.com/boostorg/integer/pull/11  

extended Euclidean algorithm, discrete logarithm.  
  
A couple of design decision left:  
  
Should we stick with boost::optional or use std::optional?  
  
Should John's blazing fast integer square root be taken out of multiprecision and placed in boost.integer?  
  
How can we test a much greater range of input without killing the CI system?

---

## Comment 1

> Username: Lastique  
> Created_at: 2018-10-24 15:15:47 UTC  
> Updated_at: 2018-10-24 15:16:30 UTC  
> Url: https://github.com/boostorg/integer/pull/11#issuecomment-432701542  

A few quick comments:  
  
- Don't use Boost.Format, its dependency is unnecessary. Use simple string operations to compose error messages, or `std::ostringstream` if something complicated needs to be done.  
- Use `BOOST_THROW_EXCEPTION` to throw exceptions. `#include <stdexcept>`.  
- Use SFINAE to limit template parameters instead of hard failing with static asserts.  
- Boost.Integer is currently a C++03 only library. Generally, I don't mind adding bits that require C++11 or later, but I'm not sure it is absolutely required in all cases in this PR. In any case, if C++11 and later components are added, their minimum C++ requirements should be documented.  
- I'm not sure about the question regarding multiprecision as I'm not using it and not sure what is the functional division between Boost.Integer and Boost.Multiprecision.

---

## Comment 2

> Username: NAThompson  
> Created_at: 2018-10-24 20:30:04 UTC  
> Url: https://github.com/boostorg/integer/pull/11#issuecomment-432816619  

> Don't use Boost.Format, its dependency is unnecessary. Use simple string operations to compose error messages, or std::ostringstream if something complicated needs to be done.  
  
Good point, done.  
  
> Use BOOST_THROW_EXCEPTION to throw exceptions. #include <stdexcept>.  
  
Good point, done.  
  
> Use SFINAE to limit template parameters instead of hard failing with static asserts.  
  
The problem is that there won't be substitution failure. The worst case is the extended Euclidean algorithm, where the solution only exists for signed integer types, but no operation required by the algorithm is undefined for unsigned types. The result is just nonsense.  
  
As for the other cases checking that the type is integral, I'm less passionate about that. Presumably someone could get it to compile with a floating point type or over some ring, but maybe that's on them.  
  
> Boost.Integer is currently a C++03 only library. Generally, I don't mind adding bits that require C++11 or later, but I'm not sure it is absolutely required in all cases in this PR. In any case, if C++11 and later components are added, their minimum C++ requirements should be documented.  
  
I've `#included` the `<tuple>` header for the extended Euclidean algorithm; arguably this is unnecessary and error prone and I should really be returning a generic struct. I've yet to find a good name for this so it'll have to wait for now. I've also used `std::unordered_map` from C++11; again, arguably I could use `boost::unordered_map`. That would make the dependency graph more complicated and presumably increase the maintenance burden. It looks like `boost::unordered` is not getting new algorithms now, and is only being updated to keep the build alive.  
  
> I'm not sure about the question regarding multiprecision as I'm not using it and not sure what is the functional division between Boost.Integer and Boost.Multiprecision.  
  
I think the correct long-term decision is to move the multiprecision sqrt functionality into integer, but for now I feel like it's not a showstopper.

---

## Comment 3

> Username: NAThompson  
> Created_at: 2018-10-26 18:06:51 UTC  
> Updated_at: 2018-10-26 18:30:46 UTC  
> Url: https://github.com/boostorg/integer/pull/11#issuecomment-433495549  

For the modular inverse and Extended euclidean algorithm, I have performed the following checks:  
  
- Run every single combination of 16 bit inputs and verified that the results are correct. Obviously I can't push this into the CI system or it'd die.  
- Run a vast number of 32 bit inputs (overnight), and verified that the results are correct.  
- Run a vast number of 64 and 128 bit inputs, and verified that the results are correct.  
- Run all the tests under UBSAN, and demonstrated no undefined behavior is detected by this tool.  
- Made sure it compiles with 16, 32, and 64 bit standard integer types, as well as 128 and 256 bit boost.multiprecision integer types.  
- Ensured that the cppcheck static analysis was clean.  
- Wrote a benchmark (not committed to the repo, since it uses googlebenchmark) and found the following performance characteristics on a 2.7GHz intel laptop:  
  
```  
----------------------------------------------------------------------  
Benchmark                               Time           CPU Iterations  
----------------------------------------------------------------------  
BM_ModInverse<int32_t>                133 ns        133 ns    5614598  
BM_ModInverse<int64_t>                137 ns        137 ns    5497224  
BM_ModInverse<int128_t>               412 ns        411 ns    1638546  
BM_ModInverse<int256_t>              1027 ns       1024 ns     744420  
BM_ExtendedEuclidean<int32_t>         129 ns        129 ns    6226207  
BM_ExtendedEuclidean<int64_t>         133 ns        133 ns    5983162  
BM_ExtendedEuclidean<int128_t>        394 ns        393 ns    1719817  
BM_ExtendedEuclidean<int256_t>        970 ns        968 ns     755801  
```  
  
- I was unable to regenerate `integer/doc/html/index.html`. But I was able to generate an xml file which had my required changes.  
  
We'll see if I can get the discrete log working; it appears that it's a bit more subtle than I first thought.

---

## Comment 4

> Username: NAThompson  
> Created_at: 2018-10-27 02:15:23 UTC  
> Url: https://github.com/boostorg/integer/pull/11#issuecomment-433583114  

Ok, this PR is finished, but the CI system needs a lot of TLC.

---

## Comment 5

> Username: Lastique  
> Created_at: 2018-10-29 14:09:52 UTC  
> Url: https://github.com/boostorg/integer/pull/11#issuecomment-433923736  

I've updated CI configs, could you rebase your PR over the current develop?

---

## Comment 6

> Username: NAThompson  
> Created_at: 2018-10-29 15:03:13 UTC  
> Url: https://github.com/boostorg/integer/pull/11#issuecomment-433944740  

> I've updated CI configs, could you rebase your PR over the current develop?  
  
Done, fingers crossed that the build will succeed. Do you understand the purpose of the rest of these tests? Some just generate huge numbers of warnings, so it might be worth considering their value and potentially removing them.

---

## Comment 7

> Username: Lastique  
> Created_at: 2018-10-29 15:16:15 UTC  
> Url: https://github.com/boostorg/integer/pull/11#issuecomment-433949874  

> Do you understand the purpose of the rest of these tests?  
  
I don't. I have very little knowledge about the library beyond that it allows to construct an integer type by size in bits.  
  
Looking at the current PR, maybe we should move the definition of `DISABLE_MP_TESTS` to a common header?  
  
Other than that and provided that the tests pass, do you consider the PR done?

---

## Comment 8

> Username: Lastique  
> Created_at: 2018-10-29 15:30:05 UTC  
> Url: https://github.com/boostorg/integer/pull/11#issuecomment-433955693  

I forgot about this:  
  
>>    Use SFINAE to limit template parameters instead of hard failing with static asserts.  
>  
> The problem is that there won't be substitution failure. The worst case is the extended Euclidean algorithm, where the solution only exists for signed integer types, but no operation required by the algorithm is undefined for unsigned types. The result is just nonsense.  
>  
> As for the other cases checking that the type is integral, I'm less passionate about that. Presumably someone could get it to compile with a floating point type or over some ring, but maybe that's on them.  
  
My point was that having a hard failure via a static assert is rarely useful. If an algorithm is only usable with a certain subset of types (e.g. signed integers) then the corresponding template parameter should be constrained as such so that the algorithm is not callable if the requirements are not met. This allows to overload the algorithm for other types that might still make sense with the algorithm (e.g. multiprecision types) but require a different implementation. A hard failure prevents this.  
  
Of course, it may be too difficult or expensive to check for the type constraints correctly. In which case you would normally only document the requirements but not enforce them in the code (neither by SFINAE nor by static asserts). Of course, this is a less preferred approach, but it is still better than static asserts because it doesn't fail in compile time if the template parameters happen to match the requirements.

---

## Comment 9

> Username: Lastique  
> Created_at: 2018-10-29 15:37:03 UTC  
> Url: https://github.com/boostorg/integer/pull/11#issuecomment-433958511  

> I've also used std::unordered_map from C++11; again, arguably I could use boost::unordered_map.  
  
`std::unordered_map` would be fine as long as C++11 requirement was documented. But I can see it is no longer used.

---

## Comment 10

> Username: NAThompson  
> Created_at: 2018-10-29 16:39:41 UTC  
> Updated_at: 2018-10-29 16:51:06 UTC  
> Url: https://github.com/boostorg/integer/pull/11#issuecomment-433983403  

> Other than that and provided that the tests pass, do you consider the PR done?  
  
Yes. The algorithm really hasn't changed at all in the last 10 or so commits, it's all fighting the CI system.  
  
> If an algorithm is only usable with a certain subset of types (e.g. signed integers) then the corresponding template parameter should be constrained as such so that the algorithm is not callable if the requirements are not met.   
  
This is something I have never done before; I just read about constraining template parameters, and the only example I've seen is how to constrain the template to a pointer type. Do you know how to constrain a template to a signed type without a static assert? I guess I could initialize a temporary to -1.

---

## Comment 11

> Username: Lastique  
> Created_at: 2018-10-29 17:02:10 UTC  
> Updated_at: 2018-10-29 17:03:14 UTC  
> Url: https://github.com/boostorg/integer/pull/11#issuecomment-433992504  

> Do you know how to constrain a template to a signed type without a static assert?  
  
For example, like so:  
  
```  
// Accepts a signed number-like type and returns a value of return_type  
template< typename T >  
typename boost::enable_if_c<  
    std::numeric_limits< T >::is_specialized && std::numeric_limits< T >::is_signed,  
    return_type  
>::type  
foo(T arg)  
{  
    return ret_val; // the return type is return_type  
}  
```  
  
This should be callable for any signed number-like types, which have `std::numeric_limits` specialized. This includes fundamental C++ types, as well as Boost.Multiprecision types. If you want to lock the algorithm to just the fundamental integers, you can use `boost::is_integral`/`std::is_integral` in the condition.  
  
If you're using C++11 already, you can use `std::enable_if` instead of `boost::enable_if_c`. The default type for `return_type`, if omitted, is `void`. See [here](https://www.boost.org/doc/libs/release/libs/core/doc/html/core/enable_if.html).

---

## Review 12 [Changes requested]

> Username: Lastique  
> Created_at: 2018-10-29 18:49:57 UTC  
> State: CHANGES_REQUESTED  
> Url: https://github.com/boostorg/integer/pull/11#pullrequestreview-169460106  

📁 include/boost/integer/extended_euclidean.hpp

```diff
  23 |+ };
  24 |+ 
  25 |+ using std::numeric_limits;
```

> Username: Lastique  
> Created_at: 2018-10-29 18:44:07 UTC  
> Updated_at: 2018-11-02 19:46:01 UTC  
> Url: https://github.com/boostorg/integer/pull/11#discussion_r229052547  

No global using directives, please.

> Username: NAThompson  
> Created_at: 2018-10-29 19:09:02 UTC  
> Updated_at: 2018-11-02 19:46:01 UTC  
> Url: https://github.com/boostorg/integer/pull/11#discussion_r229060856  

Fixed.

---

📁 include/boost/integer/extended_euclidean.hpp

```diff
  38 |+     {
  39 |+       swapped = true;
  40 |+       std::swap(m, n);
```

> Username: Lastique  
> Created_at: 2018-10-29 18:49:26 UTC  
> Updated_at: 2018-11-02 19:46:01 UTC  
> Url: https://github.com/boostorg/integer/pull/11#discussion_r229054375  

Change this to `using std::swap` and an unqualified call to `swap` so that any user-defined overloads are picked up.

> Username: NAThompson  
> Created_at: 2018-10-29 19:01:05 UTC  
> Updated_at: 2018-11-02 19:46:01 UTC  
> Url: https://github.com/boostorg/integer/pull/11#discussion_r229058264  

Yup, good point. Done.


📁 test/extended_euclidean_test.cpp

```diff
  36 |+             int256_t x = u.x;
  37 |+             int256_t y = u.y;
  38 |+             assert(u.gcd == gcdmn);
```

> Username: Lastique  
> Created_at: 2018-10-29 18:46:17 UTC  
> Updated_at: 2018-11-02 19:46:01 UTC  
> Url: https://github.com/boostorg/integer/pull/11#discussion_r229053267  

Asserts will be ignored in release builds. Use `boost/core/lightweight_test.hpp` instead. The same goes for the other tests.

> Username: NAThompson  
> Created_at: 2018-10-29 19:09:06 UTC  
> Updated_at: 2018-11-02 19:46:01 UTC  
> Url: https://github.com/boostorg/integer/pull/11#discussion_r229060877  

Fixed.


📁 include/boost/integer/mod_inverse.hpp

```diff
  26 |+     if (modulus < 2)
  27 |+     {
  28 |+         BOOST_THROW_EXCEPTION(std::domain_error("Modulus must be > 1.\n"));
```

> Username: Lastique  
> Created_at: 2018-10-29 18:47:48 UTC  
> Updated_at: 2018-11-02 19:46:01 UTC  
> Url: https://github.com/boostorg/integer/pull/11#discussion_r229053817  

Typically, you don't add a newline to exception messages, if it only contains one line.

> Username: NAThompson  
> Created_at: 2018-10-29 19:10:33 UTC  
> Updated_at: 2018-11-02 19:46:01 UTC  
> Url: https://github.com/boostorg/integer/pull/11#discussion_r229061331  

Fixed.


---

## Comment 13

> Username: Lastique  
> Created_at: 2018-11-01 16:45:52 UTC  
> Url: https://github.com/boostorg/integer/pull/11#issuecomment-435104864  

Looks like CI test only failed for gcc 4.7 in C++11 mode due to a problem in Boost.Multiprecision. I think we can disable Boost.Multiprecision on gcc 4.7 in C++11 mode for now. And could you report the problem to Multiprecision?

---

## Comment 14

> Username: jzmaddock  
> Created_at: 2018-11-01 17:42:19 UTC  
> Url: https://github.com/boostorg/integer/pull/11#issuecomment-435123498  

>And could you report the problem to Multiprecision?  
  
I've seen it, and I don't know how to fix it :(

---

## Comment 15

> Username: Lastique  
> Created_at: 2018-11-01 17:58:10 UTC  
> Url: https://github.com/boostorg/integer/pull/11#issuecomment-435128917  

@jzmaddock Does any other code branch at https://github.com/boostorg/multiprecision/blob/30bc41706d8e8a1cb7e7f060c075123acb4a164c/include/boost/multiprecision/number.hpp#L888 work with gcc 4.7? It seems, the compiler is struggling with `boost::is_destructible`, which is called by `boost::is_constructible` at https://github.com/boostorg/multiprecision/blob/30bc41706d8e8a1cb7e7f060c075123acb4a164c/include/boost/multiprecision/number.hpp#L910. Although imperfect, the other code branches do without it.

---

## Review 16 [Commented]

> Username: Lastique  
> Created_at: 2018-11-01 18:07:36 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/integer/pull/11#pullrequestreview-170811528  

📁 test/mod_inverse_test.cpp

```diff
   8 |       (defined(__clang_major__) && (__clang_major__ == 3) && (__clang_minor__ < 2)) || \
   9 |-       (defined(BOOST_GCC) && defined(BOOST_GCC_CXX11) && BOOST_GCC < 40800)
   9 |+       (defined(BOOST_GCC) && defined(BOOST_GCC_CXX11) && BOOST_GCC < 50000)
```

> Username: Lastique  
> Created_at: 2018-11-01 18:07:36 UTC  
> Updated_at: 2018-11-02 19:46:01 UTC  
> Url: https://github.com/boostorg/integer/pull/11#discussion_r230141608  

This is not right. The real problem is a missing `#include <boost/config.hpp>`.

> Username: Lastique  
> Created_at: 2018-11-01 18:08:53 UTC  
> Updated_at: 2018-11-02 19:46:01 UTC  
> Url: https://github.com/boostorg/integer/pull/11#discussion_r230142071  

Same in the other test.

> Username: NAThompson  
> Created_at: 2018-11-01 18:11:30 UTC  
> Updated_at: 2018-11-02 19:46:01 UTC  
> Url: https://github.com/boostorg/integer/pull/11#discussion_r230143031  

Wanna cancel that build then?


---

## Comment 17

> Username: jzmaddock  
> Created_at: 2018-11-01 18:22:28 UTC  
> Url: https://github.com/boostorg/integer/pull/11#issuecomment-435137243  

>Does any other code branch at https://github.com/boostorg/multiprecision/blob/30bc41706d8e8a1cb7e7f060c075123acb4a164c/include/boost/multiprecision/number.hpp#L888 work with gcc 4.7?  
  
Sure, but they either disable the explicit conversion operators, or use a simplified semi-broken version that will fail in other situations :(  
  
One could ague that the issue is actually inside type_traits, but in reality it's gcc-4.7's pre-standard C++11 support.  If the only issue is use of multiprecision types in `optional` then I'm inclined to settle for that as the least worse compromise for now.

---

## Comment 18

> Username: NAThompson  
> Created_at: 2018-11-03 19:00:03 UTC  
> Url: https://github.com/boostorg/integer/pull/11#issuecomment-435612625  

Ok, are we all happy with this? It'd be nice to get it pushed past the finish line.

---

## Comment 19

> Username: Lastique  
> Created_at: 2018-11-03 19:46:45 UTC  
> Url: https://github.com/boostorg/integer/pull/11#issuecomment-435615660  

Thanks, Nick.

---

## Comment 20

> Username: Lastique  
> Created_at: 2018-11-04 07:48:11 UTC  
> Url: https://github.com/boostorg/integer/pull/11#issuecomment-435649481  

@jzmaddock Apparently, the problem with Boost.Multiprecision and Boost.Optional is not only limited to gcc 4.7 but also any clang in C++03 mode:  
  
https://travis-ci.org/boostorg/integer/builds/450332419  
  
I think this is a serious problem that needs to be solved.

---

## Comment 21

> Username: NAThompson  
> Created_at: 2018-11-04 17:01:18 UTC  
> Url: https://github.com/boostorg/integer/pull/11#issuecomment-435686594  

> I think this is a serious problem that needs to be solved.  
  
One workaround would be to just not use `boost::optional<Z>` as the return type from `mod_inverse` and instead return a `Z`. Non-existence of the modular inverse could be communicated by returning zero.

---

## Comment 22

> Username: Lastique  
> Created_at: 2018-11-04 18:17:36 UTC  
> Url: https://github.com/boostorg/integer/pull/11#issuecomment-435692846  

That wouldn't solve the problem users might still have with Boost.Multiprecision+Boost.Optional. Or any other combination that has the same properties.  
  
I've created two PRs in both libraries which should resolve the problem on both sides. Let's see what the maintainers say.

---
