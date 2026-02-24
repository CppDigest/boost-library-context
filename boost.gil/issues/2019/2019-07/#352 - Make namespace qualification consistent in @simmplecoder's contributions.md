# #352 - Make namespace qualification consistent in @simmplecoder's contributions [Open]

> Username: simmplecoder  
> Created at: 2019-07-25 10:19:14 UTC  
> Updated at: 2019-07-25 11:45:01 UTC  
> Url: https://github.com/boostorg/gil/issues/352  

I'm a bit paranoid on ADL, so I use full qualification everywhere. I do not think it is that much of a problem, just a habit. I'm opening an issue because there might code already in develop that has full qualification, so I thought may be doing this in one sweep might be a good idea. I will compile the list of files and lines as clean-up date approaches.

---

## Comment 1

> Username: mloskot  
> Created at: 2019-07-25 11:45:00 UTC  
> Url: https://github.com/boostorg/gil/issues/352#issuecomment-515011729  

> I'm a bit paranoid on ADL, so I use full qualification everywhere.  
  
Being considerate is a good thing, but...  
  
> I do not think it is that much of a problem, just a habit  
  
Generally, if there is no known issue to be resolved, I don't think it is necessary to be overly cautious in this case.  
  
Specifically, I think the qualification in places that I pointed out during reviews is simply useless not needed.  
  
Nicolai Josuttis in his [The C++ Standard Library: A Tutorial and Reference](http://www.cppstdlib.com/) makes it quite clear:  
  
> You **don’t have to qualify** the namespace for functions if one or more argument  
> types are defined in the namespace of the function.   
  
/cc @stefanseefeld
