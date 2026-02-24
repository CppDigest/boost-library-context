# #173 - pydocca: function declarations with many specifiers get too many newlines (quickbook) [Closed]

> Username: anarthal  
> Created at: 2024-09-02 15:06:31 UTC  
> Updated at: 2024-10-03 13:37:18 UTC  
> Closed at: 2024-10-03 13:37:18 UTC  
> Url: https://github.com/boostorg/docca/issues/173  

When a function declaration gets a lot of specifiers (e.g. a constructor is `explicit` and `constexpr`), each specifier gets its own newline, causing declarations to look slightly weird.  
  
Seems to be caused by [these lines](https://github.com/boostorg/docca/blob/5f349f0c7b81efa6f26cd1014cc15f5addfbbc87/include/docca/quickbook/components.jinja2#L386-L393) in the quickbook template. To match the xslt version, all specifiers should be in the same line as the return type (which I think makes sense).
