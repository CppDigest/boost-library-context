# #849 - Downloads failing for boostorg.jfrog.io, affecting several important packages [Open]

> Username: brianwestphal  
> Created at: 2024-01-08 05:40:45 UTC  
> Updated at: 2025-02-19 17:47:05 UTC  
> Url: https://github.com/boostorg/boost/issues/849  

At least these two that I'm running into:  
- react-native-reanimated: <https://github.com/software-mansion/react-native-reanimated/issues/5542>  
- react-native-mmkv: <https://github.com/mrousavy/react-native-mmkv/issues/617>  
  
Unable to fetch from <https://boostorg.jfrog.io/artifactory/main/release/1.76.0/source/boost_1_76_0.tar.gz>  
  
Was this changed intentionally/unintentionally?

---

## Comment 1

> Username: md-irefer  
> Created at: 2024-01-08 06:55:57 UTC  
> Url: https://github.com/boostorg/boost/issues/849#issuecomment-1880468764  

i got the "Not in GZIP format" in expo-modules-core

---

## Comment 2

> Username: userdocs  
> Created at: 2024-01-08 08:37:13 UTC  
> Url: https://github.com/boostorg/boost/issues/849#issuecomment-1880572145  

The three issues before yours are all specific to this issue. This is essentially just a duplicate of them.  
  
https://github.com/boostorg/boost/issues/845  
https://github.com/boostorg/boost/issues/844  
https://github.com/boostorg/boost/issues/843

---

## Comment 3

> Username: codebycarlos  
> Created at: 2024-01-08 09:44:48 UTC  
> Url: https://github.com/boostorg/boost/issues/849#issuecomment-1880667040  

This is having a significant effect with users unable to compile apps for React Native:  
  
https://github.com/facebook/react-native/issues/42109#issuecomment-1880663873

---

## Comment 4

> Username: cortinico  
> Created at: 2024-01-08 11:43:57 UTC  
> Url: https://github.com/boostorg/boost/issues/849#issuecomment-1880845289  

Hey all,  
Nicola here from the React Native team.  
Here is the official recommendation on how to overcome this failure:  
- https://github.com/facebook/react-native/issues/42180

---

## Comment 5

> Username: sjmudd  
> Created at: 2024-01-08 11:52:00 UTC  
> Url: https://github.com/boostorg/boost/issues/849#issuecomment-1880855703  

Also affects MySQL building: https://bugs.mysql.com/bug.php?id=113576

---

## Comment 6

> Username: Icehunter  
> Created at: 2024-01-08 19:04:40 UTC  
> Url: https://github.com/boostorg/boost/issues/849#issuecomment-1881662422  

@cortinico it affects android as well, as we only ship android, so we went the patch package route as well.

---

## Comment 7

> Username: cortinico  
> Created at: 2024-01-08 19:52:00 UTC  
> Url: https://github.com/boostorg/boost/issues/849#issuecomment-1881726618  

> @cortinico it affects android as well, as we only ship android, so we went the patch package route as well.  
  
Nope it does not affect Android as we distribute a prebuilt and users don't need to re-download boost. If you're seeing a failure, that's caused by one of your dependencies that should not re-download boost

---

## Comment 8

> Username: Icehunter  
> Created at: 2024-01-08 20:34:41 UTC  
> Url: https://github.com/boostorg/boost/issues/849#issuecomment-1881780422  

> > @cortinico it affects android as well, as we only ship android, so we went the patch package route as well.  
>   
> Nope it does not affect Android as we distribute a prebuilt and users don't need to re-download boost. If you're seeing a failure, that's caused by one of your dependencies that should not re-download boost  
  
We use reanimated, but I'm not quit sure what to say. If I patch-package react-native (0.73.1) than building release works.

---

## Comment 9

> Username: hannojg  
> Created at: 2024-01-09 07:25:25 UTC  
> Updated at: 2024-01-09 07:33:16 UTC  
> Url: https://github.com/boostorg/boost/issues/849#issuecomment-1882534566  

@cortinico In case of Expensify (react native partner), they are building react native from source, so they need to download boost. There we also get the `Not in GZIP format` error.  
Is the fix from iOS transferable to android?

---

## Comment 10

> Username: hannojg  
> Created at: 2024-01-09 08:35:02 UTC  
> Url: https://github.com/boostorg/boost/issues/849#issuecomment-1882616836  

Okay, sorry, taking my words back. Seems like the issue is with the reanimated package! Sorry for the ping

---

## Comment 11

> Username: jzhou77  
> Created at: 2025-01-03 18:18:46 UTC  
> Url: https://github.com/boostorg/boost/issues/849#issuecomment-2569637087  

Downloading failed for:  
https://boostorg.jfrog.io/artifactory/main/release/1.78.0/source/boost_1_78_0.tar.bz2  
  
See these error messages on the page:  
```  
Time flies when you’re having fun.  
Your 14-day trial may be over, but you have options!  
Need help choosing the right subscription or additional time on your trial? Just let us know and a DevOps expert will reach out to assist you.  
```

---

## Comment 12

> Username: userdocs  
> Created at: 2025-01-03 18:22:55 UTC  
> Url: https://github.com/boostorg/boost/issues/849#issuecomment-2569641591  

@jzhou77 this is the solution - https://github.com/boostorg/boost/issues/843#issuecomment-2567034014  
  
https://archives.boost.io/release/1.78.0/source/boost_1_78_0.tar.bz2

---

## Comment 13

> Username: jamiecook  
> Created at: 2025-02-13 19:59:14 UTC  
> Url: https://github.com/boostorg/boost/issues/849#issuecomment-2657589640  

Looks like archives.boost.io is the recommendation from the downloads page.  
Vote to close this issue as resolved.

---

## Comment 14

> Username: jmccabe  
> Created at: 2025-02-19 17:47:04 UTC  
> Url: https://github.com/boostorg/boost/issues/849#issuecomment-2669350852  

This page, on the Boost website, needs to be updated or removed. https://www.boost.org/users/news/boost_has_moved_downloads_to_jfr.html
