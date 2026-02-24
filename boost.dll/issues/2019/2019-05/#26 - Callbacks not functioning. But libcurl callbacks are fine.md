# #26 - Callbacks not functioning. But libcurl callbacks are fine [Closed]

> Username: kenkit  
> Created at: 2019-05-28 20:36:19 UTC  
> Updated at: 2019-06-01 13:38:22 UTC  
> Closed at: 2019-06-01 13:38:21 UTC  
> Url: https://github.com/boostorg/dll/issues/26  

I noticed this when I turned most of my code into a callback based api.  
The problem happens when classes are involved.

---

## Comment 1

> Username: kenkit  
> Created at: 2019-05-28 20:39:24 UTC  
> Updated at: 2019-05-28 20:41:44 UTC  
> Url: https://github.com/boostorg/dll/issues/26#issuecomment-496680465  

This is an example where it failed  
  
```cpp  
  
 ProgressCallback Progress;  
    ...  
  
        result = compressor.DoCompress((SevenZip::ProgressCallback*)&Progress);  
```

---

## Comment 2

> Username: kenkit  
> Created at: 2019-05-28 20:40:39 UTC  
> Url: https://github.com/boostorg/dll/issues/26#issuecomment-496680947  

I've tried a number of ways to get around this but it has not worked.

---

## Comment 3

> Username: kenkit  
> Created at: 2019-05-28 20:46:11 UTC  
> Url: https://github.com/boostorg/dll/issues/26#issuecomment-496682880  

I create my plugins like this https://gist.github.com/xiongjia/50198166b65cb5dab13a339ac9843618

---

## Comment 4

> Username: kenkit  
> Created at: 2019-05-28 20:46:59 UTC  
> Url: https://github.com/boostorg/dll/issues/26#issuecomment-496683160  

This is one of the libraries where it has failed   
https://github.com/getnamo/7zip-cpp

---

## Comment 5

> Username: kenkit  
> Created at: 2019-06-01 13:38:21 UTC  
> Url: https://github.com/boostorg/dll/issues/26#issuecomment-497945949  

Fixed bug was on my side for not declaring the methods as static
