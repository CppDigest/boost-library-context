# #21 - #include <boost/bind.hpp> is used instead of <boost/bind/bind.hpp> causing deprecation warning [Open]

> Username: vbaderks  
> Created at: 2022-05-23 11:38:41 UTC  
> Updated at: 2022-05-23 11:38:41 UTC  
> Url: https://github.com/boostorg/statechart/issues/21  

The header files: fifo_worker.hpp and processor_container.hpp include <boost/bind.hpp> instead of <boost/bind/bind.hpp>  
  
During complication this causes the following warning:  
  
message : The practice of declaring the Bind placeholders (_1, _2, ...) in the global namespace is deprecated. Please use <boost/bind/bind.hpp> + using namespace boost::placeholders, or define BOOST_BIND_GLOBAL_PLACEHOLDERS to retain the current behavior.  
  
Remark: using <boost/bind/bind.hpp> may be a breaking change for projects that rely implicit on the fact that <boost/bind.hpp> is used at the moment.
