# #658 - Wrong documentation? [Open]

> Username: asfarley  
> Created at: 2022-06-08 22:45:15 UTC  
> Updated at: 2022-06-23 15:28:21 UTC  
> Url: https://github.com/boostorg/boost/issues/658  

Hi,  
  
I am following the guide here:  
https://www.boost.org/doc/libs/1_79_0/more/getting_started/windows.html  
  
And it seems that after unpacking boost_1_79_0.7z, there is no /lib subdirectory appearing under the boost_1_79_0/ folder.   
  
I'm aware that building may generate the lib subdirectory, but if you read the instructions from the top, it's saying that the subdirectory should exist immediately after unpacking the download. This is a bit confusing.

---

## Comment 1

> Username: IrisPeter  
> Created at: 2022-06-23 15:04:41 UTC  
> Updated at: 2022-06-23 15:28:21 UTC  
> Url: https://github.com/boostorg/boost/issues/658#issuecomment-1164525444  

Hi @asfarley   
  
It seems to happen when using the stage target.  I usually set  
  
`b2 toolset=msvc-14.2 --build-type=complete. --stagedir=. stage` when I build boost  
  
Running directly under the boost_1_79_0 folder
