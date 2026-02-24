# #1886 - opt_token_list versus token_list [Closed]

> Username: ErikvLinstee  
> Created at: 2020-03-29 12:44:59 UTC  
> Updated at: 2020-04-07 17:15:54 UTC  
> Closed at: 2020-04-07 17:15:54 UTC  
> Url: https://github.com/boostorg/beast/issues/1886  

The descriptions of opt_token_list and token_list are identical. As are the BNF and example.  
Is this a documentation mistake, or a comprehension failure on my part?

---

## Comment 1

> Username: madmongo1  
> Created at: 2020-04-07 16:58:47 UTC  
> Url: https://github.com/boostorg/beast/issues/1886#issuecomment-610504513  

Hi @ErikvLinstee ,  
  
Thanks for creating the issue, and sorry for the delay.  
  
The two types are very similar in form and function. There are two subtle differences:  
  
1. The function `validate_list(opt_token_list const&)` exists for the `opt_token_list` type.  
2. `token_list` has a member function `bool exists(string_view const&)` (note: not const).  
  
In every other respect the two types are identical. As I understand it, the types were created to model two distinct BNF rules in the HTTP RFCs.  
  
However, in hindsight the existence of both types in the library's public interface seems to be an oversight.  
  
The only intended legitimate use of this container view is to parse the tokens in a `Connection` header, which you would need to do if writing a web server or proxy server, ref: https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Connection  
  
It's a narrow use case which in all likelihood does not even warrant one public type let alone two.  
  
We are having internal discussions on how to proceed. It may be reasonable to think that at some point one or both of these types will be deprecated.
