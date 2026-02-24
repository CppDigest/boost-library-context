# #131 - Documentation comments from Reuben [Closed]

> Username: mborland  
> Created at: 2024-01-23 07:41:24 UTC  
> Updated at: 2024-01-30 07:55:45 UTC  
> Closed at: 2024-01-30 07:55:45 UTC  
> Url: https://github.com/boostorg/charconv/issues/131  

Minor comments on documentation:  
  
- The first example uses to_chars(... sizeof(buffer) - 1), which seems  
to imply a NULL-terminator somewhere. Since there is none, I think it  
should just be sizeof(buffer).  
- Docs don't specify the behavior about NULL-terminators. Either  
specify "adheres to standard" or clarify that no NULL-terminator is  
written.  
- IMO the front page should clearly state that this library is  
compliant with the charconv standard header, noting any exceptions.  
- from_chars overview is missing semicolons in the friends section.  
- Please use monospace in the discussion to refer to identifiers, like  
[first, last).  
- Basic usage: from_chars name is qualified but from_chars_result is not.  
- Code snippets wrap, which makes them difficult to read.  
  
cc: @anarthal
