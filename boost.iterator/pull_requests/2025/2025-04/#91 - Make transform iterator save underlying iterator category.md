# #91 Make transform iterator save underlying iterator category. [Closed]

> Username: gogagum  
> Created at: 2025-04-22 23:41:35 UTC  
> Updated at: 2025-04-23 07:26:47 UTC  
> Closed at: 2025-04-23 07:26:47 UTC  
> Url: https://github.com/boostorg/iterator/pull/91  

- operator[] in iterator facade now returns a reference, because its return type must be the same as dereference type for random_access_iterator concept.  
- add tests for categories checks.  
- change trait to determine transform_iterator reference type.

---

## Comment 1

> Username: Lastique  
> Created_at: 2025-04-22 23:57:48 UTC  
> Url: https://github.com/boostorg/iterator/pull/91#issuecomment-2822739519  

I'm not sure what problem you're solving, but the reference type may not be an actual reference if the transform function returns an rvalue.

---

## Comment 2

> Username: Lastique  
> Created_at: 2025-04-23 07:26:47 UTC  
> Url: https://github.com/boostorg/iterator/pull/91#issuecomment-2823318535  

I don't think I will accept these changes as they change the behavior of `iterator_facade` in an incompatible way and may break user-defined iterators. As it evidently broke `counting_iterator`. And I also don't think users should be required to define the `operator[]` (or other operators) themselves - it's the whole reason `iterator_facade` exists.  
  
If you want `operator[]` to return an actual reference, a different approach is needed. `iterator_facade` needs to know whether returning a reference is safe and only then do that. I'm not sure how to do that other than with some new template parameter or a trait (that defaults to `false` for backward compatibility).  
  
And finally, I'm not sure why you're changing the iterator category of the `transform_iterator`, but changing `output_iterator_tag` to `input_iterator_tag` seems entirely wrong to me.

---
