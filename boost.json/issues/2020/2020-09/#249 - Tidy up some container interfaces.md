# #249 - Tidy up some container interfaces [Closed]

> Username: vinniefalco  
> Created at: 2020-09-02 12:27:28 UTC  
> Updated at: 2020-09-04 23:34:09 UTC  
> Closed at: 2020-09-04 23:34:09 UTC  
> Url: https://github.com/boostorg/json/issues/249  

* Remove all `value::if_object`, et. al.  
* Return a pointer from all `value::is_object`, et. al. (currently returns a `bool`)  
* `object::contains` returns `value*` which can be null  
* Add `array::contains`, returns `value*` which can be null
