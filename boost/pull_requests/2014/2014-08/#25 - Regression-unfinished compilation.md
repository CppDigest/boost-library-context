# #25 Regression/unfinished compilation [Closed]

> Username: awulkiew  
> Created at: 2014-08-12 15:43:09 UTC  
> Updated at: 2018-01-28 01:37:35 UTC  
> Closed at: 2015-05-12 18:59:13 UTC  
> Url: https://github.com/boostorg/boost/pull/25  

This PR changes the way how "unfinished compilation" is displayed on the library results summary page for ~~developer~~ output.  
EDIT: it's displayed in both modes - user and developer.  
  
The unfinished compilation is recognized based on the existance of 'File too big' or 'time limit exceeded' ~~at the end~~ of a compilation message.  
EDIT: The info may be placed somewhere in the middle of the output. Therefore the whole compilation message is checked if there is a compilation failure.  
  
It's displayed as a yellow cell with a link named 'fail?' to avoid messing with the CSS.

---

## Comment 1

> Username: awulkiew  
> Created_at: 2014-08-14 15:55:47 UTC  
> Url: https://github.com/boostorg/boost/pull/25#issuecomment-52202532  

The above commit changes the way how new, unexpected failures are displayed on the page presenting the results for a library. The type of failure is marked as follows (along with the previous change):  
- "fail?" - "File too big" or "time limit exceeded"  
- "fail(c)" - compilation failure  
- "fail(l)" - linking failure  
- "fail(r)" - run failure

---

## Comment 2

> Username: awulkiew  
> Created_at: 2015-05-12 18:59:13 UTC  
> Url: https://github.com/boostorg/boost/pull/25#issuecomment-101388084  

It was done after the creation of the separate Regression module.  
https://github.com/boostorg/regression/pull/12  
https://github.com/boostorg/regression/pull/13

---
