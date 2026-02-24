# #43 - Error handlers are underdocumented [Closed]

> Username: akrzemi1  
> Created at: 2024-01-01 15:41:29 UTC  
> Updated at: 2024-01-15 08:34:28 UTC  
> Closed at: 2024-01-15 08:34:28 UTC  
> Url: https://github.com/boostorg/parser/issues/43  

It is not clear from the docs if users can define their own error handlers, or are they confined to the four handlers provided by the library.  
  
If one can install one's own hadler, then we would need a concept (or equivalent) describing what needs to be defined and with what semantics for a handler to be a handler.  
  
(Generally the docs fail to document concepts employed by this library. Another one is this `R` with specific requirements.)  
  
When does the parser generate a warning rather than an error?

---

## Comment 1

> Username: akrzemi1  
> Created at: 2024-01-01 15:45:28 UTC  
> Url: https://github.com/boostorg/parser/issues/43#issuecomment-1873376698  

Also, I thought that the parser itself does not throw exceptions on unsuccessful parse. Where do the excepitons come from?  
  
I would expect any exceptions related to managing resources ot be transparently thrown out of `parse()`.

---

## Comment 2

> Username: tzlaine  
> Created at: 2024-01-09 01:07:35 UTC  
> Url: https://github.com/boostorg/parser/issues/43#issuecomment-1882108633  

Right at the top of https://tzlaine.github.io/parser/doc/html/boost_parser__proposed_/tutorial/error_handling_and_debugging.html, it lists the 4 kinds of provided error handlers, and then there's a note immediately after that talks about using your own.  
  
The `error_handler` concept is defined in the Concepts page.  
  
Then at the bottom of the `parse()` API page, it talks about how to change the erro handler (https://tzlaine.github.io/parser/doc/html/boost_parser__proposed_/tutorial/the__parse____api.html#boost_parser__proposed_.tutorial.the__parse____api.globals_and_error_handlers).  
  
I feel like this is pretty well documented.  I'm open to specific suggestions for improvement, though.

---

## Comment 3

> Username: akrzemi1  
> Created at: 2024-01-09 18:08:00 UTC  
> Url: https://github.com/boostorg/parser/issues/43#issuecomment-1883544280  

I think that this page ([Error Handling and Debugging](https://tzlaine.github.io/parser/doc/html/boost_parser__proposed_/tutorial/error_handling_and_debugging.html)) should be self contained: I should only need to read this page to know:  
 * when it is good time to create my own error handler  
 *  what I should do to make it work  
  
Maybe no need to repeat stuff from other secitons, but at least provide cross-references.  
  
I do not know from the docs, under what circumstances the parser will invoke my handler with value `diagnostic_kind::warning`.  
  
When my error handler is passed iterator `first`, I do not know when to stop iterating over its range.  
  
The concept description doesn't tell me what the function call operator is for. Why do we have it plus the two `.diagnose` functions?  
  
There is no link to the concept. Nor to the docs that say how o pass it.   
  
The docs in https://tzlaine.github.io/parser/doc/html/boost_parser__proposed_/tutorial/the__parse____api.html#boost_parser__proposed_.tutorial.the__parse____api.globals_and_error_handlers  
have a bug, I think: is the function called `[with_error_handler()](https://tzlaine.github.io/parser/doc/html/boost/parser/with_error_handler.html)` or `error_handler()`?

---

## Comment 4

> Username: tzlaine  
> Created at: 2024-01-10 03:37:52 UTC  
> Url: https://github.com/boostorg/parser/issues/43#issuecomment-1884139138  

Fair enough.  I'll make the section on error handlers more complete, and add an example.
