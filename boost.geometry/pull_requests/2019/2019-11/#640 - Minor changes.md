# #640 Minor changes [Merged]

> Username: barendgehrels  
> Created at: 2019-11-13 12:00:02 UTC  
> Updated at: 2019-12-02 18:09:17 UTC  
> Merged at: 2019-11-14 21:46:04 UTC  
> Closed at: 2019-11-14 21:46:05 UTC  
> Url: https://github.com/boostorg/geometry/pull/640  

This is no functional change (also because it is beta period)  
  
- a reported issue is added to the testsuite  
- sort-by-side is refactored just a bit to allow calling it from other situations (the calling itself is not ready yet so not included in this PR).  
- Also it updated several tests going right/wrong with different compilerdefine settings  
- Finally it reports the type(s) being tested

---

## Review 1 [Approved]

> Username: vissarion  
> Created_at: 2019-11-14 13:46:52 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/geometry/pull/640#pullrequestreview-316962314  

I am ok with those, thanks!

---

## Comment 2

> Username: awulkiew  
> Created_at: 2019-11-14 15:51:18 UTC  
> Url: https://github.com/boostorg/geometry/pull/640#issuecomment-553949073  

@barendgehrels Do I understand correctly that you consider this a bugfix and would like to release it with 1.72?

---

## Review 3 [Commented]

> Username: awulkiew  
> Created_at: 2019-11-14 15:58:24 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/640#pullrequestreview-317066054  

📁 include/boost/geometry/algorithms/detail/overlay/traversal.hpp

```diff
 108 | 
 127 |-     typedef typename side_compare<target_operation>::type side_compare_type;
 109 |+     typedef typename sort_by_side::side_compare<target_operation>::type side_compare_type;
```

> Username: awulkiew  
> Created_at: 2019-11-14 15:58:23 UTC  
> Updated_at: 2019-11-14 15:58:24 UTC  
> Url: https://github.com/boostorg/geometry/pull/640#discussion_r346397553  

This is not a change request but a question. I'm wondering, since this is the only place where `side_compare` is used, is there a reason why separate struct template was added and not MPL/TT used to define this member type instead? E.g.:  
  
    typedef typename boost::mpl::if_c  
        <  
            boost::is_same<target_operation, operation_union>::value,  
            std::greater<int>,  
            std::less<int>  
        >::type side_compare_type;

> Username: barendgehrels  
> Created_at: 2019-11-14 21:43:16 UTC  
> Url: https://github.com/boostorg/geometry/pull/640#discussion_r346561335  

That would also be possible. This was written years ago when we didn't use is_same so often.  
Besides that, now you will get a compilation error if it is not either union or intersection. With the same, you won't get that. So it might be more error-prone (depending what you want, of course)


---

## Comment 4

> Username: barendgehrels  
> Created_at: 2019-11-14 21:45:54 UTC  
> Url: https://github.com/boostorg/geometry/pull/640#issuecomment-554094711  

>   
>   
> @barendgehrels Do I understand correctly that you consider this a bugfix and would like to release it with 1.72?  
  
No, this does not need to be in 1.72 (but it might). The only functional change is in the distance measure but that is not relevant for 1.72 itself.

---

## Comment 5

> Username: barendgehrels  
> Created_at: 2019-11-14 21:46:39 UTC  
> Url: https://github.com/boostorg/geometry/pull/640#issuecomment-554095017  

It's merged, thanks for your quick reviews

---
