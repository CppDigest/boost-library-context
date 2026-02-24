# #186 - copy_file is around 6 times slower than it was in 1.70 [Closed]

> Username: barendgehrels  
> Created at: 2021-04-30 11:53:28 UTC  
> Updated at: 2021-05-18 08:58:02 UTC  
> Closed at: 2021-05-17 17:34:38 UTC  
> Url: https://github.com/boostorg/filesystem/issues/186  

Upgrading Boost from 1.70 to 1.75 we discovered that the performance of copy_file is quite decreased.  
  
Especially if you copy many small files this is noticeable.  
  
Here are my measurements (3 runs of both versions):  
```  
107500 - copy /home/gehrels/current_boost/boost time 42933 ms  
107500 - copy /home/gehrels/current_boost/boost time 43256 ms  
107500 - copy /home/gehrels/current_boost/boost time 42684 ms  
  
107000 - copy /home/gehrels/current_boost/boost time 6646 ms  
107000 - copy /home/gehrels/current_boost/boost time 6844 ms  
107000 - copy /home/gehrels/current_boost/boost time 6574 ms  
```  
(I clean the file cache before each run using `echo 3 | sudo tee /proc/sys/vm/drop_caches`)  
  
Attached the minimal example, creating these measurements, copying the "boost" folder from Boost (15344 files on my system)  
  
[use_boost_fs.cpp.zip](https://github.com/boostorg/filesystem/files/6405089/use_boost_fs.cpp.zip)

---

## Comment 1

> Username: Lastique  
> Created at: 2021-04-30 12:36:28 UTC  
> Url: https://github.com/boostorg/filesystem/issues/186#issuecomment-830064124  

What OS are you running? If Linux, what is your kernel version?

---

## Comment 2

> Username: barendgehrels  
> Created at: 2021-04-30 12:44:08 UTC  
> Updated at: 2021-04-30 12:44:51 UTC  
> Url: https://github.com/boostorg/filesystem/issues/186#issuecomment-830068735  

I'm running on `Linux nl1lxl-110718 4.15.0-142-generic #146-Ubuntu SMP Tue Apr 13 01:11:19 UTC 2021 x86_64 x86_64 x86_64 GNU/Linux`  
  
But the problem also occurs on computers of my colleagues and for our test suites.  
  
And, another observation, if I comment these lines in `operations.cpp`:  
```  
//#if defined(BOOST_FILESYSTEM_HAS_FDATASYNC)  
//  err = ::fdatasync(outfile.fd);  
//#else  
//  err = ::fsync(outfile.fd);  
//#endif  
```  
  
the degradation is gone. So apparently it's the syncing taking time.

---

## Comment 3

> Username: Lastique  
> Created at: 2021-04-30 12:52:26 UTC  
> Url: https://github.com/boostorg/filesystem/issues/186#issuecomment-830073491  

It is expected then. The comment above that call explains why syncing is performed.

---

## Comment 4

> Username: barendgehrels  
> Created at: 2021-04-30 13:11:43 UTC  
> Url: https://github.com/boostorg/filesystem/issues/186#issuecomment-830084922  

Sure, I read it. But the std library (C++17) doesn't do this. Shouldn't they be compatible?  
  
I think that the syncing, if any, should be optional. At any rate, there should not be a regression of 6 times which cannot be avoided somehow.

---

## Comment 5

> Username: barendgehrels  
> Created at: 2021-05-18 08:58:02 UTC  
> Url: https://github.com/boostorg/filesystem/issues/186#issuecomment-842989694  

Thanks for the fix!
