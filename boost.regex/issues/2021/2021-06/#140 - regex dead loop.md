# #140 - regex dead loop [Closed]

> Username: SMSEP  
> Created at: 2021-06-16 11:06:49 UTC  
> Updated at: 2021-06-26 17:26:07 UTC  
> Closed at: 2021-06-26 17:26:07 UTC  
> Url: https://github.com/boostorg/regex/issues/140  

I ran into a freeze when using regular expressions.  
this is my program：  
  
#include \<cstdio\>  
#include \<iostream\>  
using namespace std;  
  
#include \<boost/regex.hpp\>  
  
int main()  
{  
       boost::regex e(string("1?+(?#)1"));  
       return 0;  
}

---

## Comment 1

> Username: SMSEP  
> Created at: 2021-06-22 02:56:15 UTC  
> Url: https://github.com/boostorg/regex/issues/140#issuecomment-865488550  

@jzmaddock    
hi, please check it

---

## Comment 2

> Username: jzmaddock  
> Created at: 2021-06-26 12:53:10 UTC  
> Url: https://github.com/boostorg/regex/issues/140#issuecomment-868996968  

Confirmed.
