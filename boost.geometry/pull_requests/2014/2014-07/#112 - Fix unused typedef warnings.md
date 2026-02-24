# #112 Fix unused typedef warnings [Closed]

> Username: mkaravel  
> Created at: 2014-07-25 10:06:33 UTC  
> Updated at: 2014-07-27 14:16:49 UTC  
> Closed at: 2014-07-27 10:56:21 UTC  
> Url: https://github.com/boostorg/geometry/pull/112  

Comment unused local typedefs to fix g++-4.8 unused local typedef warnings.

---

## Comment 1

> Username: awulkiew  
> Created_at: 2014-07-25 13:46:40 UTC  
> Url: https://github.com/boostorg/geometry/pull/112#issuecomment-50150957  

Isn't the use of the bg::point_type<> trait a part of the test?  
Shouldn't ignore_unused<>() be used instead of commenting the types out?

---

## Comment 2

> Username: mkaravel  
> Created_at: 2014-07-25 14:16:45 UTC  
> Url: https://github.com/boostorg/geometry/pull/112#issuecomment-50154910  

@awulkiew I did not understand it this way; it does not seem to me like the unit test checks whether it is possible to define these types. Let's wait for Barend who, I think, had written both tests to tell us what was the intention.

---

## Comment 3

> Username: barendgehrels  
> Created_at: 2014-07-25 16:04:55 UTC  
> Url: https://github.com/boostorg/geometry/pull/112#issuecomment-50169687  

OK to me, they are left-overs from previous code. It happens more - I don't have these warnings yet.  
They are not part of these test.  
Menelaos, there are currently conflicts, so I cannot yet merge. Can you just remove the lines instead of commenting them? I will merge then. Thanks.

---

## Comment 4

> Username: mkaravel  
> Created_at: 2014-07-26 22:02:25 UTC  
> Url: https://github.com/boostorg/geometry/pull/112#issuecomment-50249889  

@barendgehrels I will take care of this either tomorrow night or Monday morning.  
  
Just one for thing to clarify before creating a new PR for this:  
in `test_simplify.hpp` there is a commented `BOOST_CONCEPT_ASSERT`.  
Is it okay to uncomment this?  
  
If no, I would like to delete this line.  
  
If yes, I plan to delete the line defining `point_type` and replace the call to `BOOST_CONCEPT_ASSERT` by the following:  
  
```  
    BOOST_CONCEPT_ASSERT( (bg::concept::SimplifyStrategy<Strategy,  
                           typename bg::point_type<Geometry>::type>) );  
```  
  
Please let me know what you prefer.  
  
BTW, I already tried the latter and the unit test passes.

---

## Comment 5

> Username: mkaravel  
> Created_at: 2014-07-27 10:56:21 UTC  
> Url: https://github.com/boostorg/geometry/pull/112#issuecomment-50261401  

Closing this PR.  
The fixes suggested by this PR may be found in two new PRs:  
- fix for`test/algorithms/buffer/test_buffer.hpp` in PR #113   
- fix for `test/algorithms/test_simplify.hpp` in PR #114

---

## Comment 6

> Username: barendgehrels  
> Created_at: 2014-07-27 14:16:49 UTC  
> Url: https://github.com/boostorg/geometry/pull/112#issuecomment-50265448  

OK to me (leave the concept check), I merged this one and 3 others, thanks for taking care of it!

---
