# #358 - review error codes [Closed]

> Username: vinniefalco  
> Created at: 2022-08-06 22:15:36 UTC  
> Updated at: 2022-08-07 13:20:59 UTC  
> Closed at: 2022-08-07 13:20:59 UTC  
> Url: https://github.com/boostorg/url/issues/358  

All the error codes need to be reviewed, and we can probably trim them down by quite a lot especially when we annotate all lines that produce errors with file and line number information.  
  
Rules which don't match should return `grammar::error::mismatch` while rules that detect syntactically invalid input (for example a percent-escape with invalid hex digits) should use a different error. The recoverability of parsing should be reified into the error / condition hierarchy.

---

## Comment 1

> Username: vinniefalco  
> Created at: 2022-08-06 23:44:03 UTC  
> Url: https://github.com/boostorg/url/issues/358#issuecomment-1207298967  

Each parse function needs to return `error::incomplete` if it encounters the end of input before a complete match is found.  
  
Other names for the error:  
```  
end_of_input  
partial  
prefix  
need_input  
need_more     // <--- I'm leaning towards this  
more_input  
```

---

## Comment 2

> Username: vinniefalco  
> Created at: 2022-08-06 23:56:08 UTC  
> Url: https://github.com/boostorg/url/issues/358#issuecomment-1207299971  

This is also a good time to reorder conditionals to express the more common case as the `true` expression.

---

## Comment 3

> Username: vinniefalco  
> Created at: 2022-08-07 05:44:48 UTC  
> Url: https://github.com/boostorg/url/issues/358#issuecomment-1207335646  

`need_more` is done, grammar/ has been reviewed, rfc/ reviewed, we just have a few rfc error codes left to review.
