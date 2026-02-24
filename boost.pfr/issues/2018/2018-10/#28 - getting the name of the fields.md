# #28 - getting the name of the fields [Closed]

> Username: skgbanga  
> Created at: 2018-10-17 15:06:45 UTC  
> Updated at: 2018-10-17 15:07:48 UTC  
> Closed at: 2018-10-17 15:07:48 UTC  
> Url: https://github.com/boostorg/pfr/issues/28  

Hey,  
  
I just found this, and was taking a look at this:  
http://apolukhin.github.io/magic_get/boost_precise_and_flat_reflectio/short_examples_for_the_impatient.html  
  
```  
// incrementing each field on 1:  
boost::pfr::flat_for_each_field(var, [](auto& field) {  
    field += 1;  
});  
```  
  
Is there a quick function for getting the names of the fields as well so that reflection can be built more easily? e.g. in this case  
  
```  
struct bar {  
    char character;  
    foo f;  
};  
```  
  
getting `character` and `f` as well.

---

## Comment 1

> Username: skgbanga  
> Created at: 2018-10-17 15:07:48 UTC  
> Url: https://github.com/boostorg/pfr/issues/28#issuecomment-430666917  

Ah, sorry. ignore this. There is an open issue for this:  
https://github.com/apolukhin/magic_get/issues/4
