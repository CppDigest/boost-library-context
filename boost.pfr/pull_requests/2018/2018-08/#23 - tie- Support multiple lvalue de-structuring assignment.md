# #23 tie: Support multiple lvalue de-structuring assignment. [Merged]

> Username: abutcher-gh  
> Created at: 2018-08-11 18:29:15 UTC  
> Updated at: 2018-09-03 19:15:21 UTC  
> Merged at: 2018-09-03 07:52:30 UTC  
> Closed at: 2018-09-03 07:52:30 UTC  
> Url: https://github.com/boostorg/pfr/pull/23  

Supports use cases such as:  
```  
  auto parseLinePrefix(char const* line) {  
       struct {  
            size_t byteCount, address, recordType; char const* rest;  
       } res;  
       using namespace boost::pfr;  
       tie (res.byteCount, line) = parseHex(line, 2);  
       tie (res.address, line) = parseHex(line, 4);  
       tie (res.recordType, line) = parseHex(line, 2);  
       res.rest = line;  
       return res;  
  }  
```  
where `parseHex` returns an internal anonymous  
```  
    struct { size_t val; char const* rest; }  
```  
Test case added.

---

## Comment 1

> Username: coveralls  
> Created_at: 2018-08-11 18:38:27 UTC  
> Updated_at: 2018-08-28 21:05:20 UTC  
> Url: https://github.com/boostorg/pfr/pull/23#issuecomment-412293935  

[![Coverage Status](https://coveralls.io/builds/18716114/badge)](https://coveralls.io/builds/18716114)  
  
Coverage decreased (-0.1%) to 97.734% when pulling **c53f623d4d560e49f330d7c974cc13bf41d1156b on abutcher-gh:destructuring-tie** into **b0229248de7ee4bc775e6421eb4a9925a902cabc on apolukhin:develop**.

---

## Comment 2

> Username: coveralls  
> Created_at: 2018-08-11 18:38:27 UTC  
> Url: https://github.com/boostorg/pfr/pull/23#issuecomment-412293936  

[![Coverage Status](https://coveralls.io/builds/18440499/badge)](https://coveralls.io/builds/18440499)  
  
Coverage decreased (-0.03%) to 97.838% when pulling **86c9934c408bec1fa522ec94ee6e49e7b3485ccd on abutcher-gh:destructuring-tie** into **b0229248de7ee4bc775e6421eb4a9925a902cabc on apolukhin:develop**.

---

## Comment 3

> Username: abutcher-gh  
> Created_at: 2018-08-12 08:32:49 UTC  
> Url: https://github.com/boostorg/pfr/pull/23#issuecomment-412327548  

@apolukhin, the builds seem to be failing due to some server side issue rather than anything to do with the code.  From the travis-ci/pr details page:  
```  
  $ sudo -E apt-add-repository -y "ppa:git-core/ppa"  
  Cannot add PPA: 'ppa:git-core/ppa'.  
  Please check that the PPA name or format is correct.  
  The command "sudo -E apt-add-repository -y "ppa:git-core/ppa"" failed and exited with 1 during .  
```  
  
Is that also affecting the coveralls result?  I added a test case and the coverage decreased!  According to a local run of `gcovr` there are no uncovered lines in the new test case and it covers the newly added facilities.  From the [coveralls details link](https://coveralls.io/builds/18441252) it suggests that `offset_based_getter.hpp` is the cause for the "red" but that is not something to which this PR directly relates.

---

## Review 4 [Changes requested]

> Username: apolukhin  
> Created_at: 2018-08-17 06:15:52 UTC  
> State: CHANGES_REQUESTED  
> Url: https://github.com/boostorg/pfr/pull/23#pullrequestreview-147107744  

Please also add a `flat_tie_from_structure` function for flat reflection and tests for it too.  
  
Many thanks for the PR!

📁 include/boost/pfr/detail/tie_tuple.hpp

```diff
   1 |+ // Distributed under the Boost Software License, Version 1.0. (See accompanying
   2 |+ // file LICENSE_1_0.txt or copy at http://www.boost.org/LICENSE_1_0.txt)
   3 |+ 
```

> Username: apolukhin  
> Created_at: 2018-08-17 06:11:41 UTC  
> Updated_at: 2018-08-28 20:52:40 UTC  
> Url: https://github.com/boostorg/pfr/pull/23#discussion_r210813063  

Do not forget to put your name here :)

---

📁 include/boost/pfr/detail/tie_tuple.hpp

```diff
   8 |+ #include <tuple>
   9 |+ 
  10 |+ namespace boost::pfr {
```

> Username: apolukhin  
> Created_at: 2018-08-17 06:11:54 UTC  
> Updated_at: 2018-08-28 20:52:40 UTC  
> Url: https://github.com/boostorg/pfr/pull/23#discussion_r210813095  

Please do not use nested namespaces as they are only available in C++17

---

📁 include/boost/pfr/detail/tie_tuple.hpp

```diff
  20 |+ ///
  21 |+ template <typename... Elements>
  22 |+ struct tie_tuple : std::tuple<Elements&...> {
```

> Username: apolukhin  
> Created_at: 2018-08-17 06:12:11 UTC  
> Updated_at: 2018-08-28 20:52:40 UTC  
> Url: https://github.com/boostorg/pfr/pull/23#discussion_r210813126  

Rename to the `tie_from_structure_tuple`

---

📁 include/boost/pfr/detail/tie_tuple.hpp

```diff
  23 |+     using base = std::tuple<Elements&...>;
  24 |+     using base::tuple;
  25 |+     using base::operator=;
```

> Username: apolukhin  
> Created_at: 2018-08-17 06:12:28 UTC  
> Updated_at: 2018-08-28 20:52:40 UTC  
> Url: https://github.com/boostorg/pfr/pull/23#discussion_r210813163  

Looks like this assignment operator is not required.

---

📁 include/boost/pfr/detail/tie_tuple.hpp

```diff
  26 |+     template <typename T>
  27 |+     constexpr tie_tuple& operator= (T&& t) {
  28 |+         base::operator=(boost::pfr::structure_to_tuple(std::forward<T>(t)));
```

> Username: apolukhin  
> Created_at: 2018-08-17 06:13:09 UTC  
> Updated_at: 2018-08-28 20:52:40 UTC  
> Url: https://github.com/boostorg/pfr/pull/23#discussion_r210813252  

This may not be the best solution. structure_to_tuple creates a std::tuple with values. Better solution would be to use something like `detail::make_stdtiedtuple_from_tietuple(detail::tie_as_tuple(std::forward<T>(t)),  std::make_index_sequence< tuple_size_v<T> >())`. A test with a non copyable and non movable type could be handy here


📁 include/boost/pfr/precise/core.hpp

```diff
 175 |+ /// \endcode
 176 |+ template <typename... Elements>
 177 |+ constexpr detail::tie_tuple<Elements...> tie(Elements&... args) noexcept {
```

> Username: apolukhin  
> Created_at: 2018-08-17 06:14:11 UTC  
> Updated_at: 2018-08-28 20:52:40 UTC  
> Url: https://github.com/boostorg/pfr/pull/23#discussion_r210813417  

`tie_from_structure` seems to be a more readable name.

> Username: abutcher-gh  
> Created_at: 2018-08-24 20:19:34 UTC  
> Updated_at: 2018-08-28 20:52:40 UTC  
> Url: https://github.com/boostorg/pfr/pull/23#discussion_r212744613  

My initial intention was to use `pfr` to create a more general-purpose `tie` as a substitute for `std::tie`.  I.e. it would be usable as the lvalue of an assignment in all cases that `std::tie` could and also to tie variables to structure fields.  I was originally thinking `flat_tie` for the flattened variant.  
  
In 060437a I've renamed as you suggest but I was wondering whether there was any mileage in supplying forwarding functions named `tie` and `flat_tie` in a sub-namespace, say `boost::pfr::stdcompat` or `boost::pfr::tie_compat` or so?

> Username: apolukhin  
> Created_at: 2018-08-28 09:08:03 UTC  
> Updated_at: 2018-08-28 20:52:40 UTC  
> Url: https://github.com/boostorg/pfr/pull/23#discussion_r213236965  

> In 060437a I've renamed as you suggest but I was wondering whether there was any mileage in supplying forwarding functions named tie and flat_tie in a sub-namespace, say boost::pfr::stdcompat or boost::pfr::tie_compat or so?  
  
This is actually a great idea for a separate PR. Having a `boost::pfr::stdext` like namespace that adds functionality to `std::get`,`std::tuple_size`,`std::tie` and other standard library functions may be extreamely useful.

> Username: abutcher-gh  
> Created_at: 2018-08-28 20:35:24 UTC  
> Updated_at: 2018-08-28 20:52:40 UTC  
> Url: https://github.com/boostorg/pfr/pull/23#discussion_r213462807  

Will do.

---

📁 include/boost/pfr/precise/core.hpp

```diff
 186 |+ ///     tie(i, ignore) = f();
 187 |+ /// \endcode
 188 |+ constexpr struct swallow_assign {
```

> Username: apolukhin  
> Created_at: 2018-08-17 06:14:21 UTC  
> Updated_at: 2018-08-28 20:52:40 UTC  
> Url: https://github.com/boostorg/pfr/pull/23#discussion_r210813439  

Move it to a common place (probably into pfr/detail/)

> Username: abutcher-gh  
> Created_at: 2018-08-24 20:20:41 UTC  
> Updated_at: 2018-08-28 20:52:40 UTC  
> Url: https://github.com/boostorg/pfr/pull/23#discussion_r212744907  

Moved to `pfr/common` as `ignore` is user-facing rather than an implementation detail.


---

## Review 5 [Changes requested]

> Username: apolukhin  
> Created_at: 2018-08-28 09:02:09 UTC  
> State: CHANGES_REQUESTED  
> Url: https://github.com/boostorg/pfr/pull/23#pullrequestreview-150017930  

Almost perfect! Added some nit-picks

📁 include/boost/pfr/common/tie_ignore.hpp

```diff
  20 |+ /// \endcode
  21 |+ constexpr struct swallow_assign {
  22 |+     template <typename T> void operator= (T&&) const {}
```

> Username: apolukhin  
> Created_at: 2018-08-28 08:37:20 UTC  
> Updated_at: 2018-08-28 20:52:40 UTC  
> Url: https://github.com/boostorg/pfr/pull/23#discussion_r213227323  

noexcept

---

📁 include/boost/pfr/common/tie_ignore.hpp

```diff
  21 |+ constexpr struct swallow_assign {
  22 |+     template <typename T> void operator= (T&&) const {}
  23 |+ } ignore;
```

> Username: apolukhin  
> Created_at: 2018-08-28 08:59:56 UTC  
> Updated_at: 2018-08-28 20:52:40 UTC  
> Url: https://github.com/boostorg/pfr/pull/23#discussion_r213234426  

Hmm... I've forgot: why not just use `std::ignore`? In that case we do not need this header file, but rather just a simple `using std::ignore;` in `*tie_from_structure`. Or even not import it and force the users write `flat_tie_from_structure(x, y, std::ignore) = f();`.  
  
There are always some creepy ODR violations whith constexpr tag types before C++17, so I'd rather leave as much as possible of that stuff to the standard library implementation.

> Username: abutcher-gh  
> Created_at: 2018-08-28 20:21:51 UTC  
> Updated_at: 2018-08-28 20:52:40 UTC  
> Url: https://github.com/boostorg/pfr/pull/23#discussion_r213458763  

It was for disambiguation of an unqualified `tie` call with `using boost::pfr::tie` (or `using namespace boost::pfr`) active.  ADL caused the use of `std::ignore` to add `std::tie` to the overload set making the call to `tie` ambiguous.  
  
Since renaming `tie` to be more pfr-specific, and since we're moving the `stdext` stuff to a separate PR, I'll ditch this from here (requiring the user to specify `std::ignore`) and reconsider it there.


📁 include/boost/pfr/detail/tie_from_structure_tuple.hpp

```diff
   7 |+ #define BOOST_PFR_DETAIL_TIE_FROM_STRUCTURE_TUPLE_HPP
   8 |+ #pragma once
   9 |+ 
```

> Username: apolukhin  
> Created_at: 2018-08-28 08:40:54 UTC  
> Updated_at: 2018-08-28 20:52:40 UTC  
> Url: https://github.com/boostorg/pfr/pull/23#discussion_r213228382  

Missing includes for `tie_as_tuple` and `make_stdtiedtuple_from_tietuple`

> Username: abutcher-gh  
> Created_at: 2018-08-28 20:14:23 UTC  
> Updated_at: 2018-08-28 20:52:40 UTC  
> Url: https://github.com/boostorg/pfr/pull/23#discussion_r213456517  

It looked to me like the appropriate header to define these was `precise/core.hpp` (or `flat/core.hpp` for the flat variant) as they make different choices about which `detail/core*.hpp` to include.  Since this detail header itself is only included from the top-level "feature" core.hpp I wasn't sure whether to repeat the inclusion logic.  I opted for just "requiring" this to be included after the tuple_size and coreXX,hpp headers.  
  
Is there any reason why the flat code is fixed to use the `detail/core14.hpp` header?  If there were a `detail/core.hpp` that did the inclusion logic that would be simpler for other headers to pull.  
  
It tends to be a library policy decision on whether "detail" headers should be "self-supporting" or whether they should just require to be included at the right time by the library's other headers.  I take it you're aiming for them to each to be self-supporting?

> Username: apolukhin  
> Created_at: 2018-09-03 07:50:30 UTC  
> Url: https://github.com/boostorg/pfr/pull/23#discussion_r214601396  

> Is there any reason why the flat code is fixed to use the detail/core14.hpp header?  
  
Flat version tends to be the most stable version that does not rely on C++ core issues and behaves exactly the same way on different version of the C++ standard. My best attempt to do it - always use the c++14 implementation

> Username: apolukhin  
> Created_at: 2018-09-03 07:51:18 UTC  
> Url: https://github.com/boostorg/pfr/pull/23#discussion_r214601604  

> I take it you're aiming for them to each to be self-supporting?  
  
Yes

---

📁 include/boost/pfr/detail/tie_from_structure_tuple.hpp

```diff
  23 |+         base::operator=(
  24 |+             make_stdtiedtuple_from_tietuple(
  25 |+                 tie_as_tuple(t),
```

> Username: apolukhin  
> Created_at: 2018-08-28 08:42:18 UTC  
> Updated_at: 2018-08-28 20:52:40 UTC  
> Url: https://github.com/boostorg/pfr/pull/23#discussion_r213228761  

Add `detail::` to disable ADL. Otherwise some people amy get surprised, if they have `tie_as_tuple` in namespace of `t`


📁 include/boost/pfr/detail/flat_tie_from_structure_tuple.hpp

```diff
  24 |+         base::operator=(
  25 |+             make_stdtiedtuple_from_tietuple(
  26 |+                 tie_as_flat_tuple(std::forward<T>(t)),
```

> Username: apolukhin  
> Created_at: 2018-08-28 08:44:31 UTC  
> Updated_at: 2018-08-28 20:52:40 UTC  
> Url: https://github.com/boostorg/pfr/pull/23#discussion_r213229431  

Looks like `std::forward` is not required here.  
  
Add `detail::` to disable ADL. Otherwise some people amy get surprised, if they have tie_as_tuple in namespace of `t`

> Username: abutcher-gh  
> Created_at: 2018-08-28 20:31:33 UTC  
> Updated_at: 2018-08-28 20:52:40 UTC  
> Url: https://github.com/boostorg/pfr/pull/23#discussion_r213461729  

> Looks like std::forward is not required here.  
  
Yup.  I've changed the RHS to `T const&` as per the "precise" version.


---

## Comment 6

> Username: apolukhin  
> Created_at: 2018-09-03 07:52:54 UTC  
> Url: https://github.com/boostorg/pfr/pull/23#issuecomment-418031488  

Many many thanks for the PR!

---
