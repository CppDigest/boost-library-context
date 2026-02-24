# #70 - observer_ptr<T> [Open]

> Username: bernhardmgruber  
> Created at: 2019-10-05 21:21:38 UTC  
> Updated at: 2019-11-01 21:33:43 UTC  
> Url: https://github.com/boostorg/smart_ptr/issues/70  

There was much disagreement on the future of [`std::observer_ptr<T>`](http://www.open-std.org/jtc1/sc22/wg21/docs/papers/2018/n4786.html#memory.observer.ptr.overview) during the WG21 Cologne meeting this summer ([objection against moving it into the standard](https://wg21.link/p1408r0), but no consensus for dropping it from the library fundamentals TS3). So maybe Boost is the right place for this handy little utility.  
  
What are your thoughts? Would you accept a PR adding `observer_ptr<T>` to Boost.SmartPtr?

---

## Comment 1

> Username: pdimov  
> Created at: 2019-10-06 00:32:47 UTC  
> Url: https://github.com/boostorg/smart_ptr/issues/70#issuecomment-538700758  

Possibly, but I'm not convinced that `observer_ptr` is the right name. :-)

---

## Comment 2

> Username: glenfe  
> Created at: 2019-10-06 01:06:44 UTC  
> Url: https://github.com/boostorg/smart_ptr/issues/70#issuecomment-538702410  

@bernhardmgruber You can propose `observer_ptr` as a new Boost library too. `fixed_string` is going this route instead of going into Boost.Container. The same for `span`.

---

## Comment 3

> Username: bernhardmgruber  
> Created at: 2019-10-06 13:20:57 UTC  
> Updated at: 2019-10-06 13:21:08 UTC  
> Url: https://github.com/boostorg/smart_ptr/issues/70#issuecomment-538746790  

@pdimov The name has been bikesheded to death by WG21 during the years and `observer_ptr<T>` was the best they got. I don't want to reopen the discussion, because I feel less qualified to pick a better name ;)  
@glenfe Would you prefer having `observer_ptr<T>` in its own library? I thought that going the way through an existing library might be easier than sending an entire new one through the review process.

---

## Comment 4

> Username: glenfe  
> Created at: 2019-10-06 13:29:32 UTC  
> Url: https://github.com/boostorg/smart_ptr/issues/70#issuecomment-538747446  

Users have that preference now-a-days, to be able to pull in small independent components without dependencies on other Boost libraries.  
  
The other advantage is that the facility will receive a Boost review. It had a WG21 review, but (speaking as a WG21 and C++ Standard committee member) a Boost review is worth it.

---

## Comment 5

> Username: pdimov  
> Created at: 2019-10-06 13:51:13 UTC  
> Url: https://github.com/boostorg/smart_ptr/issues/70#issuecomment-538749215  

I don't see much to review there. Except the name. That part of the review will be fun. :-)

---

## Comment 6

> Username: pdimov  
> Created at: 2019-10-06 14:02:46 UTC  
> Url: https://github.com/boostorg/smart_ptr/issues/70#issuecomment-538750204  

```  
POLLS   
  
Drop observer_ptr from the TS.   
  
SF  F  N  A  SA    
7  9  7  7  4    
  
Move observer_ptr to Annex D of the standard (deprecated).   
  
SF  F  N  A  SA    
5  6  5  13  6    
  
Move observer_ptr toward the WD, no special policy.   
  
SF  F  N  A  SA    
2  4  4  14  12    
  
Move observer_ptr toward the WD, with LEWG policy to ignore it in the standard.   
  
SF  F  N  A  SA    
4  8  9  5  9    
```  
  
Not very encouraging.

---

## Comment 7

> Username: glenfe  
> Created at: 2019-10-06 14:06:02 UTC  
> Url: https://github.com/boostorg/smart_ptr/issues/70#issuecomment-538750479  

Should be an easy review then. :)

---

## Comment 8

> Username: pdimov  
> Created at: 2019-10-06 14:15:26 UTC  
> Url: https://github.com/boostorg/smart_ptr/issues/70#issuecomment-538751249  

Actually no, I take that back. There are some parts of the design that I'd change. The pointer constructor should be implicit; there shouldn't be a conversion to a raw pointer; and, arguably, `shared_ptr` should be implicitly convertible to `observer_ptr`.

---

## Comment 9

> Username: bernhardmgruber  
> Created at: 2019-11-01 20:42:21 UTC  
> Url: https://github.com/boostorg/smart_ptr/issues/70#issuecomment-548943458  

My colleague @MFHava and me finally found some time to put together a simple implementation with some documentation. Most of it is taken quite directly from the working draft. I talked to Walter E. Brown during the last WG21 meeting in Cologne and he was fine with letting us try to publish his work as a boost library.  
  
The repository is here: https://github.com/MFHava/observer_ptr  
  
How would you recommend to proceed now? I guess there will be a lot to polish before we can start an actual boost review, whatever that means.

---

## Comment 10

> Username: glenfe  
> Created at: 2019-11-01 21:33:43 UTC  
> Url: https://github.com/boostorg/smart_ptr/issues/70#issuecomment-548958567  

Some links:  
* https://www.boost.org/community/reviews.html  
* https://www.boost.org/development/submissions.html
