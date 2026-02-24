# #7 - Heading levels differ for classes versus functions [Open]

> Username: mellery451  
> Created at: 2016-10-10 23:49:57 UTC  
> Updated at: 2016-10-10 23:49:57 UTC  
> Url: https://github.com/boostorg/docca/issues/7  

For example:  
[class](http://vinniefalco.github.io/docca/beast/ref/beast__basic_streambuf.html)  
[method](http://vinniefalco.github.io/docca/beast/ref/beast__basic_streambuf/allocator_type.html)  
  
A workaround is to manually specify an explicit heading level, e.g. `[h3 Synopsis]`  
  
This most likely is caused when symbols are at different section nesting depth in the xml.
