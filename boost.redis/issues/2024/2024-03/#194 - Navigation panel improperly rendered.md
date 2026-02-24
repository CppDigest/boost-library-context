# #194 - [docs] Navigation panel improperly rendered [Closed]

> Username: Lastique  
> Created at: 2024-03-23 08:06:51 UTC  
> Updated at: 2024-05-09 09:35:06 UTC  
> Closed at: 2024-05-09 09:35:05 UTC  
> Url: https://github.com/boostorg/redis/issues/194  

https://www.boost.org/doc/libs/1_84_0/libs/redis/doc/html/index.html  
  
The navigation panel seems to be overlapping the library title, and the search bar overlaps the panel.  
  
![Screenshot_20240323_110356](https://github.com/boostorg/redis/assets/1589747/9e5f3dcf-080f-4643-bb42-b0fcdf4c9ef8)  
  
Firefox 124.0.1, Kubuntu 22.04.

---

## Comment 1

> Username: Lastique  
> Created at: 2024-03-23 08:20:09 UTC  
> Url: https://github.com/boostorg/redis/issues/194#issuecomment-2016408406  

The same issue is on the Reference page.

---

## Comment 2

> Username: mzimbres  
> Created at: 2024-03-27 19:41:28 UTC  
> Url: https://github.com/boostorg/redis/issues/194#issuecomment-2023828175  

Hi, I am aware of this problem but still don't have a solution. It first appeared when Boost.Redis was integrated into boost and that boost header got automatically added to the docs. @anarthal has also investigated but AFAIR it was not trivial to fix.

---

## Comment 3

> Username: anarthal  
> Created at: 2024-04-02 10:28:04 UTC  
> Url: https://github.com/boostorg/redis/issues/194#issuecomment-2031637255  

AFAIR it is a CSS problem on the Doxygen template you were using. It uses absolute positioning, assuming there is nothing on top of it. When the website adds the header, it breaks.  
  
I thought I had fixed it though, but looks like it broke again. Probably the best way to handle this is preventing the website from including the header - let me investigate on whether this is possible and I'll come back to you.

---

## Comment 4

> Username: Lastique  
> Created at: 2024-04-02 10:41:37 UTC  
> Updated at: 2024-04-02 10:43:16 UTC  
> Url: https://github.com/boostorg/redis/issues/194#issuecomment-2031663784  

I just checked https://www.boost.org/doc/libs/master/libs/redis/doc/html/index.html and indeed it looks fixed. Was the fix made post-1.84 release? If so, you can close this and sorry for the noise. Thanks.

---

## Comment 5

> Username: anarthal  
> Created at: 2024-04-02 17:35:49 UTC  
> Url: https://github.com/boostorg/redis/issues/194#issuecomment-2032657278  

Looking at the commit history, yes, I fixed it after 1.84. This explains what we're seeing.
