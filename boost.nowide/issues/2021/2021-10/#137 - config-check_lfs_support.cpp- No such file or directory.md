# #137 - config/check_lfs_support.cpp: No such file or directory [Closed]

> Username: Mailaender  
> Created at: 2021-10-25 09:41:52 UTC  
> Updated at: 2021-10-31 15:25:29 UTC  
> Closed at: 2021-10-31 15:25:29 UTC  
> Url: https://github.com/boostorg/nowide/issues/137  

11.1.2 fails to build for me:  
  
```  
CMake Error at CMakeLists.txt:55 (file):  
   file failed to open for reading (No such file or directory):  
   
     /home/abuild/rpmbuild/BUILD/nowide_v11.1.2/config/check_lfs_support.cpp  
```

---

## Comment 1

> Username: Flamefire  
> Created at: 2021-10-25 14:54:21 UTC  
> Url: https://github.com/boostorg/nowide/issues/137#issuecomment-951011130  

Well that file is missing seemingly missing from your filesystem. Why is that? It is included in the sources (git) and the release archive(s). So Seemingly you did something wrong when downloading or extracting the sources. What exactly did you do? Can you double check that?

---

## Comment 2

> Username: Mailaender  
> Created at: 2021-10-25 15:15:01 UTC  
> Url: https://github.com/boostorg/nowide/issues/137#issuecomment-951031556  

I downloaded https://github.com/boostorg/nowide/releases/download/v11.1.2/nowide_v11.1.2.tar.gz which doesn't contain the folder.
