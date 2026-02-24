# #513 Fix bootstrap arguments [Closed]

> Username: andev0  
> Created at: 2021-05-09 18:58:24 UTC  
> Updated at: 2023-01-13 12:41:41 UTC  
> Closed at: 2023-01-13 12:41:41 UTC  
> Url: https://github.com/boostorg/boost/pull/513  

Now user can specify compiler just by typing "bootstrap.bat gcc" or something that match to his compiler. First command-line argument will be passed to build.bat.

---

## Comment 1

> Username: amyspark  
> Created_at: 2022-11-27 01:03:50 UTC  
> Url: https://github.com/boostorg/boost/pull/513#issuecomment-1328144466  

@mclow There's a merge window active, what can we at Krita do to see this merged? (mine was a duplicate).  
  
Also see #682 which is a triplicate.

---

## Comment 2

> Username: swatanabe  
> Created_at: 2022-11-29 03:46:16 UTC  
> Url: https://github.com/boostorg/boost/pull/513#issuecomment-1330035982  

Further digging shows that this was inadvertently broken in #371 and   
fixed in #494 which has been merged into develop but not master for a while.

---

## Comment 3

> Username: mclow  
> Created_at: 2022-11-29 04:26:41 UTC  
> Updated_at: 2022-11-29 04:27:01 UTC  
> Url: https://github.com/boostorg/boost/pull/513#issuecomment-1330061427  

I have reached out to Glen (who landed #494) to see why it was never merged.

---

## Comment 4

> Username: Flamefire  
> Created_at: 2022-12-16 16:23:45 UTC  
> Url: https://github.com/boostorg/boost/pull/513#issuecomment-1355168459  

#494 is in master now

---
