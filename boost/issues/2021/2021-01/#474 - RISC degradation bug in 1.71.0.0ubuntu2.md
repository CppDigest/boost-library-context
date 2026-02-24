# #474 - RISC degradation bug in 1.71.0.0ubuntu2 [Closed]

> Username: Zetanova  
> Created at: 2021-01-30 13:28:47 UTC  
> Updated at: 2021-01-30 16:46:00 UTC  
> Closed at: 2021-01-30 16:45:59 UTC  
> Url: https://github.com/boostorg/boost/issues/474  

There is a bug in the ubuntu 20.04 docker image after ubuntu:focal-20200115  
The bug is only with arm/v7 docker images  
  
in arm/v7 the date can not be read.   
Result is the year 1970  
  
Can be related to the last commit:  
http://changelogs.ubuntu.com/changelogs/pool/main/b/boost-defaults/boost-defaults_1.71.0.0ubuntu2/changelog  
  
## ubuntu 20.04 arm/v7 (rpi3)  
```  
$ docker run ubuntu:focal-20210119 date  
Sun Feb 22 06:29:15 UTC 1970  
$ docker run ubuntu:focal-20200115 date  
Sat Jan 30 13:24:03 UTC 2021  
$ date  
Sat Jan 30 13:24:15 UTC 2021  
```  
  
## ubuntu 20.04 arm64 (rpi3)  
```  
$ docker run ubuntu:focal-20210119 date  
Sat Jan 30 13:27:28 UTC 2021  
$ docker run ubuntu:focal-20200115 date  
Sat Jan 30 13:28:26 UTC 2021  
$ date  
Sat Jan 30 13:28:38 UTC 2021  
```  
  
Reference Issue:  
https://github.com/bitcoin/bitcoin/issues/21019

---

## Comment 1

> Username: Zetanova  
> Created at: 2021-01-30 16:45:59 UTC  
> Url: https://github.com/boostorg/boost/issues/474#issuecomment-770242056  

Related BUG report:  
https://bugs.launchpad.net/cloud-images/+bug/1896443
