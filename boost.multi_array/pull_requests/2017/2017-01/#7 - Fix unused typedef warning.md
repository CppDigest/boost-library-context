# #7 Fix unused typedef warning [Closed]

> Username: thk686  
> Created at: 2017-01-26 18:10:15 UTC  
> Updated at: 2019-02-18 21:49:58 UTC  
> Closed at: 2019-02-18 21:49:58 UTC  
> Url: https://github.com/boostorg/multi_array/pull/7  



---

## Review 1 [Changes requested]

> Username: glenfe  
> Created_at: 2018-09-09 01:04:29 UTC  
> State: CHANGES_REQUESTED  
> Url: https://github.com/boostorg/multi_array/pull/7#pullrequestreview-153568480  

This is wrong. `index` and `index_range` are used below in that same type:  
```  
index id;  
const index* idp;  
```  
You can't just remove the type definitions.

---

## Comment 2

> Username: thk686  
> Created_at: 2018-09-09 17:20:22 UTC  
> Url: https://github.com/boostorg/multi_array/pull/7#issuecomment-419730854  

Odd the compiler was complaining. Why not just use Array::index and avoid the typedefs? Its cleaner code.

---

## Comment 3

> Username: glenfe  
> Created_at: 2018-10-02 10:42:57 UTC  
> Url: https://github.com/boostorg/multi_array/pull/7#issuecomment-426228661  

If you want to submit an updated pull request which does result in no warnings on your particular implementation, that's a different matter, and you should feel encouraged to do so. The current pull request is clearly wrong, though.

---

## Comment 4

> Username: glenfe  
> Created_at: 2019-02-18 21:49:58 UTC  
> Url: https://github.com/boostorg/multi_array/pull/7#issuecomment-464892815  

Closing as this current PR doesn't make sense and hasn't been updated. Removing type alias that is used just a few lines below would break compilation. Please feel free to update this thread or submit a new PR if needed.

---
