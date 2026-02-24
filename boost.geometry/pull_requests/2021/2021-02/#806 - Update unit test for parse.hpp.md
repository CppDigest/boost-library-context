# #806 Update unit test for parse.hpp [Merged]

> Username: ayushgupta138  
> Created at: 2021-02-12 15:08:34 UTC  
> Updated at: 2021-02-17 06:19:53 UTC  
> Merged at: 2021-02-15 18:19:44 UTC  
> Closed at: 2021-02-15 18:19:44 UTC  
> Url: https://github.com/boostorg/geometry/pull/806  

Unit test for extensions/test/algorithms/parse.hpp for the issue #788

---

## Comment 1

> Username: ayushgupta138  
> Created_at: 2021-02-12 15:19:37 UTC  
> Updated_at: 2021-02-12 15:22:42 UTC  
> Url: https://github.com/boostorg/geometry/pull/806#issuecomment-778257090  

@mloskot  @vissarion  @awulkiew  could you please review the pr and suggest if you find anything wrong.

---

## Comment 2

> Username: mloskot  
> Created_at: 2021-02-15 08:25:48 UTC  
> Url: https://github.com/boostorg/geometry/pull/806#issuecomment-779046698  

@ayushgupta138 I have just merged PR #802 with initial (first) submission of the tests by @jkerkela   
  
Next, I'd like to combine your tests, which also cover the parser from different angle, into those tests.  
Would you be interested to do it?

---

## Comment 3

> Username: ayushgupta138  
> Created_at: 2021-02-15 08:27:11 UTC  
> Url: https://github.com/boostorg/geometry/pull/806#issuecomment-779047853  

Yes , I am interested in it. Let me know what you need from my side.

---

## Comment 4

> Username: mloskot  
> Created_at: 2021-02-15 08:29:46 UTC  
> Updated_at: 2021-02-15 08:30:12 UTC  
> Url: https://github.com/boostorg/geometry/pull/806#issuecomment-779049596  

@ayushgupta138 Great! Here is what I'd do:  
  
1. Update your `unit_test_parse` branch with the latest changes from `develop`.  
2. Resolve any conflicts - you will get some, obviously, as `extensions/test/algorithms/parse.cpp` is a new file added by both, #802 and #806  
3. Copy/Add your test cases to the `extensions/test/algorithms/parse.cpp`  
4. `git push --force` to update this PR

---

## Comment 5

> Username: ayushgupta138  
> Created_at: 2021-02-15 08:35:07 UTC  
> Url: https://github.com/boostorg/geometry/pull/806#issuecomment-779053407  

On it.

---

## Comment 6

> Username: ayushgupta138  
> Created_at: 2021-02-15 11:44:43 UTC  
> Url: https://github.com/boostorg/geometry/pull/806#issuecomment-779168353  

As mentioned by you, I made commits on this pr.

---

## Review 7 [Changes requested]

> Username: mloskot  
> Created_at: 2021-02-15 11:54:46 UTC  
> State: CHANGES_REQUESTED  
> Url: https://github.com/boostorg/geometry/pull/806#pullrequestreview-590406529  

📁 extensions/test/algorithms/Jamfile

```diff
  13 | #    [ run dissolve.cpp ]
  14 |     [ run distance_info.cpp ]
  15 |+     [ run parse.cpp]
```

> Username: mloskot  
> Created_at: 2021-02-15 11:50:52 UTC  
> Updated_at: 2021-02-15 17:56:45 UTC  
> Url: https://github.com/boostorg/geometry/pull/806#discussion_r576137809  

This is no longer necessary since parse.cpp has already been added, in #802

> Username: ayushgupta138  
> Created_at: 2021-02-15 12:29:22 UTC  
> Updated_at: 2021-02-15 17:56:45 UTC  
> Url: https://github.com/boostorg/geometry/pull/806#discussion_r576158490  

removed in new commit


📁 extensions/test/algorithms/parse.cpp

```diff
  30 |+ 	geometry = parse<Geometry>(coordinate_1, coordinate_2);
  31 |+ 	BOOST_CHECK_CLOSE(((double)bg::get<0>(geometry)), expected_result_coord_1, 1.0e-4);
  32 |+ 	BOOST_CHECK_CLOSE(((double)bg::get<1>(geometry)), expected_result_coord_2, 1.0e-4);
```

> Username: mloskot  
> Created_at: 2021-02-15 11:52:41 UTC  
> Updated_at: 2021-02-15 17:56:45 UTC  
> Url: https://github.com/boostorg/geometry/pull/806#discussion_r576138782  

Such change from `1.0e-6` to `1.0e-4`  needs a word of comment, ideally, in commit message

> Username: ayushgupta138  
> Created_at: 2021-02-15 12:29:40 UTC  
> Updated_at: 2021-02-15 17:56:45 UTC  
> Url: https://github.com/boostorg/geometry/pull/806#discussion_r576158616  

mentioned now

> Username: mloskot  
> Created_at: 2021-02-15 12:58:58 UTC  
> Updated_at: 2021-02-15 17:56:45 UTC  
> Url: https://github.com/boostorg/geometry/pull/806#discussion_r576175153  

Yes, the commit https://github.com/boostorg/geometry/pull/806/commits/138d3d53e66a65dfd12be0af7815bc9493ed49fc  does mention it:  
  
```  
and reduce the error range from 1e-6 to 1e-4  
```  
  
but this is just *repeating **what** the commit does change* (i.e. it is equivalent to what everyone can see in the diff :-)), but it does not explain **why** the range changed. The **why** is what the commit messages are for. The **what** everyone can see by looking at the diff of a commit.  
  
So, I'm hitting "Unresolve conversation" as this still needs clarification.

> Username: ayushgupta138  
> Created_at: 2021-02-15 17:15:10 UTC  
> Updated_at: 2021-02-15 17:56:45 UTC  
> Url: https://github.com/boostorg/geometry/pull/806#discussion_r576334083  

The range changed because I have also included test cases where the final value is very close to zero, so even though the difference between the actual value and expected value is of order 1e-9 , the percentage error exceed the previous error range of 1e-6 and had to be reduced to 1e-4. This was the case when 10 precision points were specified in the expected value. Is this explanation satisfying enough? If so I will add the explanation in the commit message after your confirmation.

> Username: mloskot  
> Created_at: 2021-02-15 17:21:45 UTC  
> Updated_at: 2021-02-15 17:56:45 UTC  
> Url: https://github.com/boostorg/geometry/pull/806#discussion_r576337676  

Yes, it is indeed.

---

📁 extensions/test/algorithms/parse.cpp

```diff
 109 |+ 
 110 |+ 	// test with random characters in strategy(for generality check)
 111 |+ 	typedef bg::projections::detail::dms_parser<coord_t, true, 'A', 'B', 'C', 'D', 'm', 's', 'o', 'p'> parser_t1;
```

> Username: mloskot  
> Created_at: 2021-02-15 11:54:17 UTC  
> Updated_at: 2021-02-15 17:56:45 UTC  
> Url: https://github.com/boostorg/geometry/pull/806#discussion_r576139646  

Since we moved to C++14, we should prefer  
```  
using parser_t1 = bg::projections::detail::dms_parser<coord_t, true, 'A', 'B', 'C', 'D', 'm', 's', 'o', 'p'>;  
```  
  
which places the alias name to the left, it's easier to read.

---

📁 extensions/test/algorithms/parse.cpp

```diff
 162 |- 	test_parse<bg::model::point<double, 2, bg::cs::cartesian> >(std::string("1W"),
 163 |- 		std::string("2S"), -1 * bg::math::d2r<double>(), -2 * bg::math::d2r<double>());
 171 |+ 	typedef typename bg::coordinate_type<Geometry>::type T;
```

> Username: mloskot  
> Created_at: 2021-02-15 11:54:40 UTC  
> Updated_at: 2021-02-15 17:56:45 UTC  
> Url: https://github.com/boostorg/geometry/pull/806#discussion_r576139831  

`using`


---

## Comment 8

> Username: mloskot  
> Created_at: 2021-02-15 12:21:44 UTC  
> Url: https://github.com/boostorg/geometry/pull/806#issuecomment-779187949  

@ayushgupta138 Please hit the "Resolve conversation" button for the comment threads that you have addressed.

---

## Comment 9

> Username: mloskot  
> Created_at: 2021-02-15 18:36:46 UTC  
> Url: https://github.com/boostorg/geometry/pull/806#issuecomment-779392052  

@ayushgupta138 Thank you

---
