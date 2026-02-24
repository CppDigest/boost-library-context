# #74 Correct handling of status() for reparse point when it is not a symlink. [Closed]

> Username: stima  
> Created at: 2018-06-08 16:27:52 UTC  
> Updated at: 2019-02-06 17:13:21 UTC  
> Closed at: 2019-02-06 17:13:21 UTC  
> Url: https://github.com/boostorg/filesystem/pull/74  

When the path is Windows Reparse Point and it is not a symlink return status as reparse_file, otherwise resolve symlink and return status as regural_file with attributes of resolved file.

---

## Comment 1

> Username: stima  
> Created_at: 2018-07-12 08:53:10 UTC  
> Url: https://github.com/boostorg/filesystem/pull/74#issuecomment-404439974  

Hello guys, is the any changes to have it reviewed and committed?

---

## Comment 2

> Username: stima  
> Created_at: 2018-08-08 18:49:04 UTC  
> Url: https://github.com/boostorg/filesystem/pull/74#issuecomment-411512434  

Hi guys, any updates?

---

## Comment 3

> Username: Flamefire  
> Created_at: 2019-02-04 16:49:20 UTC  
> Url: https://github.com/boostorg/filesystem/pull/74#issuecomment-460322116  

As I'm working on something related: What exactly is the problem you fixed here? Do you have a testcase that fails? Can you add that to the tests?

---

## Comment 4

> Username: stima  
> Created_at: 2019-02-06 11:31:21 UTC  
> Url: https://github.com/boostorg/filesystem/pull/74#issuecomment-460991327  

Hi. I will create test soon.

---
