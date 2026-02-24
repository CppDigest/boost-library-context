# #211 Qi: List operator not omitting second argument [Closed]

> Username: mxc-commons  
> Created at: 2016-08-19 21:10:55 UTC  
> Updated at: 2016-08-24 03:35:40 UTC  
> Closed at: 2016-08-24 03:35:40 UTC  
> Url: https://github.com/boostorg/spirit/pull/211  

For discussion: This is an example of the list operator not omitting the second argument's attribute.  
For this one the original docu page's first part is true: `a % b` is equivalent to `a >> *(b >> a)`.   
The attribute section would need correction, because `b` would not longer get omitted.  
See #208.  
  
To retain the behaviour of the current list operator using the new one, you'd have to write `a % omit[b]`. An example file is provided to show the difference.  
  
It feels strange that currently `a % char_('b')` is equivalent to `a % lit('b')`. Just a matter of taste?

---

## Comment 1

> Username: djowel  
> Created_at: 2016-08-23 05:58:32 UTC  
> Url: https://github.com/boostorg/spirit/pull/211#issuecomment-241634620  

I think this one needs some more explanation on the use-cases.

---

## Comment 2

> Username: mxc-commons  
> Created_at: 2016-08-24 00:11:37 UTC  
> Url: https://github.com/boostorg/spirit/pull/211#issuecomment-241921161  

With or without this PR there is no particular use case which was not expressible as `a >> *(b >> a)`.  
Today `a % b` is not a full replacement, because the attribute of `b` is omitted always.   
  
Namespace qualified identifiers `ident::ident2::ident3` (including the `::`) for example could be expressed as `id % string("::")` with that PR. Without this PR this example would have to be `name >> *(string("::") >> name)`.  
  
I proposed that because I'd find it more consistent if `a % char_('b')` was not equivalent to `a % lit('b')` and `a % b` was not equivalent to `a % omit[b]`.  
  
Accepted PR #210 addresses the same topic without a change to the operator.

---

## Comment 3

> Username: djowel  
> Created_at: 2016-08-24 01:10:37 UTC  
> Url: https://github.com/boostorg/spirit/pull/211#issuecomment-241929708  

Well, alas, it's a no-go if it will introduce a breaking change.

---

## Comment 4

> Username: mxc-commons  
> Created_at: 2016-08-24 03:35:40 UTC  
> Url: https://github.com/boostorg/spirit/pull/211#issuecomment-241948602  

It would introduce a breaking change because users would have to switch to an explicit omit using the operator.  
  
This PR comes 10 years late or so, I know. :)

---
