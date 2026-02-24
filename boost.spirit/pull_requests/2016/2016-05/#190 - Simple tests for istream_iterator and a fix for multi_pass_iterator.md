# #190 Simple tests for istream_iterator and a fix for multi_pass_iterator [Merged]

> Username: jefftrull  
> Created at: 2016-05-16 20:40:45 UTC  
> Updated at: 2016-05-16 21:49:10 UTC  
> Merged at: 2016-05-16 21:39:32 UTC  
> Closed at: 2016-05-16 21:39:32 UTC  
> Url: https://github.com/boostorg/spirit/pull/190  

This should resolve TRAC #12166 and add some test coverage for istream_iterator (including the fix).

---

## Comment 1

> Username: bebuch  
> Created_at: 2016-05-16 21:37:25 UTC  
> Url: https://github.com/boostorg/spirit/pull/190#issuecomment-219556052  

The relational operators in lines 155, 159 and 163 can also be const.

---

## Comment 2

> Username: jefftrull  
> Created_at: 2016-05-16 21:49:10 UTC  
> Url: https://github.com/boostorg/spirit/pull/190#issuecomment-219558987  

@bebuch very true :)  I just wasn't sure what their purpose was and decided to leave them alone...

---
