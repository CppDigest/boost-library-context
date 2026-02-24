# #625 Deleted deprecated.hpp [Closed]

> Username: YashK773  
> Created at: 2021-09-02 20:05:58 UTC  
> Updated at: 2022-04-06 09:34:43 UTC  
> Closed at: 2022-04-06 09:34:42 UTC  
> Url: https://github.com/boostorg/gil/pull/625  

deprecated.hpp file was successfully deleted.

---

## Comment 1

> Username: sdebionne  
> Created_at: 2021-09-03 12:56:07 UTC  
> Url: https://github.com/boostorg/gil/pull/625#issuecomment-912517813  

That was the easy part, now you need to find out where the file is still included. Check the build log in the pipeline and look for errors like the following:  
  
```  
/home/vsts/work/1/s/boost-root/libs/gil/include/boost/gil.hpp:22:36: fatal error: boost/gil/deprecated.hpp: No such file or directory  
 #include <boost/gil/deprecated.hpp>  
```

---

## Comment 2

> Username: YashK773  
> Created_at: 2021-09-03 19:13:26 UTC  
> Url: https://github.com/boostorg/gil/pull/625#issuecomment-912754298  

> > That was the easy part, now you need to find out where the file is still included. Check the build log in the pipeline and look for errors like the following:  
> > ```  
> > /home/vsts/work/1/s/boost-root/libs/gil/include/boost/gil.hpp:22:36: fatal error: boost/gil/deprecated.hpp: No such file or directory  
> >  #include <boost/gil/deprecated.hpp>  
> > ```  
  
Ok, got it thank you for the help. I am just a beginner trying the learn to contribute so please don't mind. Also, I closed the PR by mistake, so I am just reopening it.

---

## Comment 3

> Username: lpranam  
> Created_at: 2022-04-06 09:34:42 UTC  
> Url: https://github.com/boostorg/gil/pull/625#issuecomment-1090061882  

closed in favour of #627

---
