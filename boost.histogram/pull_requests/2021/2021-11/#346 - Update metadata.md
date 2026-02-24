# #346 Update metadata [Merged]

> Username: sdarwin  
> Created at: 2021-11-05 18:02:31 UTC  
> Updated at: 2021-11-06 05:57:31 UTC  
> Merged at: 2021-11-06 05:57:31 UTC  
> Closed at: 2021-11-06 05:57:31 UTC  
> Url: https://github.com/boostorg/histogram/pull/346  

In the metadata, the name field is generally being used verbatim to display the library name, even including capitalization.   Compare to other boost libraries...

---

## Comment 1

> Username: henryiii  
> Created_at: 2021-11-06 02:47:52 UTC  
> Url: https://github.com/boostorg/histogram/pull/346#issuecomment-962379523  

macos-latest now points at macOS 11; I think that’s why this is failing. There’s likely a bit removed from the image; a brew install gsl or something is likely needed. On vacation so can’t check further right now. If @HDembinski can’t get to it this weekend I can help early next week.

---
