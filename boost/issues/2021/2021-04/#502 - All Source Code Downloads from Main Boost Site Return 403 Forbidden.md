# #502 - All Source Code Downloads from Main Boost Site Return 403 Forbidden [Closed]

> Username: marko-kovacevic  
> Created at: 2021-04-12 16:08:09 UTC  
> Updated at: 2023-05-10 21:54:30 UTC  
> Closed at: 2023-05-10 21:54:30 UTC  
> Url: https://github.com/boostorg/boost/issues/502  

Hello,  
  
Wanted to bring to the attention of the people that manage the Boost website - multiple source code version downloads are returning 403 forbidden error when trying to download.  
  
Example, when I wget https://dl.bintray.com/boostorg/release/1.74.0/source/boost_1_74_0.tar.gz  
  
Or when I visit the following  
https://dl.bintray.com/boostorg/release/1.74.0/source/  
  
Thanks!

---

## Comment 1

> Username: mclow  
> Created at: 2021-04-12 16:33:29 UTC  
> Url: https://github.com/boostorg/boost/issues/502#issuecomment-817954464  

BinTray is closing soon; we're in the process of moving to JFrog Artifactory.  For 1.74.0 releases, you can use https://boostorg.jfrog.io/artifactory/main/release/1.74.0/source/boost_1_74_0.tar.gz

---

## Comment 2

> Username: mclow  
> Created at: 2021-04-12 16:33:57 UTC  
> Url: https://github.com/boostorg/boost/issues/502#issuecomment-817954776  

That should be the stable home; but we're still working on it.

---

## Comment 3

> Username: userdocs  
> Created at: 2021-04-12 18:33:00 UTC  
> Url: https://github.com/boostorg/boost/issues/502#issuecomment-818034828  

Is there a reason you don't use git actions + releases for hosting the archives (not the git source)?

---

## Comment 4

> Username: mclow  
> Created at: 2021-04-12 20:35:48 UTC  
> Url: https://github.com/boostorg/boost/issues/502#issuecomment-818193996  

Yes; because we get *a lot* of downloads. Our bandwidth quota on BinTray kept getting raised until we no longer ran out every month, and it's such a large number that we don't want to just drop that load on a provider w/o discussion.

---

## Comment 5

> Username: userdocs  
> Created at: 2021-04-12 20:50:10 UTC  
> Updated at: 2021-04-12 20:51:49 UTC  
> Url: https://github.com/boostorg/boost/issues/502#issuecomment-818223373  

I am not aware of any specific limitation documented or enforced by Github. I found this for example [https://github.community/t/restrictions-on-released-binaries/749/2](https://github.community/t/restrictions-on-released-binaries/749/2) but I am not sure what applies to a project of your size.  
  
Thanks for the reply. I was just curious to know.

---

## Comment 6

> Username: mclow  
> Created at: 2021-04-12 21:15:24 UTC  
> Url: https://github.com/boostorg/boost/issues/502#issuecomment-818242495  

If we had to pay for the bandwidth used for boost downloads, it would be several thousand $$/month.  We appreciate that JFrog is willing to provide this to us for free.

---

## Comment 7

> Username: userdocs  
> Created at: 2021-04-13 11:50:21 UTC  
> Updated at: 2021-04-13 12:23:30 UTC  
> Url: https://github.com/boostorg/boost/issues/502#issuecomment-818674046  

Ok so I was thinking about this. Looking at stats for the last 30 days:  
  
Bintray   
  
`426,026 `@ https://bintray.com/boostorg/release/boost#statistics  
`2,489` @ https://bintray.com/boostorg/release/boost-binaries#statistics  
  
`428,515` mixed downloads per month.  
  
Hardly any downloads are for binaries here (2489 is 0.58% of 428515)  
  
Sourceforge https://sourceforge.net/projects/boost/files/stats/timeline?dates=2021-03-14+to+2021-04-13  
  
`138,429` mixed downloads per month  
  
The majority of downloads are from the source files and not binaries (11500 is 8.31% of 138429)  
  
Assume some generous averages and define some values:  
  
- 1GB - 1000MB  
- 1TB - 1000GB  
- 150MB per download  
- 150k average downloads per month for Sourceforge. (source + binary)  
- 430000k average downloads per month for bintray (source + binary)  
  
```  
430000 downloads x 150MB = 64,500,000MB / 1000 = 64,500GB / 1000 = 64.5TB per 30 days @ bintray  
```  
  
```  
150000 downloads x 150MB = 22,500,000MB / 1000 = 22500GB / 1000 = 22.5TB per 30 days  @ sourecforge  
```  
  
So that works that Soureforge is currently doing the most work. So by adding Github releases you would not be increasing this load on another provider but simply shifting some of the load to github.  
  
So if the new site jfrog.io follows in this pattern we would see something like this?  
  
- jfrog.io = 65TB per month  
- Soureforge = 15TB per month  
- Github = 15TB per month.  
  
Even if for some weird reason github took the full burden it will be at best 30TB a month. That does not cost a huge amount per month.  
  
If Soureforge can handle it i am sure it github can handle it.

---

## Comment 8

> Username: userdocs  
> Created at: 2021-04-13 12:07:16 UTC  
> Url: https://github.com/boostorg/boost/issues/502#issuecomment-818683369  

I used the wrong figure for bintray (binary only) I have updated and corrected.

---

## Comment 9

> Username: rothn  
> Created at: 2022-01-09 18:55:07 UTC  
> Url: https://github.com/boostorg/boost/issues/502#issuecomment-1008354252  

This is still happening

---

## Comment 10

> Username: mclow  
> Created at: 2022-01-09 19:57:44 UTC  
> Url: https://github.com/boostorg/boost/issues/502#issuecomment-1008363475  

We moved off of binary nine months ago. Please use jFrog instead. The links are on the boost website.

---

## Comment 11

> Username: rayrapetyan  
> Created at: 2022-09-06 02:33:18 UTC  
> Updated at: 2022-09-06 02:33:29 UTC  
> Url: https://github.com/boostorg/boost/issues/502#issuecomment-1237594138  

> BinTray is closing soon; we're in the process of moving to JFrog Artifactory. For 1.74.0 releases, you can use https://boostorg.jfrog.io/artifactory/main/release/1.74.0/source/boost_1_74_0.tar.gz  
  
Could we not use both underscores and dots in the URL? This is really annoying especially for scripts which do not support basic string substitutions (e.g. Dockerfile) to define two similar vars like:   
```  
ARG BOOST_VER_UNDERSCORED=1_74_0  
ARG BOOST_VER_DOTTED=1.74.0  
```

---

## Comment 12

> Username: Killtutor  
> Created at: 2023-05-10 19:08:28 UTC  
> Url: https://github.com/boostorg/boost/issues/502#issuecomment-1542672110  

jfrog keeps forbidden downloads

---

## Comment 13

> Username: sdarwin  
> Created at: 2023-05-10 19:15:45 UTC  
> Url: https://github.com/boostorg/boost/issues/502#issuecomment-1542679171  

@Killtutor jfrog seems to be working.  You are welcome to provide a more detailed bug report.

---

## Comment 14

> Username: Killtutor  
> Created at: 2023-05-10 20:11:38 UTC  
> Url: https://github.com/boostorg/boost/issues/502#issuecomment-1542747198  

<img width="711" alt="image" src="https://github.com/boostorg/boost/assets/20267814/259f21a0-39a6-4317-a950-10fccc981cf4">

---

## Comment 15

> Username: Killtutor  
> Created at: 2023-05-10 20:21:37 UTC  
> Url: https://github.com/boostorg/boost/issues/502#issuecomment-1542761652  

alfin pude descargarlo

---

## Comment 16

> Username: Killtutor  
> Created at: 2023-05-10 20:22:03 UTC  
> Url: https://github.com/boostorg/boost/issues/502#issuecomment-1542762070  

sorry.. Finally.. it finish the download
