# #552 Support constructing a planar image from interleaved image [Merged]

> Username: theroyn  
> Created at: 2021-01-29 01:17:23 UTC  
> Updated at: 2021-01-30 20:11:52 UTC  
> Merged at: 2021-01-30 20:11:51 UTC  
> Closed at: 2021-01-30 20:11:52 UTC  
> Url: https://github.com/boostorg/gil/pull/552  

### Description  
  
Due to the different layout of planar images (e.g. `rgb8_planar_image_t`), construction of them using interleaved images(e.g. `rgb8_image_t`) failed on `std::unintialized_copy()` and this workflow needed special care.  
  
This implementation of the constructor does-  
  
1. allocate buffer according to the interleaved's dimensions.  
2. default-constructs the elements.  
3. copies the interleaved image.  
  
### References  
  
fixes #478 which was opened by @mloskot   
  
### Tasklist  
  
  
- [x] Add test case(s)  
- [x] Ensure all CI builds pass  
- [x] Review and approve

---

## Review 1 [Changes requested]

> Username: mloskot  
> Created_at: 2021-01-29 09:39:49 UTC  
> State: CHANGES_REQUESTED  
> Url: https://github.com/boostorg/gil/pull/552#pullrequestreview-579079238  

Thank you for your contribution.  
  
The implementation looks good to me. I just offered a few nitpicks :-)

📁 include/boost/gil/algorithm.hpp

```diff
 756 |+                             It2 first2, 
 757 |+                             It2 last2,
 758 |+                             planar_2_planar_type)
```

> Username: mloskot  
> Created_at: 2021-01-29 09:29:58 UTC  
> Updated_at: 2021-01-29 19:35:05 UTC  
> Url: https://github.com/boostorg/gil/pull/552#discussion_r566688788  

Did you perhaps formatted this with clang-format using our (preliminary) [.clang-format](https://github.com/boostorg/gil/tree/develop/example/clang-format) configuration?  
  
We strive to avoid (unnecessary) whitespace fields to the left.  
You could either try our `.clang-format` or manually make it more compact.  
  
In fact, in this particular case, the prototype will fit our [100 characters line limit recommendation](https://github.com/boostorg/gil/blob/develop/CONTRIBUTING.md#guidelines):  
  
```  
void uninitialized_copy_aux(It1 first1, It1 last1, It2 first2,  It2 last2, planar_2_planar_type)  
```

---

📁 include/boost/gil/algorithm.hpp

```diff
 740 |+     interleaved_2_planar,
 741 |+     mixed_2_interleaved
 742 |+ };
```

> Username: mloskot  
> Created_at: 2021-01-29 09:30:50 UTC  
> Updated_at: 2021-01-29 19:35:05 UTC  
> Url: https://github.com/boostorg/gil/pull/552#discussion_r566689316  

Please, make `_2_`  read `_to_`. We ask for descriptive names, even of lengthy, and avoidance of abbreviations where possible.

---

📁 include/boost/gil/algorithm.hpp

```diff
 821 |+                                         (is_planar<View1>::value ?
 822 |+                                             copy_planarity_condition::planar_2_planar:
 823 |+                                             copy_planarity_condition::interleaved_2_planar)>;
```

> Username: mloskot  
> Created_at: 2021-01-29 09:37:12 UTC  
> Updated_at: 2021-01-29 19:35:05 UTC  
> Url: https://github.com/boostorg/gil/pull/552#discussion_r566692970  

As I mentioned earlier, we try to lean the code to left, not to right (trying to keep line length limit to 100 characters) - there is still plenty of places where the code has not been re-formatted though.  
  
The `<` and `>` can work like `{` and `}`   
  
```cpp  
using vxv_planarity = std::integral_constant  
    <  
        copy_planarity_condition,  
        !is_planar<View2>::value  
            ? copy_planarity_condition::mixed_2_interleaved  
            : (is_planar<View1>::value   
                ? copy_planarity_condition::planar_2_planar  
                : copy_planarity_condition::interleaved_2_planar)  
    >;  
```  
  
following formatting like here, for example:  
  
https://github.com/boostorg/gil/blob/422ca82fe58d5aed90f86a2b043a402a2dc48a53/include/boost/gil/algorithm.hpp#L434-L454

---

📁 include/boost/gil/algorithm.hpp

```diff
 780 |-     using is_planar = std::integral_constant<bool, is_planar<View1>::value && is_planar<View2>::value>;
 817 |+     using copy_planarity_condition = detail::copy_planarity_condition;
 818 |+     using vxv_planarity = std::integral_constant<copy_planarity_condition,
```

> Username: mloskot  
> Created_at: 2021-01-29 09:39:24 UTC  
> Updated_at: 2021-01-29 19:35:05 UTC  
> Url: https://github.com/boostorg/gil/pull/552#discussion_r566694452  

What does the `vxv` stand for?

> Username: theroyn  
> Created_at: 2021-01-29 16:14:31 UTC  
> Updated_at: 2021-01-29 19:35:05 UTC  
> Url: https://github.com/boostorg/gil/pull/552#discussion_r566931872  

my bad, that's a placeholder name that I should've removed. It stood for 'value x value". I'll replace it with "copy_planarity_condition_type"


---

## Review 2 [Approved]

> Username: mloskot  
> Created_at: 2021-01-30 20:10:51 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/gil/pull/552#pullrequestreview-579804282  

LGTM. Thank you

---
