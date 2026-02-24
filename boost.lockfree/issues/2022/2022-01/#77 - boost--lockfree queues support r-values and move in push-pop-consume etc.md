# #77 - boost::lockfree queues support r-values and move in push/pop/consume etc? [Open]

> Username: huili80  
> Created at: 2022-01-27 18:10:40 UTC  
> Updated at: 2022-04-28 21:14:05 UTC  
> Url: https://github.com/boostorg/lockfree/issues/77  

I can get around it right now by using std::shared_ptr, but would be nice if the push function could accept r-values and move appropriately.  
Similarly the pop & consume functions could move the objects out of the queue?

---

## Comment 1

> Username: jwdevel  
> Created at: 2022-04-28 21:14:04 UTC  
> Url: https://github.com/boostorg/lockfree/issues/77#issuecomment-1112660087  

See also #31 — seems a dupe of this. There doesn't seem to be anything preventing this feature, and there are even PRs for it, but no movement on the issue for a while.
