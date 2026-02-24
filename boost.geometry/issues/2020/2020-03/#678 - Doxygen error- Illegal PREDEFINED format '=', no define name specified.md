# #678 - Doxygen error: Illegal PREDEFINED format '=', no define name specified [Closed]

> Username: mloskot  
> Created at: 2020-03-05 23:10:08 UTC  
> Updated at: 2020-03-06 23:17:12 UTC  
> Closed at: 2020-03-06 23:17:12 UTC  
> Url: https://github.com/boostorg/geometry/issues/678  

```console  
$ python make_qbk.py  
Boost.Geometry is making .qbk files in /mnt/d/boost.wsl/libs/geometry/doc  
Boost.Geometry is cleaning Doxygen files in /mnt/d/boost.wsl/libs/geometry/doc/doxy/doxygen_output/xml/  
error: Illegal PREDEFINED format '=', no define name specified  
error: Illegal PREDEFINED format '=', no define name specified  
```  
  
The error seems to be caused by these `=`  
  
https://github.com/boostorg/geometry/blob/8baddd3634cbd627be58b0bfd264d8512785e52d/doc/doxy/Doxyfile#L317-L319  
  
I can't remember what was that [PREDEFINED](http://www.doxygen.nl/manual/config.html#cfg_predefined) config for, but it does not seem to be kosher syntax for Doxygen.  
  
AFAICT, these two names are not used anywhere in the `.qbk` or `.html` output:  
  
- `BOOST_CONCEPT_REQUIRES`  
- `BOOST_CONCEPT_ASSERT`  
  
Only `BOOST_STATIC_ASSERT` is used, once, in `design_rationale.qbk`.  
  
Shall we remove those two `BOOST_CONCEPT_*` ones or at least remove the `=` in order to get rid of the error?

---

## Comment 1

> Username: vissarion  
> Created at: 2020-03-06 11:16:02 UTC  
> Url: https://github.com/boostorg/geometry/issues/678#issuecomment-595722904  

I cannot reproduce it neither with `python 2` nor with `python 3`.   
I am getting some warnings though   
```  
Warning: template parameter Point has no type in franklin  
Warning: template parameter PointOfSegment has no type in franklin  
Warning: template parameter Point has no type in crossings_multiply  
Warning: template parameter PointOfSegment has no type in crossings_multiply  
Parameter not found: width_height  
```  
and also a few regarding `boost::geometry::index` like   
```  
/boost/boost/geometry/index/equal_to.hpp:165: warning: The following parameters of boost::geometry::index::detail::equal_to::operator()(Value const &l, Value const &r, Strategy const &strategy) const are not documented:  
  parameter 'strategy'  
```

---

## Comment 2

> Username: vissarion  
> Created at: 2020-03-06 11:18:24 UTC  
> Url: https://github.com/boostorg/geometry/issues/678#issuecomment-595723755  

Also, is it possible that removing the space in `(x) =` removes error? Although I do not see any reason not to remove the `=`.

---

## Comment 3

> Username: mloskot  
> Created at: 2020-03-06 11:44:29 UTC  
> Url: https://github.com/boostorg/geometry/issues/678#issuecomment-595732284  

Thanks for checking.  
  
> I am getting some warnings though  
  
Yes, I can confirm those too.  
  
> I cannot reproduce it neither with python 2 nor with python 3.  
  
I am using Python 2.7  
  
I forgot to add which Doxygen I'm using:  
  
```  
$ doxygen --version  
1.8.17  
```  
  
Perhaps that makes a difference.

---

## Comment 4

> Username: vissarion  
> Created at: 2020-03-06 12:14:57 UTC  
> Url: https://github.com/boostorg/geometry/issues/678#issuecomment-595742484  

>Perhaps that makes a difference.  
  
Indeed (I was with `1.8.13`). I can reproduce with `1.8.18` and also verify that the error is removed if you remove the space before `=`.

---

## Comment 5

> Username: mloskot  
> Created at: 2020-03-06 20:05:06 UTC  
> Url: https://github.com/boostorg/geometry/issues/678#issuecomment-595943513  

@vissarion Thank you for checking. That helps. I think we are safe to remove the `=`. I will do it.
