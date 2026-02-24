# #210 - python 3 compatible injector pattern [Closed]

> Username: Anthchirp  
> Created at: 2018-06-07 18:51:25 UTC  
> Updated at: 2018-06-08 18:59:46 UTC  
> Closed at: 2018-06-08 18:59:46 UTC  
> Url: https://github.com/boostorg/python/issues/210  

The boost python tutorial suggests this injector pattern:  
https://github.com/boostorg/python/blob/2d1f66fd1948fa28fde05f4c533b13fb3c96d782/doc/tutorial.qbk#L1874-L1895  
This depends on the behaviour of ```__metaclass__```, which has changed in python 3, so boost injections using the class constructor will no longer happen. There are no error messages or anything - the code is simply never executed.  
  
It is not incredibly difficult to change the pattern from the currently suggested form of  
```python  
class _(injector, extension_class):  
  def method(...):  
     ...  
```  
to a more imperative  
```python  
class _():  
  def method(...):  
    ...  
inject(extension_class, _)  
```  
that would work in python 2 and 3. Or one could even do a probably more pythonic  
```python  
@inject_into(extension_class)  
class _():  
  def method(...):  
    ...  
```  
which could also distinguish between classes and functions, so could support single method injection as well  
```python  
@inject_into(extension_class)  
def __str__(self):  
    (...)  
```  
but has the downside of local name space cluttering.  
  
Are these sensible suggestions? I would be happy to provide a reference implementation write this up properly in a pull request.

---

## Comment 1

> Username: stefanseefeld  
> Created at: 2018-06-07 19:15:04 UTC  
> Url: https://github.com/boostorg/python/issues/210#issuecomment-395533635  

Given that this is merely some illustration of Python features, which are themselves entirely unrelated to Boost.Python, I tend to think we might simply remove those outdated and non-portable suggestions. The Boost.Python tutorial isn't the right place to teach how to use Python metaclasses, so I'd rather not concern myself with that here.  
  
So if you want to submit a PR that removes those bits from the tutorial, that would be fine.
