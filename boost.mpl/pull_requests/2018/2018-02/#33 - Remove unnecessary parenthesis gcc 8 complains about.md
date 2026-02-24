# #33 Remove unnecessary parenthesis gcc 8 complains about. [Closed]

> Username: Romain-Geissler-1A  
> Created at: 2018-02-19 09:43:13 UTC  
> Updated at: 2018-03-03 23:34:06 UTC  
> Closed at: 2018-03-03 23:34:06 UTC  
> Url: https://github.com/boostorg/mpl/pull/33  

Hi,  
  
I had new warnings when migrating to gcc 8 (which are blocking for anyone using -Werror).  
  
Cheers,  
Romain

---

## Comment 1

> Username: Romain-Geissler-1A  
> Created_at: 2018-02-19 10:11:08 UTC  
> Url: https://github.com/boostorg/mpl/pull/33#issuecomment-366643626  

Here is what I get:  
  
> /remote/mw/combld/delivery/pack18.continuous/components.osp/osp/Boost/18-0-0-0/include/boost/mpl/assert.hpp:188:21: error: unnecessary parentheses in declaration of ‘assert_arg’ [-Werror=parentheses]  
 failed ************ (Pred::************   
                     ^  
/remote/mw/combld/delivery/pack18.continuous/components.osp/osp/Boost/18-0-0-0/include/boost/mpl/assert.hpp:193:21: error: unnecessary parentheses in declaration of ‘assert_not_arg’ [-Werror=parentheses]  
 failed ************ (boost::mpl::not_<Pred>::************

---

## Comment 2

> Username: Romain-Geissler-1A  
> Created_at: 2018-03-03 23:34:03 UTC  
> Url: https://github.com/boostorg/mpl/pull/33#issuecomment-370188475  

Replaced by #34

---
