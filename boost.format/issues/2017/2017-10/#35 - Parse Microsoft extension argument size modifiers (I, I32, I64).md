# #35 - Parse Microsoft extension argument size modifiers (I, I32, I64) [Closed]

> Username: jeking3  
> Created at: 2017-10-18 19:14:55 UTC  
> Updated at: 2017-12-18 15:36:27 UTC  
> Closed at: 2017-10-24 20:57:13 UTC  
> Url: https://github.com/boostorg/format/issues/35  

This is a request from [Dan Konigsbach on Boost Trac](https://svn.boost.org/trac10/ticket/10541) which I am moving to github issues.    
  
```  
Microsoft's printf implementation includes non-ANSI extensions to the valid length modifier prefixes: I, I32, and I64. boost::format currently does not recognize them.  
  
It would ease migration to boost::format if parse_printf_directive() in format/parsing.hpp would parse and ignore them, just as it currently does with h and l.  
  
(Note: I'd be willing to code the proposed enhancement, if that's appropriate.)  
  
Thanks!!!! Dan K.  
```  
  
Reference: https://msdn.microsoft.com/en-us/library/56e442dc.aspx  
  
Note that even though we could add parsing for these options, they would be summarily ignored by the current implementation because no conversion is done based on argument size.  These modifiers are parsed and ignored because the type passed in defines the argument size.
