# #89 - Document CONNECT use-case with parse_authority [Closed]

> Username: vinniefalco  
> Created at: 2021-12-20 21:31:53 UTC  
> Updated at: 2022-03-15 16:53:19 UTC  
> Closed at: 2022-03-15 16:53:19 UTC  
> Url: https://github.com/boostorg/url/issues/89  

See: https://datatracker.ietf.org/doc/html/rfc7230#section-5.3.3

---

## Comment 1

> Username: alandefreitas  
> Created at: 2022-02-24 19:46:34 UTC  
> Url: https://github.com/boostorg/url/issues/89#issuecomment-1050203503  

We already have an example like that in https://develop.url.cpp.al/url/parsing/authority.html#url.parsing.authority.authority_view

---

## Comment 2

> Username: vinniefalco  
> Created at: 2022-02-26 14:42:44 UTC  
> Url: https://github.com/boostorg/url/issues/89#issuecomment-1052150286  

Nice! Since this is the use-case that 99% of people will need, I think we should reflect that in the section heading so they can find it in the table of contents.

---

## Comment 3

> Username: alandefreitas  
> Created at: 2022-02-28 22:53:42 UTC  
> Url: https://github.com/boostorg/url/issues/89#issuecomment-1054763545  

We did have something like that, where authority_view had its own section. I merged the sections in some previous PR to make the TOC look a little better:   
  
![image](https://user-images.githubusercontent.com/5369819/156071166-3385bdda-a04a-49e4-8b3b-0f39e5629bdc.png)  
(the overview could still improve though)  
  
Maybe we have a different idea for that, but isolating authority view in the TOC looked quite ugly:  
  
![image](https://user-images.githubusercontent.com/5369819/156071468-66de24f9-23cc-4b53-b916-1cddfff44b0e.png)  
  
This previous layout also made the relationship between authority and authority_view more complex to explain, less natural to the reader, and I'm not sure it's so helpful, since authority is already visible and it's the natural place to look for that kind of use case.  But I don't know, maybe there's a better way I haven't considered yet.

---

## Comment 4

> Username: vinniefalco  
> Created at: 2022-03-01 07:43:47 UTC  
> Url: https://github.com/boostorg/url/issues/89#issuecomment-1055118718  

> isolating authority view in the TOC looked quite ugly:  
  
Yes that image of the ToC looks bad. It repeats the word Authority. How about the heading "HTTP CONNECT" since that's usually what it is used with?

---

## Comment 5

> Username: alandefreitas  
> Created at: 2022-03-01 16:21:18 UTC  
> Url: https://github.com/boostorg/url/issues/89#issuecomment-1055617599  

To be honest, the repeated word is something we can improve but not something I mind that much because this is likely to happen in this kind of document anyway.   
  
What I don't like is  
- visually the snaggle-tooth as an exception for something that is really just a use case among many, and   
- how it conceptually affects the section hierarchy, because "authority view" is not even the last subsection of this section and this becomes a mess: the "Authority" section would include text that should come before and after the "Authority view"  
  
But we can do anything. Just say the word.

---

## Comment 6

> Username: vinniefalco  
> Created at: 2022-03-01 16:34:44 UTC  
> Url: https://github.com/boostorg/url/issues/89#issuecomment-1055630488  

"Documentation can always be improved."  
  
Get the information into the docs, and we can worry how to make it pretty later.

---

## Comment 7

> Username: alandefreitas  
> Created at: 2022-03-01 17:32:22 UTC  
> Url: https://github.com/boostorg/url/issues/89#issuecomment-1055684651  

> Get the information into the docs, and we can worry how to make it pretty later.  
  
The information is in the docs already. We are just discussing the presentation now.   
  
> "Documentation can always be improved."  
  
At this point, we're not discussing an improvement. I think we're discussing whether this would make it worse.

---

## Comment 8

> Username: vinniefalco  
> Created at: 2022-03-08 21:04:59 UTC  
> Url: https://github.com/boostorg/url/issues/89#issuecomment-1062207594  

What I mean is we can live with it the way it is now...and worry about making it better later.

---

## Comment 9

> Username: alandefreitas  
> Created at: 2022-03-08 22:02:13 UTC  
> Url: https://github.com/boostorg/url/issues/89#issuecomment-1062261039  

I support that :)
