# #138 - Segfault when calling new on undefined word [Closed]

> Username: refi64  
> Created at: 2016-10-10 20:03:14 UTC  
> Updated at: 2018-01-09 23:40:56 UTC  
> Closed at: 2018-01-09 23:40:56 UTC  
> Url: https://github.com/boostorg/build/issues/138  

This will segfault:  
  
```  
import "class" : new ;  
segfault = [ new xx ] ;  
```  
  
I dug through the source of `class.new` and have reduced the segfault to the following:  
  
```  
local id = foo ;  
INSTANCE $(id) : bar ;  
$(id).something ;  
```  
  
**Original, MUCH longer test case**  
  
In `bug.jam`:  
  
```  
class Bug { }  
```  
  
In `Jamroot`:  
  
```  
import bug ;  
import "class" : new ;  
  
segfault = [ new bug.Bug ] ; # Commenting this line out fixes the segfault.  
```  
  
Tested with both 2014 release **and** latest Git.

---

## Comment 1

> Username: frenchtoast747  
> Created at: 2016-10-10 20:19:25 UTC  
> Url: https://github.com/boostorg/build/issues/138#issuecomment-252734975  

If my memory serves me correctly, when creating an instance of a class in Jam, you don't need the module prefix. Try doing this instead:  
  
```  
import bug ;  
import "class" : new ;  
  
instance = [ new Bug ] ;  
```

---

## Comment 2

> Username: swatanabe  
> Created at: 2016-10-10 20:27:21 UTC  
> Url: https://github.com/boostorg/build/issues/138#issuecomment-252736744  

AMDG  
  
On 10/10/2016 02:19 PM, Aaron Boman wrote:  
  
> If my memory serves me correctly, when creating an instance of a class in Jam, you don't need the module prefix. Try doing this instead:  
>   
> ```  
> import bug ;  
> import "class" : new ;  
>   
> instance = [ new Bug ] ;  
> ```  
  
  Right, class names are global and should  
not be scoped.  The fact that it segfaults  
is a bug, however.  
  
In Christ,  
Steven Watanabe

---

## Comment 3

> Username: swatanabe  
> Created at: 2018-01-09 23:40:56 UTC  
> Url: https://github.com/boostorg/build/issues/138#issuecomment-356450520  

This should be fixed by 6c3397fac8300afe55be4007faea932771c85199
