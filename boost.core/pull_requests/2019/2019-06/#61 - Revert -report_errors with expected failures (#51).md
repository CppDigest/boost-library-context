# #61 Revert "report_errors with expected failures (#51)" [Closed]

> Username: Lastique  
> Created at: 2019-06-26 16:34:39 UTC  
> Updated at: 2020-05-08 10:50:43 UTC  
> Closed at: 2020-05-08 10:50:43 UTC  
> Url: https://github.com/boostorg/core/pull/61  

This reverts commit 02041f6c9f94d9e5d69f7c36f08624e1001143a0 and https://github.com/boostorg/core/pull/51.  
  
Reason for revert: The change allows for incorrect result code returned  
from the process if the number of failures is a multiple of 256. This  
may result in test failures being taken as success by the parent process.

---

## Comment 1

> Username: pdimov  
> Created_at: 2019-06-26 16:55:41 UTC  
> Url: https://github.com/boostorg/core/pull/61#issuecomment-505958973  

This should fail all tests that check for the correct number of failures, as mentioned. How about we apply https://github.com/boostorg/core/commit/eba08e936a807e0cae03c0d50160457f578b71aa instead.

---

## Comment 2

> Username: Lastique  
> Created_at: 2019-06-26 17:00:44 UTC  
> Url: https://github.com/boostorg/core/pull/61#issuecomment-505960732  

I'd rather have a different solution that allows to obtain the number of failures without hacks. Either a separate function (`get_failure_count`) or by adding an optional argument to `report_errors` (e.g. `int report_errors(int& failure_count);`). We can think of a better solution in due time, but for now and especially for 1.71 I'd rather revert to a bug-free version first.  
  
> This should fail all tests that check for the correct number of failures  
  
Do you know which tests these are?

---

## Comment 3

> Username: pdimov  
> Created_at: 2019-06-26 17:07:01 UTC  
> Url: https://github.com/boostorg/core/pull/61#issuecomment-505962935  

What are the remaining bugs in this version?  
  
Hans's original suggestion was for `return report_errors(expected);` instead of the current `return report_errors() == expected? 0: 1;`  
  
I gave this a fair bit of thought; what made me choose the latter was the observation that I already had `return report_errors() == expected;` in a number of places, that is, I already intuitively expected it to return the number of failures. This "worked" only because the two errors canceled out.

---

## Comment 4

> Username: Lastique  
> Created_at: 2019-06-26 17:11:36 UTC  
> Url: https://github.com/boostorg/core/pull/61#issuecomment-505964557  

> Hans's original suggestion was for `return report_errors(expected);` instead of the current `return report_errors() == expected? 0: 1;`  
  
This solves the original problem, so I'm fine with it. I just think `get_failure_count` would be more flexible.  
  
> I gave this a fair bit of thought; what made me choose the latter was the observation that I already had return report_errors() == expected; in a number of places, that is, I already intuitively expected it to return the number of failures.  
  
Maybe we can think of some sort of protection from mistakes like that? E.g. return a wrapper class that has comparison operators `= delete` and can be converted to `int`?  
  
In the meantime, do you have objections if I merge this PR? We can create a new PR for the final solution.

---

## Comment 5

> Username: pdimov  
> Created_at: 2019-06-26 17:14:27 UTC  
> Url: https://github.com/boostorg/core/pull/61#issuecomment-505965643  

Yes, I do have objections.  
  
What bugs remain after https://github.com/boostorg/core/commit/eba08e936a807e0cae03c0d50160457f578b71aa?

---

## Comment 6

> Username: Lastique  
> Created_at: 2019-06-26 17:15:34 UTC  
> Url: https://github.com/boostorg/core/pull/61#issuecomment-505966109  

> What bugs remain after eba08e9?  
  
It returns the wrong value if there are more than 255 failures.

---

## Comment 7

> Username: pdimov  
> Created_at: 2019-06-26 17:16:36 UTC  
> Url: https://github.com/boostorg/core/pull/61#issuecomment-505966461  

It's significantly less wrong than always returning 1.

---

## Comment 8

> Username: Lastique  
> Created_at: 2019-06-26 17:18:11 UTC  
> Url: https://github.com/boostorg/core/pull/61#issuecomment-505967073  

Well, no, given that `report_errors` did not return the number of failures (until #51). That someone assumed it did was a user error.

---

## Comment 9

> Username: Lastique  
> Created_at: 2019-06-26 17:19:26 UTC  
> Url: https://github.com/boostorg/core/pull/61#issuecomment-505967498  

Anyway, I don't see why we should argue about making it more or less "wrong" when we can just make it "right".

---

## Comment 10

> Username: pdimov  
> Created_at: 2019-06-26 17:37:42 UTC  
> Url: https://github.com/boostorg/core/pull/61#issuecomment-505974134  

> Well, no, given that report_errors did not return the number of failures  
  
Arguably a bug, because the documentation has always stated it did.

---

## Comment 11

> Username: Lastique  
> Created_at: 2019-06-26 18:07:41 UTC  
> Url: https://github.com/boostorg/core/pull/61#issuecomment-505985047  

The test in Boost.Core are broken even if you assume `report_errors` return the number of failures.

---

## Comment 12

> Username: Lastique  
> Created_at: 2019-06-26 18:09:37 UTC  
> Url: https://github.com/boostorg/core/pull/61#issuecomment-505985787  

> Arguably a bug, because the documentation has always stated it did.  
  
The docs also say to use `return boost::report_errors();` in main. So `report_errors` returning the number of failures contradicts that advice. I say, stop pretending that `report_errors` returns the number of errors.

---

## Comment 13

> Username: breese  
> Created_at: 2019-06-26 18:14:03 UTC  
> Url: https://github.com/boostorg/core/pull/61#issuecomment-505987352  

[POSIX](https://pubs.opengroup.org/onlinepubs/9699919799/functions/exit.html) confirms the 8-bit limitation.

---

## Comment 14

> Username: pdimov  
> Created_at: 2019-06-26 18:15:55 UTC  
> Url: https://github.com/boostorg/core/pull/61#issuecomment-505988034  

> The test in Boost.Core are broken even if you assume report_errors return the number of failures.  
  
How are they broken?

---

## Comment 15

> Username: Lastique  
> Created_at: 2019-06-26 18:27:01 UTC  
> Url: https://github.com/boostorg/core/pull/61#issuecomment-505992048  

@pdimov Could you look at https://github.com/Lastique/core/commit/a4c98cebaabfd0bb11c52cd73234efa02a9c5c50?  
  
> How are they broken?  
  
They contain `return boost::report_errors() == test_cases;`, which means returning 1 when there are no errors. Yes, they are `run-fail` in the Jamfile, but that is counter-intuitive.

---

## Comment 16

> Username: pdimov  
> Created_at: 2019-06-26 18:31:01 UTC  
> Url: https://github.com/boostorg/core/pull/61#issuecomment-505993580  

Counter-intuitive is not the same as broken.

---

## Comment 17

> Username: Lastique  
> Created_at: 2019-06-26 19:19:06 UTC  
> Url: https://github.com/boostorg/core/pull/61#issuecomment-506009866  

> Counter-intuitive is not the same as broken.  
  
True. I was writing "broken" before I found out they are `run-fail`. Still worth fixing.

---

## Comment 18

> Username: glenfe  
> Created_at: 2019-06-26 20:20:04 UTC  
> Updated_at: 2019-06-26 20:22:15 UTC  
> Url: https://github.com/boostorg/core/pull/61#issuecomment-506029573  

@Lastique a4c98ce seems overkill.  Instead of `return boost::report_errors() == N` it could be something like:  
```  
BOOST_REQUIRE_ERRORS(N);  
return boost::report_errors();  
```  
Where `report_errors()` is reverted to its old behavior (returning 0 or 1) and the documentation changed to reflect that this is what it does.  
  
This also allows us to do things like:  
````  
{  
    // test things  
    BOOST_REQUIRE_ERRORS(N);  
    // test things  
    BOOST_REQUIRE_ERRORS(M);  
    return boost::report_errors();  
}  
````  
The alternative spelling of `BOOST_REQUIRE_ERRORS(N)` could easily be something like `BOOST_REQUIRE(boost::test_errors() == N)`.   
  
(The function `test_errors()` already exists and is currently in `boost::detail` namespace but could easily be for public consumption in the `boost` namespace).

---

## Comment 19

> Username: Lastique  
> Created_at: 2019-06-26 20:45:37 UTC  
> Url: https://github.com/boostorg/core/pull/61#issuecomment-506037883  

@glenfe `BOOST_REQUIRE_ERRORS` can't be a check because it will just add/not add to the error count while the intention is to return 0 from `main` when the total number of errors matches some condition.  
  
Also, I updated that commit and created https://github.com/boostorg/core/pull/62 with it, you can comment there.

---

## Comment 20

> Username: glenfe  
> Created_at: 2019-06-26 21:34:49 UTC  
> Url: https://github.com/boostorg/core/pull/61#issuecomment-506054096  

@Lastique Couldn't `BOOST_REQUIRE_ERRORS(N)`  reset the error count to 0 if the error count is equal to N, otherwise, set the error count to 1? That way, `BOOST_REQUIRE_ERRORS(5); return boost::report_errors();` would result in returning `0` from main when we expected the error count at that point to be 5.

---

## Comment 21

> Username: Lastique  
> Created_at: 2019-06-26 21:44:27 UTC  
> Url: https://github.com/boostorg/core/pull/61#issuecomment-506056899  

It could, but then it would affect the final report on the console (it would say 0 or 1 errors detected instead of the actual number). This could be worked around too, but this is yet more hackery with the counter. I like my approach better because it is more straightforward and as a bonus flags all tests that are currently broken.

---

## Comment 22

> Username: Lastique  
> Created_at: 2019-06-26 22:03:18 UTC  
> Url: https://github.com/boostorg/core/pull/61#issuecomment-506061926  

@pdimov Do you still object to merging this PR (i.e. to revert `report_errors` to pre-#51 state)? We can proceed with our discussions about a better solution later.  
  
If you still object, please tell why.

---

## Comment 23

> Username: pdimov  
> Created_at: 2019-06-26 22:15:52 UTC  
> Url: https://github.com/boostorg/core/pull/61#issuecomment-506065054  

Yes, I do object.  
  
There's nothing wrong with the current state.  
  
After 1.71 ships, I'll extract lightweight_test into its own module, and leave the Core version for you to screw up as you please. But it's late for that now.

---

## Comment 24

> Username: Lastique  
> Created_at: 2019-06-26 22:22:36 UTC  
> Url: https://github.com/boostorg/core/pull/61#issuecomment-506066822  

Feel free to update the 1.71 release notes so that the happy users at least know what they're in for. Or you can tell me what to add in https://github.com/boostorg/website/pull/433.  
  
Personally, I think ignoring the obvious introduced bug is unacceptable.

---

## Comment 25

> Username: HDembinski  
> Created_at: 2019-06-27 05:32:36 UTC  
> Url: https://github.com/boostorg/core/pull/61#issuecomment-506196807  

Please do not make lightweight_tests into a separate module. I think it is great that we have minimal testing capabilities for Boost-internal use directly in Boost.Core. It is perfectly in line with the mission statement for Boost.Core - to provide a common foundation of tools for other Boost libraries - and it helps to keep the dependencies for other libs, such as Boost.Histogram small.

---

## Comment 26

> Username: pdimov  
> Created_at: 2019-06-27 10:26:08 UTC  
> Url: https://github.com/boostorg/core/pull/61#issuecomment-506289356  

As lwt started moving beyond just one simple BOOST_TEST macro and started attracting feature contributions, it would be better placed as a separate module anyway, with an improved documentation. I've wanted to do this for a while. Plus, testing shouldn't require pulling the whole Core, even though that's not really a practical problem at the moment.

---
