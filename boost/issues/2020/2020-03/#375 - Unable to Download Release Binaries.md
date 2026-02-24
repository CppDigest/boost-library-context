# #375 - Unable to Download Release Binaries [Closed]

> Username: san4d  
> Created at: 2020-03-01 00:46:49 UTC  
> Updated at: 2020-03-01 09:40:44 UTC  
> Closed at: 2020-03-01 01:11:00 UTC  
> Url: https://github.com/boostorg/boost/issues/375  

When I try to download any of the links from https://dl.bintray.com/boostorg/release/1.72.0/source/, I see a "Forbidden!" message. I also see this for 1.70.  
  
Steps to reproduce:  
```  
$ curl -O https://dl.bintray.com/boostorg/release/1.72.0/source/boost_1_72_0.tar.bz2  
$ cat boost_1_72_0.tar.bz2  
```

---

## Comment 1

> Username: jabaa  
> Created at: 2020-03-01 00:54:01 UTC  
> Url: https://github.com/boostorg/boost/issues/375#issuecomment-593024574  

I think it's a duplicate of https://github.com/boostorg/boost/issues/299. You can use https://sourceforge.net/projects/boost/files/boost/1.72.0/boost_1_72_0.tar.bz2 as a temporary solution

---

## Comment 2

> Username: san4d  
> Created at: 2020-03-01 01:10:59 UTC  
> Url: https://github.com/boostorg/boost/issues/375#issuecomment-593025536  

Thanks. I didn't see that issue. I'm able to download form the site.

---

## Comment 3

> Username: kszucs  
> Created at: 2020-03-01 09:40:32 UTC  
> Updated at: 2020-03-01 09:40:44 UTC  
> Url: https://github.com/boostorg/boost/issues/375#issuecomment-593076141  

@san4d I'm experiencing the same, so the issue is valid:  
  
```bash  
✖ wget https://dl.bintray.com/boostorg/release/1.72.0/source/boost_1_72_0.tar.bz2  
--2020-03-01 10:39:53--  https://dl.bintray.com/boostorg/release/1.72.0/source/boost_1_72_0.tar.bz2  
Resolving dl.bintray.com (dl.bintray.com)... 52.28.187.224, 52.29.129.139  
Connecting to dl.bintray.com (dl.bintray.com)|52.28.187.224|:443... connected.  
HTTP request sent, awaiting response... 403 Forbidden  
2020-03-01 10:39:54 ERROR 403: Forbidden.  
```
