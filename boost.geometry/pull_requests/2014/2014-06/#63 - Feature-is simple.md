# #63 Feature/is simple [Merged]

> Username: mkaravel  
> Created at: 2014-06-18 01:50:40 UTC  
> Updated at: 2014-06-18 11:59:33 UTC  
> Merged at: 2014-06-18 11:58:37 UTC  
> Closed at: 2014-06-18 11:58:37 UTC  
> Url: https://github.com/boostorg/geometry/pull/63  

1. fix problem with non-running tests commented by Barend  
2. make is_valid testing more elaborate (for each open areal geometry test the corresponding closed,  
   open with reversed points order and closed with reversed point order)  
3. move implementation for number of distinct values in a range in separate file  
4. re-design is_valid for linestrings, which also fixes a bug (the bug was producable by the test cases  
   recently added in the is_valid unit test)

---

## Comment 1

> Username: barendgehrels  
> Created_at: 2014-06-18 09:07:00 UTC  
> Url: https://github.com/boostorg/geometry/pull/63#issuecomment-46412011  

I made some comments but I'm OK with merging - please mention if you want to have it merged now or later.

---

## Comment 2

> Username: mkaravel  
> Created_at: 2014-06-18 09:11:12 UTC  
> Updated_at: 2014-06-18 11:44:34 UTC  
> Url: https://github.com/boostorg/geometry/pull/63#issuecomment-46412384  

@barendgehrels I will make the changes you suggested.  
  
I would like to do a couple more things before merging. I will let you know when I am ready.  
The things I want to finish before merging are:  
1. Add variant support (almost done).  
2. Use the rescale-to-integer policy when calling self_turns for areal geometries.  
  
The next step (after the merge) is to finish is_valid for multi-polygons.

---

## Comment 3

> Username: barendgehrels  
> Created_at: 2014-06-18 09:13:13 UTC  
> Url: https://github.com/boostorg/geometry/pull/63#issuecomment-46412567  

OK

---

## Comment 4

> Username: mkaravel  
> Created_at: 2014-06-18 09:28:57 UTC  
> Url: https://github.com/boostorg/geometry/pull/63#issuecomment-46413944  

@barendgehrels BTW, even though most of the code for is_valid/is_simple is already merged, you can still comment on it by going to the correspoding pull request (it is PR #59).   
The only problem is that you need to be careful to avoid commenting on lines that have already been changed. The diff you see there is the diff between the develop branch and the feature/is_simple branch at the moment the pull request was merged.

---

## Comment 5

> Username: mkaravel  
> Created_at: 2014-06-18 10:00:50 UTC  
> Url: https://github.com/boostorg/geometry/pull/63#issuecomment-46416816  

The four commits above are related to adding variant support for is_simple and is_valid.

---

## Comment 6

> Username: mkaravel  
> Created_at: 2014-06-18 11:35:57 UTC  
> Url: https://github.com/boostorg/geometry/pull/63#issuecomment-46424343  

In last commit refers to how self turns are called by the polygon validity algorithm: the no_rescale_policy is replaced by the appropriate rescale policy.

---

## Comment 7

> Username: mkaravel  
> Created_at: 2014-06-18 11:41:25 UTC  
> Updated_at: 2014-06-18 11:45:33 UTC  
> Url: https://github.com/boostorg/geometry/pull/63#issuecomment-46424785  

@barendgehrels the PR is ready for merging. I have done the two things I wanted:  
1. add variant support  
2. use the correct robustness policy for polygons  
  
In addition to those, I implemented the new directory/file structure (it was partially there already, but not finished).  
  
I also applied all your comments, except renaming GEOMETRY_TEST_DEBUG to BOOST_GEOMETRY_TEST_DEBUG.  
BTW, the GEOMETRY_TEST_DEBUG was already used in some unit tests as is (for example, see test/algorithms/test_area.hpp) which is where I got the name from. Unless you object I will rename those macros as well.

---

## Comment 8

> Username: barendgehrels  
> Created_at: 2014-06-18 11:59:33 UTC  
> Url: https://github.com/boostorg/geometry/pull/63#issuecomment-46426568  

Thanks!  
Yes, it might well be that I once introduced GEOMETRY_TEST_DEBUG. OK to rename them

---
