# #177 Fixing lost notification bugs, part 1 (again) [Merged]

> Username: austin-beer  
> Created at: 2017-10-11 16:17:05 UTC  
> Updated at: 2017-10-11 20:57:42 UTC  
> Merged at: 2017-10-11 20:08:25 UTC  
> Closed at: 2017-10-11 20:08:25 UTC  
> Url: https://github.com/boostorg/thread/pull/177  

- Updated all of the functions in all of the condition variable classes to ensure that they don't lose notifications  
- Re-enabled notification tests  
  
Instead of attempting to merge the existing PR, I rebased to the latest feature/timespec_clocks and then created this new PR. Hopefully that's simpler for you.  
  
Don't worry about being busy until the 16th. I'm busy as well. Thanks for reviewing all of these changes!

---

## Comment 1

> Username: viboes  
> Created_at: 2017-10-11 19:47:31 UTC  
> Url: https://github.com/boostorg/thread/pull/177#issuecomment-335926795  

I don't know why AppVeyor was not launched.

---
