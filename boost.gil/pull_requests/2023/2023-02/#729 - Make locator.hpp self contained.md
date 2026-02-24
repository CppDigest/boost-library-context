# #729 Make locator.hpp self contained [Merged]

> Username: sdebionne  
> Created at: 2023-02-20 14:13:15 UTC  
> Updated at: 2023-03-21 14:58:20 UTC  
> Merged at: 2023-02-22 10:59:12 UTC  
> Closed at: 2023-02-22 10:59:12 UTC  
> Url: https://github.com/boostorg/gil/pull/729  

### Description  
  
`locator` implementation uses `make_step_iterator()` but `step_iterator.hpp` is not included.  
  
### Tasklist  
  
<!-- Add YOUR OWN TASK(s), especially if your PR is a work in progress -->  
  
- [x] Ensure all CI builds pass  
- [x] Review and approve

---

## Comment 1

> Username: sdebionne  
> Created_at: 2023-02-20 14:25:39 UTC  
> Url: https://github.com/boostorg/gil/pull/729#issuecomment-1437106609  

This fixes the following issue:  
  
```  
boost/gil/locator.hpp:258:32: error: 'make_step_iterator' was not declared in this scope, and no declarations were found by argument-dependent lookup at the point of instantiation [-fpermissive]  
  258 |         : _p(make_step_iterator(loc.x(),(transpose ? loc.row_size() : loc.pixel_size())*x_step),  
      |              ~~~~~~~~~~~~~~~~~~^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
...  
note: 'template<class I> typename boost::gil::dynamic_x_step_type<T>::type boost::gil::make_step_iterator(const I&, std::ptrdiff_t)' declared here, later in the translation unit

---

## Comment 2

> Username: sdebionne  
> Created_at: 2023-03-21 14:34:53 UTC  
> Url: https://github.com/boostorg/gil/pull/729#issuecomment-1477951850  

@mloskot Do you think this little change could make it to master for 1.82?

---

## Comment 3

> Username: mloskot  
> Created_at: 2023-03-21 14:58:15 UTC  
> Url: https://github.com/boostorg/gil/pull/729#issuecomment-1477992385  

@sdebionne I see no reason why not, unless the release managers object it.

---
