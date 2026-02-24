# #186 - for_each_pixel do not compile with lambdas [Closed]

> Username: sdebionne  
> Created at: 2018-12-12 09:36:40 UTC  
> Updated at: 2018-12-12 12:17:56 UTC  
> Closed at: 2018-12-12 12:17:55 UTC  
> Url: https://github.com/boostorg/gil/issues/186  

### Minimal Working Example (in C++)  
```c++  
#include <boost/gil.hpp>  
  
int main()  
{  
  using namespace boost::gil;  
  for_each_pixel(gray8_view_t(), [](auto pix) { });  
}  
```  
gives:  
  
```  
boost\gil\algorithm.hpp(768): error C2280: 'main::<lambda_3ff3948> &main::<lambda_3ff3948>::operator =(const main::<lambda_3ff3948> &)': attempting to reference a deleted function  
```  
### Actual behavior  
  
Fails to compile because from 5.1.2:  
  
> [19] The closure type associated with a lambda-expression has a deleted (8.4.3) default constructor and **a deleted copy assignment operator**. It has an implicitly-declared copy constructor (12.8) and may have an implicitly declared move constructor (12.8). [ Note: The copy/move constructor is implicitly defined in the same way as any other implicitly declared copy/move constructor would be implicitly defined. —end note ]  
  
### Expected  behavior  
  
Should compile fine.  
  
### Environment  
  
Boost 1.68, tested on MSVC 2017 15.9+

---

## Comment 1

> Username: mloskot  
> Created at: 2018-12-12 10:28:40 UTC  
> Updated at: 2018-12-12 10:36:25 UTC  
> Url: https://github.com/boostorg/gil/issues/186#issuecomment-446538653  

@sdebionne  Could you try Boost 1.69 with #139 fix?  
  
You can also use GIL's current `master` or `develop` branches as both have this fix applied https://github.com/boostorg/gil/commit/f613cc4088e3330e5feba40c68c929da3858cf36#diff-e39436aa17a5c37f907c881288e53a0e

---

## Comment 2

> Username: sdebionne  
> Created at: 2018-12-12 11:16:54 UTC  
> Url: https://github.com/boostorg/gil/issues/186#issuecomment-446552151  

I see, it is unfortunate that the trac issues were not imported in github, probably the reason why I could not find a related issue when I looked up for `for_each_pixel`. Glad to see that this has been fixed and released in 1.69. Thanks for maintaining this library!

---

## Comment 3

> Username: mloskot  
> Created at: 2018-12-12 11:25:52 UTC  
> Url: https://github.com/boostorg/gil/issues/186#issuecomment-446554571  

> it is unfortunate that the trac issues were not imported in github  
> probably the reason why I could not find a related issue when I looked up for for_each_pixel.  
  
Yes, it would be handy.   
  
There have been some efforts to partially do it, see this project:  
https://github.com/boostorg/gil/projects/4  
with aim to manually import all outstanding open issues from Trac, then fix/reject and close them on both, Trac and GitHub with inter-references. The project has been completed, just one minor issue is left open.  
  
So, I think it's best to search GitHub first now.  
  
> Thanks for maintaining this library!  
  
Thank you for using it, and testing!  
  
Please, let me know if 1.69 fixes your issue. Perhaps #187 is no longer necessary (BTW, thanks for effort to fix it).

---

## Comment 4

> Username: sdebionne  
> Created at: 2018-12-12 12:17:55 UTC  
> Url: https://github.com/boostorg/gil/issues/186#issuecomment-446568030  

Yes, it is fixed in 1.69, closing issue and PR.
