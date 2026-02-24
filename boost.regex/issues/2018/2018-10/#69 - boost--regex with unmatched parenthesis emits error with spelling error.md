# #69 - boost::regex   with unmatched parenthesis emits error with spelling error [Closed]

> Username: drjasonharrison-vp-eio  
> Created at: 2018-10-12 19:56:14 UTC  
> Updated at: 2018-11-11 16:51:51 UTC  
> Closed at: 2018-11-11 16:51:51 UTC  
> Url: https://github.com/boostorg/regex/issues/69  

I made a typo in a regex:  
  
    boost::regex tty_filter( "^tty\\.usbmodem([[:digit:]]+)$" );  
  
and got this exception:  
  
    libc++abi.dylib: terminating with uncaught exception of type   
    boost::exception_detail::clone_impl<boost::exception_detail::   
    error_info_injector<boost::regex_error> >: Found a closing ) with no corresponding   
    openening parenthesis.  The error occurred while parsing the regular expression fragment: 'digit:]])+>>>HERE>>>)$'.  
  
Note that "opening" is spelled wrong.  
  
There was a pull request to fix this: https://github.com/boostorg/regex/pull/52

---

## Comment 1

> Username: jeking3  
> Created at: 2018-11-11 14:49:30 UTC  
> Url: https://github.com/boostorg/regex/issues/69#issuecomment-437676143  

This can be closed - the PR was merged.
