# #183 Remove B2 docs ref. [Merged]

> Username: grafikrobot  
> Created at: 2018-04-28 16:02:12 UTC  
> Updated at: 2018-05-03 21:42:16 UTC  
> Merged at: 2018-05-03 21:37:16 UTC  
> Closed at: 2018-05-03 21:37:16 UTC  
> Url: https://github.com/boostorg/boost/pull/183  

B2 docs are now generated outside of boostbook. This also fixes a current error on the release archive builds.

---

## Comment 1

> Username: mclow  
> Created_at: 2018-04-28 18:06:12 UTC  
> Url: https://github.com/boostorg/boost/pull/183#issuecomment-385194994  

``` xinclude tools :  
 -    ../tools/build/doc//jam_docs  
      ../tools/quickbook/doc//quickbook  
      ../tools/boostbook/doc/boostbook.xml  
      ../tools/build/doc//boostdoc  
```  
  
Any idea why some of these have double `//` and others have single?  
Other than that, this looks fine to me.

---

## Comment 2

> Username: swatanabe  
> Created_at: 2018-04-28 18:12:36 UTC  
> Url: https://github.com/boostorg/boost/pull/183#issuecomment-385195424  

AMDG  
  
On 04/28/2018 12:06 PM, Marshall Clow wrote:  
> ``` xinclude tools :  
>  -    ../tools/build/doc//jam_docs  
>       ../tools/quickbook/doc//quickbook  
>       ../tools/boostbook/doc/boostbook.xml  
>       ../tools/build/doc//boostdoc  
> ```  
>   
> Any idea why some of these have double `//` and others have single?  
> Other than that, this looks fine to me.  
>   
  
The part after the `//` is the name of a  
target.  boostbook.xml is a pre-existing file.  
  
In Christ,  
Steven Watanabe

---

## Comment 3

> Username: grafikrobot  
> Created_at: 2018-05-03 21:42:02 UTC  
> Url: https://github.com/boostorg/boost/pull/183#issuecomment-386446344  

Thanks @pdimov for the seemingly infinite free time you have on your hands ;-)

---
