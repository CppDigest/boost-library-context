# #773 - Color table segfault with NaN argument [Closed]

> Username: jzmaddock  
> Created at: 2022-03-10 19:02:26 UTC  
> Updated at: 2022-04-18 16:23:21 UTC  
> Closed at: 2022-04-18 16:23:21 UTC  
> Url: https://github.com/boostorg/math/issues/773  

Just a heads up that accidentally passing a NaN to the color table lookup results in a segfault (vector subscript out of range).
