# #145 - Rotating log files -- the wrong files are being deleted [Open]

> Username: matthiasklein  
> Created at: 2021-03-20 10:47:13 UTC  
> Updated at: 2021-03-20 17:16:40 UTC  
> Url: https://github.com/boostorg/log/issues/145  

When I run the following code https://paste.debian.net/1190133/ the second time, the wrong 00008.log file is deleted.  
  
After run 1: (I copied the folder ...)  
```  
$ ls -ort logs-first-run/  
-rw-r--r-- 1 mak 16363 20. Mär 11:33 file_00001.log  
-rw-r--r-- 1 mak 16359 20. Mär 11:33 file_00002.log  
-rw-r--r-- 1 mak 16359 20. Mär 11:33 file_00003.log  
-rw-r--r-- 1 mak 16335 20. Mär 11:33 file_00004.log  
-rw-r--r-- 1 mak 16356 20. Mär 11:33 file_00005.log  
-rw-r--r-- 1 mak 16356 20. Mär 11:33 file_00006.log  
-rw-r--r-- 1 mak 16356 20. Mär 11:33 file_00007.log  
-rw-r--r-- 1 mak 16356 20. Mär 11:33 file_00008.log  
```  
After run 2:  
```  
$ ls -ort logs/  
-rw-r--r-- 1 mak 16356 20. Mär 11:33 file_00007.log  
-rw-r--r-- 1 mak 16344 20. Mär 11:34 file_00010.log  
-rw-r--r-- 1 mak 16375 20. Mär 11:34 file_00011.log  
-rw-r--r-- 1 mak 16359 20. Mär 11:34 file_00012.log  
-rw-r--r-- 1 mak 16359 20. Mär 11:34 file_00013.log  
-rw-r--r-- 1 mak 16335 20. Mär 11:34 file_00014.log  
-rw-r--r-- 1 mak 16356 20. Mär 11:34 file_00015.log  
-rw-r--r-- 1 mak 16356 20. Mär 11:34 file_00016.log  
-rw-r--r-- 1 mak 16356 20. Mär 11:34 file_00017.log  
-rw-r--r-- 1 mak 16356 20. Mär 11:34 file_00018.log  
```  
I am using Boost 1.74.0 from Debian bullseye.

---

## Comment 1

> Username: Lastique  
> Created at: 2021-03-20 17:16:40 UTC  
> Url: https://github.com/boostorg/log/issues/145#issuecomment-803428754  

I believe, this is caused by a Boost.Filesystem limitation. The last write timestamps of a file provided by Boost.Filesystem only have granularity of one second, so often all rotated files have the same timestamp. In this situation, as no file is older than the other, Boost.Log removes files in the order they are returned by the filesystem, which is basically undefined.  
  
Adding support for more fine grained timestamps in Boost.Filesystem has been in my TODO list for a while, but it won't be easy and it will be an API change. After this is done, the issue should resolve in Boost.Log.
