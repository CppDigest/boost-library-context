# #850 - When I intsall boost for my react-native proj, I got this error. [Closed]

> Username: Hao-yiwen  
> Created at: 2024-01-08 13:25:54 UTC  
> Updated at: 2024-01-09 03:33:07 UTC  
> Closed at: 2024-01-09 03:25:34 UTC  
> Url: https://github.com/boostorg/boost/issues/850  

```  
Installing boost (1.76.0)  
  
[!] Error installing boost  
Verification checksum was incorrect, expected f0397ba6e982c4450f27bf32a2a83292aba035b827a5623a14636ea583318c41, got 5e89103d9b70bba5c91a794126b169cb67654be2051f90cf7c22ba6893ede0ff  
```  
How can i fix this error?

---

## Comment 1

> Username: mishenThakshana  
> Created at: 2024-01-08 15:04:48 UTC  
> Url: https://github.com/boostorg/boost/issues/850#issuecomment-1881186253  

Me too getting this error

---

## Comment 2

> Username: SharpEV  
> Created at: 2024-01-08 15:37:47 UTC  
> Url: https://github.com/boostorg/boost/issues/850#issuecomment-1881248718  

See here: https://github.com/boostorg/boost/issues/843#issuecomment-1872943124

---

## Comment 3

> Username: wheeleruniverse  
> Created at: 2024-01-08 17:03:03 UTC  
> Updated at: 2024-01-08 17:03:27 UTC  
> Url: https://github.com/boostorg/boost/issues/850#issuecomment-1881482861  

Is there anyway to resolve this issue for Expo only projects? The workarounds in #843 don't seem possible for Expo only as I can't access the podspecs.

---

## Comment 4

> Username: mishenThakshana  
> Created at: 2024-01-08 17:20:21 UTC  
> Url: https://github.com/boostorg/boost/issues/850#issuecomment-1881512242  

Yes can somebody provide the solution for expo projects thanks

---

## Comment 5

> Username: mishenThakshana  
> Created at: 2024-01-08 18:37:27 UTC  
> Url: https://github.com/boostorg/boost/issues/850#issuecomment-1881624631  

I could solve this by changing  boost.podspec file in node_modules/react native/third-party-podspecs/boost.podspec,  
in there spec.source use the line below,  
```  
  spec.source = { :http => 'https://sourceforge.net/projects/boost/files/boost/1.76.0/boost_1_76_0.tar.bz2',  
                  :sha256 => 'f0397ba6e982c4450f27bf32a2a83292aba035b827a5623a14636ea583318c41' }  
```  
@wheeleruniverse

---

## Comment 6

> Username: wheeleruniverse  
> Created at: 2024-01-08 20:12:07 UTC  
> Url: https://github.com/boostorg/boost/issues/850#issuecomment-1881752539  

@mishenThakshana thanks you saved me 💕

---

## Comment 7

> Username: Hao-yiwen  
> Created at: 2024-01-09 03:25:31 UTC  
> Url: https://github.com/boostorg/boost/issues/850#issuecomment-1882327892  

https://github.com/boostorg/boost/issues/843#issuecomment-1872943124

---

## Comment 8

> Username: Hao-yiwen  
> Created at: 2024-01-09 03:33:07 UTC  
> Url: https://github.com/boostorg/boost/issues/850#issuecomment-1882343315  

https://github.com/facebook/react-native/issues/42180
