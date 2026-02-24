# #53 - Unmatched posix_time::time_input_facet format string delimiters are not considered an error [Closed]

> Username: jeking3  
> Created at: 2017-12-21 17:41:35 UTC  
> Updated at: 2018-01-22 23:37:43 UTC  
> Closed at: 2018-01-22 23:37:42 UTC  
> Url: https://github.com/boostorg/date_time/issues/53  

Test case (for testtime_input_facet):  
```  
  {  
      // what if the separator characters between fields do not match the format string?  
      const std::string value = "December 07|27|10.435945  5 2017";   // should be colons, not bars, see format string:  
      boost::posix_time::time_input_facet* facet = new boost::posix_time::time_input_facet("%B %H:%M:%s %e %Y");  
      boost::posix_time::ptime pt;  
      check("expect colon got bar", failure_test(pt, value, facet)); // proves failbit was set  
  }  
```  
  
Currently this test fails, as failbit does not get set.  The code in time_facet.hpp blindly steps over anything that isn't a format token.

---

## Comment 1

> Username: jeking3  
> Created at: 2018-01-22 23:37:42 UTC  
> Url: https://github.com/boostorg/date_time/issues/53#issuecomment-359611790  

Resolving as won't fix; existing behavior has been there for a long time and there are no open requests to change it that I know of.
