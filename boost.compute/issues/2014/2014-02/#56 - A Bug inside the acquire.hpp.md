# #56 - A Bug inside the acquire.hpp [Closed]

> Username: sajis997  
> Created at: 2014-02-28 18:01:50 UTC  
> Updated at: 2014-03-01 05:38:40 UTC  
> Closed at: 2014-03-01 05:38:40 UTC  
> Url: https://github.com/boostorg/compute/issues/56  

Hi ,   
  
I am getting an error at the function "void opengl_enqueue_release_gl_objects" even though no reference has been made to the function.   
  
The issue has been solved by putting an inline before the function definition in the source.  
  
Not sure though if it is a crack or a hack.  
  
Looking forward to be addressed by the authors.  
  
Thanks

---

## Comment 1

> Username: kylelutz  
> Created at: 2014-03-01 05:38:39 UTC  
> Url: https://github.com/boostorg/compute/issues/56#issuecomment-36416984  

Thanks for the report!  
  
Fixed in 86c0bb0a1294cd684412b34ba22bd119b9d45f50.
