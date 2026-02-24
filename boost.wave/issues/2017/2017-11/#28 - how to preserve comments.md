# #28 - how to preserve comments ? [Closed]

> Username: malickf  
> Created at: 2017-11-30 11:31:15 UTC  
> Updated at: 2017-11-30 14:00:14 UTC  
> Closed at: 2017-11-30 13:48:02 UTC  
> Url: https://github.com/boostorg/wave/issues/28  

Does anyone know how to preserve comments ? I saw some related code but can't figure out the way to get comments in the preprocessed file.   
  
I'm working with the waveidl sample.  
  
I have tried to change this line : https://github.com/boostorg/wave/blob/6a7af0e878c2eefa2a68535fdd729146893fdcc6/samples/waveidl/idl.cpp#L375  
  
to :   
  
`   output <<  (*first).get_value() << endl;`  
  
but comment still doesn't appear. Only a new line is added.  
  
Many thanks.

---

## Comment 1

> Username: hkaiser  
> Created at: 2017-11-30 13:26:48 UTC  
> Url: https://github.com/boostorg/wave/issues/28#issuecomment-348187329  

See the wave tool example for how to achieve this: https://github.com/boostorg/wave/blob/develop/tool/cpp.cpp#L914-L917

---

## Comment 2

> Username: malickf  
> Created at: 2017-11-30 13:48:00 UTC  
> Updated at: 2017-11-30 13:48:12 UTC  
> Url: https://github.com/boostorg/wave/issues/28#issuecomment-348192547  

@hkaiser    
Many thanks, I have just added :   
  
`  ctx.set_language(  
                boost::wave::enable_preserve_comments(ctx.get_language()));  
			}`  
and it effectively preserves comments . Great !  
  
Thank you !

---

## Comment 3

> Username: hkaiser  
> Created at: 2017-11-30 14:00:14 UTC  
> Url: https://github.com/boostorg/wave/issues/28#issuecomment-348195660  

@malickf the wave tool is a nice resource exposing most of (If not all) capabilities of the library itself. You should be able to find many answers by looking at this code.
