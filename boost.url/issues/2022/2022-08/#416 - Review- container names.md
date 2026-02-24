# #416 - Review: container names [Closed]

> Username: alandefreitas  
> Created at: 2022-08-16 23:57:40 UTC  
> Updated at: 2022-09-20 18:53:23 UTC  
> Closed at: 2022-09-20 18:53:22 UTC  
> Url: https://github.com/boostorg/url/issues/416  

From the review  
  
> I've found the naming for view containers confusing (e.g.  
params and params_view). When I saw it, I thought params  
would take ownership of the query string params buffer, while  
params_view would not. If I've understood correctly, the difference  
is that params allows for modification (as it's returned from url containers)  
and params_view does not (as it's returned from url_view). So it's  
more about mutability than ownership. If that's the case, I would  
either emphasize it in the docs, or rename the containers to  
something that reflects this fact (e.g. const_params and mutable_params,  
as asio does).  
  
More comments  
  
> Well...yeah. I am not entirely happy about having 8 different  
containers between the segments and params ranges. However the  
decision was made to offer the user an interface that resembles  
standard containers, so that standard algorithms could be composed on  
them (more or less). I think that the ForwardRange and  
BidirectionalRange interfaces were the right choice, so we followed  
the design where it led us. The need for having read-only views,  
modifiable views, over separate encoded versus unencoded strings left  
us with the 8 containers.  
  
> I would much rather emphasize it in the docs, because writing "const"  
and "mutable" over and over is bound to be a chore. Asio gets away  
with it because the mutability of buffers is central to the role of  
asynchronous operations but I'm not so sure the same applies here.  
  
> I would suggest leaving url and url_view as-is, and replacing the "view"  
part for "const" for the rest of the containers:  
> - params stays as is  
> - params_view becomes const_params  
> - params_encoded stays as is  
> - params_encoded_view becomes const_params_encoded  
>   
> And so on; I'd leave the member function names as they are.

---

## Comment 1

> Username: vinniefalco  
> Created at: 2022-08-17 00:04:10 UTC  
> Url: https://github.com/boostorg/url/issues/416#issuecomment-1217292008  

Yeah I like having the first word being the same more than I like changing "view" to "const". Because they sort nicely this way, the docs look decent and there's a certain pattern to it.

---

## Comment 2

> Username: alandefreitas  
> Created at: 2022-08-17 04:19:06 UTC  
> Url: https://github.com/boostorg/url/issues/416#issuecomment-1217443280  

I think the most important complaint is more about the mutable version, which might look like it owns the data. I don't know of a good suffix for that. `params_encoded_ref`/`params_encoded_view`?  
  
I can't find any spec that really defines query parameters either. The libraries I've looked at usually call them `search_params`...

---

## Comment 3

> Username: alandefreitas  
> Created at: 2022-08-24 17:03:27 UTC  
> Url: https://github.com/boostorg/url/issues/416#issuecomment-1225996580  

More comments:  
  
> > So the eager urls::url is the one maintaining the invariants, but the  
> > segment or parameter views are a better way to operate on the URL when  
> > dealing with individual subcomponents.  
> > Thus, the functions are not exactly a subset of the STL because the  
> > operations are ensuring the invariants of the underlying url remain valid.  
> > (The names "segments" and "params" might be a little confusing because it  
> > sounds like it owns the data. Some other review has recommended  
> > mutable_segments_view/mutable_params_view.)  
>   
> This seems to be the heart of the matter.  I expect params to be a  
> real flat_map or map or sorted vec, and it's not.  If my expectation  
> is wrong because of the lack of a _view suffix in the name, that's  
> fine -- but please add that to make things clear.  However, the _view  
> suffix in std C++ is used exclusively for immutable types, so maybe  
> something else is indicated, like _facade or some variation of span.

---

## Comment 4

> Username: alandefreitas  
> Created at: 2022-08-24 17:03:37 UTC  
> Url: https://github.com/boostorg/url/issues/416#issuecomment-1225996786  

```  
    segments_ref  
    segments_view  
    segments_encoded_ref  
    segments_encoded_view  
    params_ref  
    params_view  
    params_encoded_ref  
    params_encoded_view  
```

---

## Comment 5

> Username: vinniefalco  
> Created at: 2022-09-03 20:10:50 UTC  
> Url: https://github.com/boostorg/url/issues/416#issuecomment-1236192051  

This is what we settled on for params  
```  
params_view  
params_const_view  
params_encoded_view  
params_const_encoded_view  
```

---

## Comment 6

> Username: vinniefalco  
> Created at: 2022-09-20 18:53:22 UTC  
> Url: https://github.com/boostorg/url/issues/416#issuecomment-1252777115  

We went back to this:  
```  
    segments_ref  
    segments_view  
    segments_encoded_ref  
    segments_encoded_view  
    params_ref  
    params_view  
    params_encoded_ref  
    params_encoded_view  
```  
  
I think we're done with the renaming
