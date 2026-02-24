# #47 - Make the description of the transform() algorithm a lot clearer [Closed]

> Username: tzlaine  
> Created at: 2018-02-16 21:27:55 UTC  
> Updated at: 2018-06-11 17:16:19 UTC  
> Closed at: 2018-02-25 20:19:14 UTC  
> Url: https://github.com/boostorg/yap/issues/47  

Explicit transformations: the phrases "that will be placed into result at the corresponding place in result" seems confusing.  Especially given the central role played by transforms, I feel that a much clearer description of their operation is needed.  This could leverage a detailed section on expression trees generally and (another one?) on the representation of them in Yap.  The latter is implicit in the expression concept, but clarity would be improved by making it explicit and by relating it to the more general concepts.  
  
Yeah, I've never been super happy with that sentence.  I think this calls for a picture, too.  
  
The sentence "Otherwise, N is used, and visitation of nodes in expr continues below N." seems misleading if I am understanding transforms correctly.  First, the "N is used" part is very vague; used for what specifically?  Second, the "visitation continues" part seems wrong.  Isn't the recursion entirely under the control of the transform?  If so, then how can this assertion be made?  Perhaps you mean something like "sane transforms should often be written to have this property".  Again, this gets at the need to provide much clearer best practices information that is clearly distinct from tutorials and reference material.  These all serve different needs and should not be interspersed in the documentation.
