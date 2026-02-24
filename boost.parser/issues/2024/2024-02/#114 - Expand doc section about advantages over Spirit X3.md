# #114 - Expand doc section about advantages over Spirit X3 [Closed]

> Username: tzlaine  
> Created at: 2024-02-24 02:26:53 UTC  
> Updated at: 2024-03-15 21:29:25 UTC  
> Closed at: 2024-03-15 21:29:25 UTC  
> Url: https://github.com/boostorg/parser/issues/114  

From Christian Mazakas in the Boost review:  
  
> The differences between Spirit.X3 and Parser need to be made much more  
> clear by the  
> documentation, by which I mean:  
> > * Spirit X3 has rules that do not compose well — the attributes produced  
> by a rule can change depending on the context in which you use the rule.  
> > * Spirit X3 is missing many of the convenient interfaces to parsers that  
> Spirit 2 had. For instance, you cannot add  
> parameters to a parser.  
> > * All versions of Spirit have Unicode support, but it is quite difficult  
> to get working.  
>  
> These need code samples showing the limitations of X3 and how Parser  
> actually solves them. I think many,  
> myself included, aren't overwhelmingly clear on what these differences are  
> and how Parser solves them  
> and _why_ this is a good thing.
