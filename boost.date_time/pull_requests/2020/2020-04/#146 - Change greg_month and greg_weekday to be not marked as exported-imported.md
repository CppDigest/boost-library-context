# #146 Change greg_month and greg_weekday to be not marked as exported/imported [Merged]

> Username: Lastique  
> Created at: 2020-04-06 10:31:43 UTC  
> Updated at: 2020-04-06 18:22:04 UTC  
> Merged at: 2020-04-06 15:55:37 UTC  
> Closed at: 2020-04-06 15:55:37 UTC  
> Url: https://github.com/boostorg/date_time/pull/146  

Both `greg_month` and `greg_weekday` classes are implemented completely in headers, so they need not be marked with `BOOST_DATE_TIME_DECL`. For consistency with other similar classes, they are now marked with `BOOST_SYMBOL_VISIBLE`.  
  
This should fix [linking errors](https://ci.appveyor.com/project/Lastique/log/build/job/m422rkarjkskc049#L906) on Windows/MSVC, where all members of `greg_month` and `greg_weekday` classes remain unresolved as they are expected to be implemented in a shared library.  
  
This PR also enables both shared and static linking in AppVeyor CI.

---

## Review 1 [Commented]

> Username: JeffGarland  
> Created_at: 2020-04-06 11:25:32 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/date_time/pull/146#pullrequestreview-388157446  

📁 include/boost/date_time/gregorian/greg_month.hpp

```diff
  50 |   //! Wrapper class to represent months in gregorian based calendar
  51 |-   class BOOST_DATE_TIME_DECL greg_month : public greg_month_rep {
  51 |+   class BOOST_SYMBOL_VISIBLE greg_month : public greg_month_rep {
```

> Username: JeffGarland  
> Created_at: 2020-04-06 11:25:25 UTC  
> Updated_at: 2020-04-06 11:25:32 UTC  
> Url: https://github.com/boostorg/date_time/pull/146#discussion_r404017278  

Isn't the other option to just remove the macro at this point?  I see that this is what we have in ptime now so I'm fine with it

> Username: Lastique  
> Created_at: 2020-04-06 11:38:21 UTC  
> Url: https://github.com/boostorg/date_time/pull/146#discussion_r404023842  

Yes, I see no reason to keep the classes visible. But you have all similar classes marked visible, so I figured you had a reason and I made these two classes marked as well for consistency. If there is no reason, they can all be un-marked by a separate PR. I can make one, if you want me to.

> Username: JeffGarland  
> Created_at: 2020-04-06 12:24:35 UTC  
> Url: https://github.com/boostorg/date_time/pull/146#discussion_r404049525  

We can do it post 1.73.  There will be another round of enhancements for 1.74 so I'll just clean it up then.  And now if the CI would get around to running...


---

## Comment 2

> Username: JeffGarland  
> Created_at: 2020-04-06 15:55:41 UTC  
> Url: https://github.com/boostorg/date_time/pull/146#issuecomment-609880633  

I'm going forward without waiting for linux CI since it was windows that was the issue and appveyor has passed.

---
