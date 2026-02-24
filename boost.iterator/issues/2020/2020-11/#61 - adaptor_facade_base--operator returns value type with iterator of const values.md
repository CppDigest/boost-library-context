# #61 - adaptor_facade_base::operator[] returns value type with iterator of const values [Closed]

> Username: MC-DeltaT  
> Created at: 2020-11-19 03:55:25 UTC  
> Updated at: 2025-06-09 02:17:13 UTC  
> Closed at: 2025-06-09 02:08:50 UTC  
> Url: https://github.com/boostorg/iterator/issues/61  

`iterator_facade_base::operator[]` returns the iterator's value type if the iterated values are const and POD, as determined by `boost::iterators::detail::operator_brackets_result`. This results in subtle object lifetime errors in places where a reference is expected.  
  
For example, if you use such an iterator with `iterator_range`, `iterator_range::operator[]` becomes UB, as it attempts to return the reference type:  
  
    std::vector<double> data{1.0, 3.14, 42.7, 2.618, 9.8};  
    std::vector<std::size_t> indices{1, 0, 2, 1, 3, 2, 4, 3};  
  
    auto const begin = boost::make_permutation_iterator(data.cbegin(), indices.cbegin());  
    auto const end = boost::make_permutation_iterator(data.cbegin(), indices.cend());  
  
    auto const range = boost::make_iterator_range(begin, end);  
    auto const& e = range[0];   // Returns reference to temporary, UB  
  
Is there any reason why in this case `iterator_facade_base::operator[]` cannot return the reference type, or even a proxy object, instead?

---

## Comment 1

> Username: Lastique  
> Created at: 2025-06-09 00:15:38 UTC  
> Url: https://github.com/boostorg/iterator/issues/61#issuecomment-2954352343  

> Is there any reason why in this case `iterator_facade_base::operator[]` cannot return the reference type, or even a proxy object, instead?  
  
`operator[]` is basically an equivalent of `{ auto it_copy = *this + index; return *it_copy; }`. For iterators that embed the value (e.g. `counting_iterator`, `function_input_iterator`) this would return a dangling reference. This means `operator[]` must return a value or, if the iterator supports modification, a proxy that contains a copy of the iterator.  
  
I understand this may be surprising for cases when the iterator does not embed the value, but there currently is no way to tell whether the value is embedded or not, so `iterator_facade` has taken the conservative route.

---

## Comment 2

> Username: Lastique  
> Created at: 2025-06-09 02:17:12 UTC  
> Url: https://github.com/boostorg/iterator/issues/61#issuecomment-2954462955  

I've made the `operator[]` always return a proxy so that it is possible to obtain a reference to the value pointed to by the iterator. It won't work well with `auto` and templates, but at least it is now possible to do this:  
  
```  
iterator_reference_t< iterator > ref = it[n];  
```
