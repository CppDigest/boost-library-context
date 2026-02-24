# #66 - Possible memory leak in filesystem::copy() [Closed]

> Username: trungnq97  
> Created at: 2018-01-02 12:43:51 UTC  
> Updated at: 2018-11-24 16:37:23 UTC  
> Closed at: 2018-11-24 16:37:23 UTC  
> Url: https://github.com/boostorg/filesystem/issues/66  

Hi team,  
Do you have any update on the ticket https://svn.boost.org/trac10/ticket/8007?  
I'm seeing my app increases mem consumption exactly at the time I call filesystem::copy.  
  
Every 12 hours, I copy 16 files each is about 1GB. After the copy process, RSS mem increases and stays flat for the next 12 hours. I expect RSS only increases during the copy process, but then will go back to the level before copying. I'm running Ubuntu 16.04 64bit, boost 1.66.  
  
Thanks,  
Trung  
  
<img width="821" alt="screen shot 2018-01-02 at 12 34 58" src="https://user-images.githubusercontent.com/2920147/34484116-8b278278-efba-11e7-80d3-f21eb01266b6.png">

---

## Comment 1

> Username: Lastique  
> Created at: 2018-11-24 16:37:23 UTC  
> Url: https://github.com/boostorg/filesystem/issues/66#issuecomment-441379731  

I cannot reproduce the leak with the test case given in the ticket 8007 and the current develop (effectively, Boost 1.69) and gcc 8.2 on Kubuntu 18.10. Valgrind shows no leaks.  
  
If you're using a custom memory allocator, it might be caching memory allocated and released by the `copy` algorithm. Check if the pattern persists with the default libc allocator.
