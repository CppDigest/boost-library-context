# #59 - fatal error: 'boost/type_traits.hpp' file not found [Closed]

> Username: Co1lin  
> Created at: 2022-03-24 10:25:11 UTC  
> Updated at: 2022-03-25 02:45:09 UTC  
> Closed at: 2022-03-25 02:45:09 UTC  
> Url: https://github.com/boostorg/sort/issues/59  

Hi! I want to test these sorting algorithms, and I find that the readme says it is a include only lib. However, after `#include "boost/sort/sort.hpp"` in this repo, I got an error:  
  
```  
path/sort/include/boost/sort/spreadsort/spreadsort.hpp:26:10: fatal error: 'boost/type_traits.hpp' file not found  
#include <boost/type_traits.hpp>  
         ^~~~~~~~~~~~~~~~~~~~~~~  
1 error generated.  
```

---

## Comment 1

> Username: spreadsort  
> Created at: 2022-03-25 00:27:17 UTC  
> Url: https://github.com/boostorg/sort/issues/59#issuecomment-1078531989  

On Thu, Mar 24, 2022 at 6:25 AM Colin ***@***.***> wrote:  
  
> Hi! I want to test these sorting algorithms, and I find that the readme  
> says it is a include only lib. However, after #include  
> "boost/sort/sort.hpp" in this repo, I got an error:  
>  
> path/sort/include/boost/sort/spreadsort/spreadsort.hpp:26:10: fatal error: 'boost/type_traits.hpp' file not found  
> #include <boost/type_traits.hpp>  
>          ^~~~~~~~~~~~~~~~~~~~~~~  
> 1 error generated.  
>  
> Hi Colin,  
  
You need to download the entire boost repo (not just sort), and put it in  
your path, and then it should work.  What we meant is you shouldn't have to  
compile anything as long as you downloaded the boost source.  Boost likes  
to have the sublibraries depend on boost includes where possible, which is  
why it works that way.  
  
  
  
>  —  
> Reply to this email directly, view it on GitHub  
> <https://github.com/boostorg/sort/issues/59>, or unsubscribe  
> <https://github.com/notifications/unsubscribe-auth/ABG2HXDGOAHQJZBZJQFIGZ3VBQ7JFANCNFSM5RQV6FJQ>  
> .  
> You are receiving this because you are subscribed to this thread.Message  
> ID: ***@***.***>  
>

---

## Comment 2

> Username: Co1lin  
> Created at: 2022-03-25 02:45:09 UTC  
> Url: https://github.com/boostorg/sort/issues/59#issuecomment-1078599828  

Thanks!
