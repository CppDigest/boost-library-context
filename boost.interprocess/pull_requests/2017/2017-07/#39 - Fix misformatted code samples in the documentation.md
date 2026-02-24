# #39 Fix misformatted code samples in the documentation [Merged]

> Username: ldionne  
> Created at: 2017-07-03 23:19:26 UTC  
> Updated at: 2017-07-06 12:33:13 UTC  
> Merged at: 2017-07-06 12:19:29 UTC  
> Closed at: 2017-07-06 12:19:29 UTC  
> Url: https://github.com/boostorg/interprocess/pull/39  

The current examples look strange since the C++-style comments result in the rest of the line being completely commented-out:  
  
<img width="1246" alt="screen shot 2017-07-03 at 16 16 51" src="https://user-images.githubusercontent.com/700834/27810124-5afc334e-600b-11e7-82c3-2df2c67a4ce6.png">  
  
  
This commit changes the way those parameters are documented to make them consistent with the rest of the documentation.  
  
Note that I was unable to test this change because I wasn't able to generate the Boost documentation locally.

---

## Comment 1

> Username: igaztanaga  
> Created_at: 2017-07-06 12:33:13 UTC  
> Url: https://github.com/boostorg/interprocess/pull/39#issuecomment-313382873  

Thanks for the patch!

---
