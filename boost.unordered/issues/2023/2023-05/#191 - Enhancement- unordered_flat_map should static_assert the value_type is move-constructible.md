# #191 - Enhancement: unordered_flat_map should static_assert the value_type is move-constructible [Closed]

> Username: hadrielk  
> Created at: 2023-05-30 20:10:01 UTC  
> Updated at: 2024-08-08 17:00:45 UTC  
> Closed at: 2024-08-08 16:57:23 UTC  
> Url: https://github.com/boostorg/unordered/issues/191  

Unlike `unordered_node_map<>`, the `flat` one requires the `value_type` be move-constructible. (I think? Or perhaps copy-constructible if the type is not move-constructible?)  
  
Unfortunately, if you use a type that is _not_ move-constructible, the compiler generates an enormous wall of cryptic errors instead... because the compilation failure occurs inside of a deep chain of template instantiations.  
  
I think a few `static_assert(std::is_move_constructible<T>::value)`s would be nicer.

---

## Comment 1

> Username: cmazakas  
> Created at: 2023-05-31 03:50:09 UTC  
> Url: https://github.com/boostorg/unordered/issues/191#issuecomment-1569454096  

This is simple to do and is a good hardening measure to improve the UX.  
  
We'll get this done soon after the concurrent_flat_map branch is merged in.

---

## Comment 2

> Username: k3DW  
> Created at: 2024-08-08 17:00:44 UTC  
> Url: https://github.com/boostorg/unordered/issues/191#issuecomment-2276275438  

After merging #269, now every invalid constructor call will be caught. This is generic for any future use cases, as I have not hard-coded any specific constraints on the containers. A `flat` container will invoke a move constructor, and therefore requires that a move constructor exists and is callable. A `node` container does not do this, so it will not check for move constructibility.
