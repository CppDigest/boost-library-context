# #442 - Review: doc issues [Closed]

> Username: alandefreitas  
> Created at: 2022-08-22 06:04:43 UTC  
> Updated at: 2022-10-26 19:57:03 UTC  
> Closed at: 2022-10-26 19:57:03 UTC  
> Url: https://github.com/boostorg/url/issues/442  

> - s/will be easy to read/is easy to read (no need to use the future tense)  
> - s/1.78/1.81 (can't use 1.78 if URL depends on TOT, which is stated  
> elsewhere in the review announcement)  
> - The bullet list is an awkward mix of verb- and noun-phrases (e.g.  
> "Require only C++11" and "Fast compilation, few templates").  I  
> suggest all noun-phrases, like "Requires only C++11").  
> - s/1.80/1.81 -- same reason as above.  
> - I think that flow in tutorial-style documentation is important.  In  
> the Overview, you introduce what the library is at a high level, then  
> jump into some build- and compiler-support weeds, only to then follow  
> with the Quick Look section.  IMO, you should move the bits after the  
> bullet list to a section called Compilers and Build Considerations or  
> similar, and place it much later in the docs.  
>   
> The text at the top makes the relative/absolute distinction sound  
> important, but the rest of the page does not define or show examples  
> of the difference between the two.  
>   
> I suggest single quotes around "/" in "The reason is any decoded  
> character / could make", since on first reading I thought it was part  
> of the documentation text, not in code-font.  Maybe do it like you do  
> for the mention of double slashes a few lines later?  
>   
> When the ambiguity of the string  
> "https://www.example.com/get-customer.php?name=John%26Doe" is  
> mentioned, the docs say that if the query string represents  
> parameters, the quoted "&" is not ambiguous.  Then it shows an example  
> of getting all the parameters from the string  
> "https://www.example.com/get-customer.php?id=409&name=Joe&individual".  
> This begs the question, what happens when there's a "%26" in the  
> string, and you use .params()?  Please show this as part of the  
> example, or at least explain it.  
>   
> Examples:  
>   
> It would be nice if these were on separate pages.  Even nicer would be  
> some context, besides just a bunch of code.  I had to Google "magnet  
> link", and I still don't really know what it is.  A brief explanation  
> would be helpful.  
>   
>   
> The utl::params API docs refer to it as a random-access view, but it  
> is neither random-access, nor a view.  
>

---

## Comment 1

> Username: alandefreitas  
> Created at: 2022-08-22 06:43:13 UTC  
> Url: https://github.com/boostorg/url/issues/442#issuecomment-1221918465  

More comments:  
  
>  - The current implementation trips a fair number of deprecation warnings in  
>    Boost Filesystem - I trust those are probably known and easily addressed  
>  - In Containers docs: "URL reference. Missing scheme and host." - should say  
>    "scheme and authority"  
>  - "Although URL query strings are often used to represent key/value pairs,  
>    they are not a compound element" - Is confusing to me. It seems to say  
>    key/values are not supported, but it goes on to say `url_view::params`  
>    extracts "this view of key/value pairs".  
>  - Defining BOOST_URL_NO_SOURCE_LOCATION does not compile the tests (possibly more?)  
>  - message for `error::success` returns arbitrary value:  
>   
>     #4372 error.cpp(53) failed: 'BOOST_URL_ERR(error::success).message() == "success"' ('bad alpha' == 'success')  
>   
>  - Running the tests is very noisy with console litter, making it hard to find  
>    actual failure info  
>   
>  - "Exceptions only thrown on excessive input length" - what is excessive? Is  
>    that restriction useful? Since parsing doesn't allocate in the first place,  
>    at the time of parsing any "excessive" allocation will already have existed.  
>    Any limit would usefully apply on conversion to `urls::  
>   
>  - Throw location on parse error appears to always be the call site if  
>    `.value()` is allowed to throw. While technically accurate (the _throw  
>    location_ vs _error location_) it does diminish the value of the error  
>    information if people opt to handle errors by exceptions. This might be more  
>    of a Boost System concern.  
>   
>  - That said, source location isn't always set - probably some spots missing BOOST_URL_RETURN_EC magic?  
>   
>         auto rr = parse_relative_ref("foo:bar");  
>   
>         if (rr.has_error()) {  
>             std::cout << "Error: " << rr.error().location() << "\n"; // "(unknown source location)"  
>         }  
>

---

## Comment 2

> Username: alandefreitas  
> Created at: 2022-08-22 20:56:13 UTC  
> Url: https://github.com/boostorg/url/issues/442#issuecomment-1223020039  

> I also like the help card, which  
> would be greatly improved by adding links to the reference pages.

---

## Comment 3

> Username: alandefreitas  
> Created at: 2022-08-22 21:29:00 UTC  
> Url: https://github.com/boostorg/url/issues/442#issuecomment-1223097298  

> Two, the colouring of different parts of URL syntax, as in  
> https://master.url.cpp.al/url/containers.html#url.containers.generic_syntax:  
> it help to quickly grasp things, but it also bring some confusion. For  
> instance, I cannot tell if the double-slash is part of authority, or if the  
> colon is part of the scheme. The colors suggest that they are, but the  
> member functions tell otherwise.  
>

---

## Comment 4

> Username: vinniefalco  
> Created at: 2022-10-04 00:18:49 UTC  
> Url: https://github.com/boostorg/url/issues/442#issuecomment-1266236244  

> Two, the colouring of different parts of URL syntax, as in  
https://master.url.cpp.al/url/containers.html#url.containers.generic_syntax:  
it help to quickly grasp things, but it also bring some confusion.   
  
I'm getting rid of this diagram.  
  
This is the new one, I think its clear?  
  
![image](https://user-images.githubusercontent.com/1503976/193708298-2d95abfe-de4e-4f20-b6cb-9b1b11491afd.png)

---

## Comment 5

> Username: alandefreitas  
> Created at: 2022-10-04 08:44:21 UTC  
> Url: https://github.com/boostorg/url/issues/442#issuecomment-1266608462  

Yes. That's nice.

---

## Comment 6

> Username: alandefreitas  
> Created at: 2022-10-20 20:30:42 UTC  
> Url: https://github.com/boostorg/url/issues/442#issuecomment-1286109142  

> IMO, you should move the bits after the bullet list to a section called Compilers and Build Considerations or similar, and place it much later in the docs.  
  
I'm not sure we should do that

---

## Comment 7

> Username: alandefreitas  
> Created at: 2022-10-20 21:05:35 UTC  
> Url: https://github.com/boostorg/url/issues/442#issuecomment-1286150334  

Same for  
  
> Running the tests is very noisy with console litter, making it hard to find  
actual failure info
