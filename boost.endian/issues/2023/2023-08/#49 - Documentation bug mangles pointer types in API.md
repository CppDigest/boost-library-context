# #49 - Documentation bug mangles pointer types in API [Closed]

> Username: ednolan  
> Created at: 2023-08-16 21:11:58 UTC  
> Updated at: 2023-10-19 22:58:23 UTC  
> Closed at: 2023-10-19 22:58:23 UTC  
> Url: https://github.com/boostorg/endian/issues/49  

Screenshot from Firefox 116 on macOS:  
<img width="1094" alt="Screen Shot 2023-08-16 at 5 10 31 PM" src="https://github.com/boostorg/endian/assets/907967/078cbe85-55c6-47e7-8ed2-5f50326bb4ba">  
Note that `unsigned char const* p` is getting rendered as `unsigned char const  p` and the asterisk is getting eaten by some markup generator that's using it to vary the boldness.
