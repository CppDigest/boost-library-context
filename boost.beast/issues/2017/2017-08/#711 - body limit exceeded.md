# #711 - body limit exceeded [Closed]

> Username: maytrue  
> Created at: 2017-08-02 08:40:00 UTC  
> Updated at: 2017-08-02 12:43:20 UTC  
> Closed at: 2017-08-02 08:59:11 UTC  
> Url: https://github.com/boostorg/beast/issues/711  

I send head request with http://78rfgs.com1.z0.glb.clouddn.com/140CC08668649808A5B5EA2E13A07476.png, return 200 OK.  
  
But with http://img.momocdn.com/feedvideo/E5/41/E541B8CA-9C1C-0121-0146-9F4192ED0E7820170419_C.flv, I got "body limit exceeded".  
  
My beast version :  
  
commit 6081d6e4cc0288e837337a4722f1174ff5b2d23d  
Author: Vinnie Falco <vinnie.falco@gmail.com>  
Date:   Mon Jul 31 19:50:51 2017 -0700  
  
    Set version to 97

---

## Comment 1

> Username: maytrue  
> Created at: 2017-08-02 08:59:11 UTC  
> Url: https://github.com/boostorg/beast/issues/711#issuecomment-319612616  

I change   
  
  static  
    std::uint64_t  
    default_body_limit(std::false_type)  
    {  
        // limit for responses  
        return 1 * 1024 * 1024 * 1024; // 1G  
    }

---

## Comment 2

> Username: vinniefalco  
> Created at: 2017-08-02 12:43:19 UTC  
> Url: https://github.com/boostorg/beast/issues/711#issuecomment-319660728  

Or you can call `basic_parser::body_limit` instead of modifying the source code:  
http://vinniefalco.github.io/beast/beast/ref/beast__http__basic_parser/body_limit.html
