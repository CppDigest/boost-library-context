# #148 Add support for C++17 std::variant [Merged]

> Username: sdebionne  
> Created at: 2019-03-11 16:41:45 UTC  
> Updated at: 2023-09-03 20:06:01 UTC  
> Merged at: 2023-09-03 20:06:00 UTC  
> Closed at: 2023-09-03 20:06:01 UTC  
> Url: https://github.com/boostorg/serialization/pull/148  

Partially fixes #114 and drops support for MSVC6.

---

## Comment 1

> Username: sdebionne  
> Created_at: 2019-03-12 09:19:36 UTC  
> Url: https://github.com/boostorg/serialization/pull/148#issuecomment-471918933  

@robertramey Robert, do you know how I could set `std=c++17` only for a specific test with Boost.Build? Some compilers in the pipeline do not support C++17, is there a way to detect the feature and not run the test with these older compilers?

---

## Comment 2

> Username: robertramey  
> Created_at: 2019-03-12 14:56:15 UTC  
> Url: https://github.com/boostorg/serialization/pull/148#issuecomment-472035385  

the normal way to do this is to include a line in the Jamfile which looks like the following:  
  
     [ test-bsl-run_files test_array : A : :  [ requires cxx11_hdr_array ] ] # BOOST_NO_CXX11_HDR_ARRAY  
  
This will enable the test for all compilers which support std::array.  This depends in boost config defining BOOST_NO_CXX11_HDR_ARRAY.  So in your case you'll need to use BOOST_NO_CXX11_HDR_VARIANT.  But I see that boost config doesn't include this yet.  So you'll have to bug John Maddock to get this included.  They you'll be good to go.  BTW - since you're on this, you might want to look at boost variant2 - in development by peter dimov

---

## Comment 3

> Username: sdebionne  
> Created_at: 2019-03-12 21:17:11 UTC  
> Url: https://github.com/boostorg/serialization/pull/148#issuecomment-472184746  

AFAIU, `variant2` is a drop in replacement of `std::variant` for C+11 so it should work out of the box (beside the namespace), are you suggesting to add support for this as well?  
  
I try to add a property `cxxstd=17` in the test requirements of the variant test, but that gives `error: 'cxxstd=17' is not a valid property specification`. What would be the right way to switch to C++17 for a single test?

---

## Comment 4

> Username: sdebionne  
> Created_at: 2019-06-04 16:38:37 UTC  
> Updated_at: 2019-09-12 15:31:51 UTC  
> Url: https://github.com/boostorg/serialization/pull/148#issuecomment-498749282  

@robertramey The only test (test_polymorphic_helper_polymorphic_binary_archive) that do not pass actually fails on the develop branch too. Any idea what is wrong?

---

## Comment 5

> Username: robertramey  
> Created_at: 2019-06-04 20:07:54 UTC  
> Url: https://github.com/boostorg/serialization/pull/148#issuecomment-498823227  

> @robertramey The only test (test_polymorphic_helper_polymorphic_binary_archive) that do not pass actually fails on the develop branch to. Any idea what is wrong?  
  
 Notice that it only happens with gcc on windows (mingw?), with polymorphic_binary_archive, on static linking.  The executable seems to run but then ... the expected *.test file isn't found.  I'm guessing that this is indicates some obscure issue with the jam file in the test directory.   I haven't been able to find it.  The only real way to would be to test on a local machine with that configuration - which I don't have.   
  
So I haven't worried about this failure.  If you wanted to spend some time on this obscure circumstance, that would be great.  But you can ignore it as well.

---

## Comment 6

> Username: p12tic  
> Created_at: 2019-07-04 17:59:15 UTC  
> Updated_at: 2019-07-04 17:59:28 UTC  
> Url: https://github.com/boostorg/serialization/pull/148#issuecomment-508549183  

@robertramey @sdebionne : Is there any way I could help to land this PR? Is it that test failure that prevents merging or something else? `std::variant` support would be really useful for us. Thanks!

---

## Review 7 [Commented]

> Username: p12tic  
> Created_at: 2019-07-04 20:23:20 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/serialization/pull/148#pullrequestreview-258164097  

📁 include/boost/serialization/std_variant.hpp

```diff
  35 |+ 
  36 |+ template<class Archive>
  37 |+ struct variant_save_visitor
```

> Username: p12tic  
> Created_at: 2019-07-04 20:23:19 UTC  
> Updated_at: 2020-05-05 17:49:14 UTC  
> Url: https://github.com/boostorg/serialization/pull/148#discussion_r300494372  

This will clash with a class with the same name on boost::variant's variant.hpp


---

## Review 8 [Commented]

> Username: p12tic  
> Created_at: 2019-07-04 20:24:41 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/serialization/pull/148#pullrequestreview-258164305  

📁 include/boost/serialization/std_variant.hpp

```diff
 125 |+             type value;
 126 |+             ar >> BOOST_SERIALIZATION_NVP(value);
 127 |+             v = value;
```

> Username: p12tic  
> Created_at: 2019-07-04 20:24:41 UTC  
> Updated_at: 2020-05-05 17:49:14 UTC  
> Url: https://github.com/boostorg/serialization/pull/148#discussion_r300494541  

We could use `std::move` to allow non-copy-constructible types. The serialization support for boost::variant has the same issue though.


---

## Review 9 [Commented]

> Username: p12tic  
> Created_at: 2019-07-04 20:25:28 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/serialization/pull/148#pullrequestreview-258164424  

📁 include/boost/serialization/std_variant.hpp

```diff
 107 |+ }  // namespace mp
 108 |+ 
 109 |+ template<std::size_t N, class Seq>
```

> Username: p12tic  
> Created_at: 2019-07-04 20:25:28 UTC  
> Updated_at: 2020-05-05 17:49:14 UTC  
> Url: https://github.com/boostorg/serialization/pull/148#discussion_r300494630  

N looks like unused...

> Username: sdebionne  
> Created_at: 2019-07-05 07:20:03 UTC  
> Updated_at: 2020-05-05 17:49:14 UTC  
> Url: https://github.com/boostorg/serialization/pull/148#discussion_r300566580  

`N` is used for recursion, see line 134: `variant_impl<N - 1, types>::load(ar, which - 1, v, version);`

> Username: p12tic  
> Created_at: 2019-07-05 10:33:44 UTC  
> Updated_at: 2020-05-05 17:49:14 UTC  
> Url: https://github.com/boostorg/serialization/pull/148#discussion_r300631744  

Ah, I missed that the types parameter is the same.


---

## Comment 10

> Username: sdebionne  
> Created_at: 2019-07-05 07:13:49 UTC  
> Url: https://github.com/boostorg/serialization/pull/148#issuecomment-508656470  

@p12tic Thanks for the review, I think all your points are valid, I'll fix that ASAP.  
  
Regarding the merge, I won't have time to investigate the (unrelated) pipeline failure now, but I think @robertramey was OK to merge as is.  
  
Boost.Variant2 is landing in 1.71. Since it has the same interface than the `std`, I wonder if we could factorize both serializations here.  
  
As a side note, I have an `std::optional` serialization to, but will open a separate PR not to delay this one.

---

## Review 11 [Commented]

> Username: pdimov  
> Created_at: 2019-09-06 10:43:34 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/serialization/pull/148#pullrequestreview-284788432  

📁 include/boost/serialization/std_variant.hpp

```diff
 163 |+             )
 164 |+         );
 165 |+     variant_impl<sizeof...(Types), mp::typelist<Types...>>::load(ar, which, v, version);
```

> Username: pdimov  
> Created_at: 2019-09-06 10:43:33 UTC  
> Updated_at: 2020-05-05 17:49:14 UTC  
> Url: https://github.com/boostorg/serialization/pull/148#discussion_r321678568  

I'd use mp11 here, specifically `mp_with_index` on `which`.

> Username: robertramey  
> Created_at: 2019-09-06 12:32:33 UTC  
> Updated_at: 2020-05-05 17:49:14 UTC  
> Url: https://github.com/boostorg/serialization/pull/148#discussion_r321711895  

Hmmm - this would add mp11 as a dependency of the serialization library.  I'd be reluctant to do this to accommodate one data type.  I understand that it wouldn't preclude the rest of the library from continue to be C++03 compliant. But the net result would be a pretty confusing situation where the level of compatibility would depend upon which types one is serializing.  
  
I've always advocated that serialization of boost types be specified in the particular boost library itself.  This eliminates the above problem.  But then library authors (justifiable) object to introduction of a dependency of their library on serialization.  The real solution to this conundrum is to complete the factorization of the serialization library into the two halves serialization and archive.  Turns out that most of this is done as I was aware of this from the beginning.  But I gave it up near the end as it was getting difficult to maintain while actually getting all the functionality that boost required to be inserted into the library.  Undertaking this task now would be possible, not require wholesale changes, but would be intricate and run the risk of introducing subtle problems and be somewhat tedious to undertake.

> Username: sdebionne  
> Created_at: 2019-09-12 15:44:04 UTC  
> Updated_at: 2020-05-05 17:49:14 UTC  
> Url: https://github.com/boostorg/serialization/pull/148#discussion_r323814912  

> I've always advocated that serialization of boost types be specified in the particular boost library itself.  
  
I get your point, but what about std types, like `std::variant`, then?  
  
Since `std::variant` is C++17 and Boost.Variant2 is C++11, if Peter thinks that mp11 is adequate for this work, I don't see a problem here. Adding a new dependency that does not trigger new requirements is pretty harmless to me.  
  
@robertramey Just tell me how to move forward!


---

## Comment 12

> Username: mateuszzz88  
> Created_at: 2020-05-04 15:24:05 UTC  
> Url: https://github.com/boostorg/serialization/pull/148#issuecomment-623530308  

I think you should add serialization of `std::monostate` - it is equivalent of `boost::blank`  
```  
namespace boost {  
namespace serialization {  
  
template<class Archive>  
void serialize(Archive &ar, std::monostate &, const unsigned int /*version*/)  
{}  
  
}  
}  
```  
I see no reason to put in separate file, variant.hpp seems ok (monostate is in the same header `<variant>`).

---

## Comment 13

> Username: sdebionne  
> Created_at: 2020-05-05 17:29:14 UTC  
> Url: https://github.com/boostorg/serialization/pull/148#issuecomment-624197690  

@pdimov Do you see any way I could support Boost.Variant2 without duplicating the code too much?

---

## Comment 14

> Username: pdimov  
> Created_at: 2020-05-05 17:44:34 UTC  
> Url: https://github.com/boostorg/serialization/pull/148#issuecomment-624205853  

If you remove the std:: from std::visit and std::get, the same code should also work for variant2. Instead of variant_size you can use mp_size which should work on both (but you'll need to remove qualifiers and references if present.) Also, what I said above about mp11::mp_with_index.

---

## Comment 15

> Username: robertramey  
> Created_at: 2020-05-05 17:52:10 UTC  
> Url: https://github.com/boostorg/serialization/pull/148#issuecomment-624209689  

> Instead of variant_size you can use mp_size which should work on both (but you'll need to remove qualifiers and references if present.) Also, what I said above about mp11::mp_with_index.  
  
Hmmm - this would bump the Compiler requirement from C++03 to C++11.   But then it would only do so for those using std::variant or boost::variant2.  So I'd have to think about this.

---

## Comment 16

> Username: robertramey  
> Created_at: 2020-05-05 17:58:59 UTC  
> Url: https://github.com/boostorg/serialization/pull/148#issuecomment-624213188  

Whoops.  I misspoke and I apologize.  I got the notice of a commit and didn't realize it was for your PR and not the boost develop repo.  Chalk it up to some GitHub confusion.

---

## Review 17 [Approved]

> Username: kilasuelika  
> Created_at: 2022-09-23 14:16:58 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/serialization/pull/148#pullrequestreview-1118547531  

I have test `std_variant`, it works perfectly.

---

## Comment 18

> Username: code-affinity  
> Created_at: 2023-01-03 19:39:15 UTC  
> Url: https://github.com/boostorg/serialization/pull/148#issuecomment-1370154340  

When I try to compile this header in a context in which <boost/serialization/variant.hpp> has already been included, there is a name conflict between the variant_impl struct that is declared in this header, and the variant_impl struct that is declared in <boost/serialization/variant.hpp>  
  
I resolved the problem by changing the three occurrences of variant_impl to stdvariant_impl.

---

## Comment 19

> Username: Febbe  
> Created_at: 2023-06-19 13:43:32 UTC  
> Url: https://github.com/boostorg/serialization/pull/148#issuecomment-1597219785  

@robertramey whats missing here? May you overtake this PR when it does not fulfill your expectations?

---
