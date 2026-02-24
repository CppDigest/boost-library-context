# #24 - Merge to master [Closed]

> Username: glenfe  
> Created at: 2018-09-17 12:04:11 UTC  
> Updated at: 2018-09-20 12:55:35 UTC  
> Closed at: 2018-09-19 22:08:59 UTC  
> Url: https://github.com/boostorg/pool/issues/24  

Reminder to please merge 37b504b3366c69129566bdbdff0e9f62c8457241 to master before the release.

---

## Comment 1

> Username: jeking3  
> Created at: 2018-09-19 12:05:10 UTC  
> Url: https://github.com/boostorg/pool/issues/24#issuecomment-422776803  

Thanks.  @eldiener and @mjcaisse FYI the boost development calendar is empty for the rest of the year and into next.

---

## Comment 2

> Username: jeking3  
> Created at: 2018-09-19 12:10:32 UTC  
> Url: https://github.com/boostorg/pool/issues/24#issuecomment-422778284  

@glenfe I see a number of error code 134 regressions in the test results on the test matrix for pool/develop in the test that was altered as compared to pool/master results.  I'm not going to merge to master until this is resolved or explained.

---

## Comment 3

> Username: glenfe  
> Created at: 2018-09-19 12:24:04 UTC  
> Updated at: 2018-09-19 12:34:39 UTC  
> Url: https://github.com/boostorg/pool/issues/24#issuecomment-422782195  

@jeking3 What part about those 134 failures is unclear to you? If the error message says:  
```  
main() should return report_errors()  
EXIT STATUS: 134  
```  
Those are failing because the test did not return `report_errors()`. When they are re-run, they should pass. Because the tests now return `report_errors()`. Check the time when those tests were run. It was before the relevant commit that fixed the issue.  
  
Furthermore, check the time when the passing test in master was run. If it was run before Boost.Core changes were merged to master, then that's why they're still passing. When they run next, they will fail. Until the commit above is merged to master.

---

## Comment 4

> Username: mjcaisse  
> Created at: 2018-09-19 21:40:12 UTC  
> Url: https://github.com/boostorg/pool/issues/24#issuecomment-422967894  

I've got this one. I'm also reviewing the threading changes from Peter.

---

## Comment 5

> Username: mjcaisse  
> Created at: 2018-09-19 22:08:59 UTC  
> Url: https://github.com/boostorg/pool/issues/24#issuecomment-422975263  

merged

---

## Comment 6

> Username: jeking3  
> Created at: 2018-09-20 12:38:49 UTC  
> Url: https://github.com/boostorg/pool/issues/24#issuecomment-423167429  

All the test suites in the test matrix went from green to yellow as a result of this change.  Sounds like you have a handle on it.

---

## Comment 7

> Username: glenfe  
> Created at: 2018-09-20 12:55:35 UTC  
> Url: https://github.com/boostorg/pool/issues/24#issuecomment-423172522  

Not as a result of this change. As a result of a change in Boost.Core. Because the pool tests were defective. They will all turn to green - eventually.
