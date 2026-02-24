# #594 - Getting JSON [Closed]

> Username: celavek  
> Created at: 2017-07-04 13:01:31 UTC  
> Updated at: 2017-07-04 14:05:58 UTC  
> Closed at: 2017-07-04 14:05:58 UTC  
> Url: https://github.com/boostorg/beast/issues/594  

Hi,  
  
I am trying to use the library to get data from a website, via its API, which is returned in JSON form.  
I read the documentation and if I understood correctly what I have to provide is a custom `Body` type along with its corresponding structures\algorithms for the `reader` and `writer` to pass to the `message` template.  
  
Is that the correct approach? Or getting the body as a string and then parsing it accordingly would be enough. The examples are not clear on this type of situation. Maybe a small example on how to use Beast to build higher level libraries\abstractions would be helpful although I don't know if it would be feasible.  
  
This might not be the place to ask this kind of questions so please let me know if I should direct this kind of inquiries someplace else.  
  
Thanks

---

## Comment 1

> Username: vinniefalco  
> Created at: 2017-07-04 13:49:02 UTC  
> Updated at: 2017-07-04 13:49:38 UTC  
> Url: https://github.com/boostorg/beast/issues/594#issuecomment-312882951  

> Or getting the body as a string and then parsing it accordingly would be enough  
  
^^ That. Of course, if you really want to make it complex you could make a subclass of `Body`, to parse as you go. But its not necessary.  
  
> let me know if I should direct this kind of inquiries someplace else.  
  
You're in the right place!

---

## Comment 2

> Username: celavek  
> Created at: 2017-07-04 14:05:58 UTC  
> Url: https://github.com/boostorg/beast/issues/594#issuecomment-312886661  

Thank you!
