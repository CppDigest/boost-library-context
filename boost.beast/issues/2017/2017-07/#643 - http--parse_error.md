# #643 - http::parse_error [Open]

> Username: vinniefalco  
> Created at: 2017-07-13 03:46:33 UTC  
> Updated at: 2017-09-01 03:26:47 UTC  
> Url: https://github.com/boostorg/beast/issues/643  

This should be a separate category with a get function for the category parse_error.hpp or maybe parser.hpp and documentation, parser errors are non-recoverable. The recoverable errors can stay in http::error (which also needs a name and a getter for the category).

---

## Comment 1

> Username: vinniefalco  
> Created at: 2017-08-12 20:07:29 UTC  
> Url: https://github.com/boostorg/beast/issues/643#issuecomment-322002903  

See https://akrzemi1.wordpress.com/2017/08/12/your-own-error-condition/#comment-6954
