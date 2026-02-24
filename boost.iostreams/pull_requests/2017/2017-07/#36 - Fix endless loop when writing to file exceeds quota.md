# #36 Fix endless loop when writing to file exceeds quota. [Merged]

> Username: rdoeffinger  
> Created at: 2017-07-03 12:38:05 UTC  
> Updated at: 2017-07-04 02:01:59 UTC  
> Merged at: 2017-07-04 02:01:59 UTC  
> Closed at: 2017-07-04 02:01:59 UTC  
> Url: https://github.com/boostorg/iostreams/pull/36  

See also issue 2557.

---

## Comment 1

> Username: eldiener  
> Created_at: 2017-07-03 15:05:39 UTC  
> Url: https://github.com/boostorg/iostreams/pull/36#issuecomment-312670107  

Maybe "quota_full_test.cpp" is a better name than "disk_full_test.cpp" for the test you added ? The latter name might be a bit disturbing to anyone who wants to run the iostream tests and does not want to think that one of them actually fills his hard disk with data so that there is no room left for other files on the hard disk.

---

## Comment 2

> Username: rdoeffinger  
> Created_at: 2017-07-03 16:03:21 UTC  
> Url: https://github.com/boostorg/iostreams/pull/36#issuecomment-312683547  

I agree that the test name is not good, and maybe by what it tests now it should rather be called "write_failure_test" or such?  
I don't really think that replacing "disk" by "quota" will be much comfort to anyone worrying :)  
But if you have a strong preference for a specific name, I'm happy to just pick that.

---

## Comment 3

> Username: eldiener  
> Created_at: 2017-07-03 17:17:15 UTC  
> Url: https://github.com/boostorg/iostreams/pull/36#issuecomment-312697526  

I am fine with anything that does not sugges that the disk drive is going to be filled with data. So changing it to "write_failure_test.cpp" is fine with me.

---

## Comment 4

> Username: rdoeffinger  
> Created_at: 2017-07-03 17:42:31 UTC  
> Url: https://github.com/boostorg/iostreams/pull/36#issuecomment-312702001  

Ok, updated. Btw the test system seems to have a pretty annoying bug: when aborting a test run with ctrl+c, none of the test binaries are actually aborted. So if you have a hanging test you have to go an manually kill it (after wondering why the computer got so slow)...

---

## Comment 5

> Username: eldiener  
> Created_at: 2017-07-03 21:52:50 UTC  
> Url: https://github.com/boostorg/iostreams/pull/36#issuecomment-312739763  

The test system is a Boost Build issue. Please bring it up there if you like.

---
