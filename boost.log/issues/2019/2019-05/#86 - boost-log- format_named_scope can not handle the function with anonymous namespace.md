# #86 - boost/log: format_named_scope can not handle the function with anonymous namespace [Open]

> Username: Arlea1221  
> Created at: 2019-05-24 09:10:14 UTC  
> Updated at: 2019-05-29 17:44:20 UTC  
> Url: https://github.com/boostorg/log/issues/86  

use boost::log::format_named_scope, it can not handle the function with anonymous namespace:  
%n (%f:%l) :  void aa::bb::{anonymous}::test() (/home/joycguan/projects/test/62_log.cpp:443)   
                                --> Right  
%c (%f:%l) :  anonymous}::test (/home/joycguan/projects/test/62_log.cpp:443)   
                                 --> Wrong

---

## Comment 1

> Username: Lastique  
> Created at: 2019-05-29 17:44:20 UTC  
> Url: https://github.com/boostorg/log/issues/86#issuecomment-497039036  

%c and %C placeholders are known to not support all cases well. Do not use them if you want predictable output.
