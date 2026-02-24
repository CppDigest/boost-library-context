# #454 - unnecessary boost/bind.hpp deprecation warnings [Open]

> Username: bitbugprime  
> Created at: 2020-12-28 11:32:34 UTC  
> Updated at: 2020-12-28 11:32:34 UTC  
> Url: https://github.com/boostorg/boost/issues/454  

Quite a few boost libraries still "#include <boost/bind.hpp>" and use _1, _2, etc without their boost::placeholders scope prefix. This generates unnecessary deprecation warnings in user code, not to mention boost itself.  
  
They should instead "#include <boost/bind/bind.hpp>" and replace all uses of naked _1, _2, etc with boost::placeholders::_1, boost::placeholders::_2, etc.  
  
Affected libraries:  
algorithm  
bind  
gil  
graph  
iostreams  
log  
msm  
multi_index  
numeric  
property_map  
property_tree  
python  
signals2  
statechart  
test  
thread  
variant  
  
Numerous additional tests and code examples also use <boost/bind.hpp>.
