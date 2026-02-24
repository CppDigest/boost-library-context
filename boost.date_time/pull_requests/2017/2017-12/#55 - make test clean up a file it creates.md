# #55 make test clean up a file it creates [Closed]

> Username: jeking3  
> Created at: 2017-12-21 20:44:00 UTC  
> Updated at: 2017-12-23 16:57:23 UTC  
> Closed at: 2017-12-22 21:19:39 UTC  
> Url: https://github.com/boostorg/date_time/pull/55  



---

## Comment 1

> Username: eldiener  
> Created_at: 2017-12-22 13:13:42 UTC  
> Url: https://github.com/boostorg/date_time/pull/55#issuecomment-353596101  

Does date_time depend on filesystem other than your change in the test ? I am concerned that we are adding a dependency to filesystem which was not there before, even if that dependency be only in date_time tests. I am not saying that is necessarily wrong to do

---

## Comment 2

> Username: jeking3  
> Created_at: 2017-12-22 14:59:30 UTC  
> Url: https://github.com/boostorg/date_time/pull/55#issuecomment-353614099  

I'm not aware of any other dependency.  The other way to fix this is to add a .gitignore for the file the test creates but does not clean up, or find another way to get rid of it that is portable.  Thoughts?

---

## Comment 3

> Username: eldiener  
> Created_at: 2017-12-22 21:07:35 UTC  
> Url: https://github.com/boostorg/date_time/pull/55#issuecomment-353674741  

Is there any real testing problem with not deleting the file each time ? If there is not I would rather go the .gitignore route rather than add a testing dependency to filesystem.

---

## Comment 4

> Username: mclow  
> Created_at: 2017-12-22 21:11:31 UTC  
> Url: https://github.com/boostorg/date_time/pull/55#issuecomment-353675203  

Currently it just overwrites any previously existing file when you run the tests, right?  
If that is the case, I'd be ok with the `.gitignore` route. Maybe delete it in the "clean" target.

---

## Comment 5

> Username: jeking3  
> Created_at: 2017-12-22 21:16:42 UTC  
> Url: https://github.com/boostorg/date_time/pull/55#issuecomment-353675884  

It shows up after building when you do a "git status" because the file isn't cleaned up.  
A .gitignore would fix the issue, however the project didn't have a .gitignore to begin with and I was concerned folks would not want one added if there wasn't one.  I'll change this to just add that file to .gitignore.

---

## Comment 6

> Username: swatanabe  
> Created_at: 2017-12-23 16:57:23 UTC  
> Url: https://github.com/boostorg/date_time/pull/55#issuecomment-353736765  

AMDG  
  
On 12/22/2017 02:11 PM, Marshall Clow wrote:  
> Currently it just overwrites any previously existing file when you run the tests, right?  
  
Is this safe for parallel builds?  
  
> If that is the case, I'd be ok with the `.gitignore` route. Maybe delete it in the "clean" target.  
>   
  
In Christ,  
Steven Watanabe

---
