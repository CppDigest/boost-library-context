# #18 - Coverity 292488: Parse Warning [Closed]

> Username: jeking3  
> Created at: 2018-06-13 18:42:38 UTC  
> Updated at: 2018-06-14 20:20:31 UTC  
> Closed at: 2018-06-14 20:20:31 UTC  
> Url: https://github.com/boostorg/function/issues/18  

https://scan4.coverity.com/reports.htm#v17121/p14875/fileInstanceId=45133314&defectInstanceId=8392322&mergedDefectId=292488  
CID 292488 (#1 of 1): Parse warning (PW.PARAM_SET_BUT_NOT_USED)  
param_set_but_not_used: parameter "g" was set but never used  
```  
void f(boost::function0<void> f, boost::function0<void> g)  
{  
  BOOST_STATIC_ASSERT((boost::is_same<boost::function0<void>, BOOST_TYPEOF(f = g)>::value));  
}  
```  
  
Is this a bug or intentional?

---

## Comment 1

> Username: swatanabe  
> Created at: 2018-06-13 18:50:57 UTC  
> Url: https://github.com/boostorg/function/issues/18#issuecomment-397046445  

AMDG  
  
On 06/13/2018 12:42 PM, James E. King III wrote:  
> CID 292488 (#1 of 1): Parse warning (PW.PARAM_SET_BUT_NOT_USED)  
> param_set_but_not_used: parameter "g" was set but never used  
  
That's not exactly true.  g is used in a unevaluated context.  
This is a false positive.  
  
> ```  
> void f(boost::function0<void> f, boost::function0<void> g)  
> {  
>   BOOST_STATIC_ASSERT((boost::is_same<boost::function0<void>, BOOST_TYPEOF(f = g)>::value));  
> }  
> ```  
>   
> Is this a bug or intentional?  
>   
In Christ,  
Steven Watanabe
