# #709 - (encoded_)set_path should bypass edit_segments [Closed]

> Username: alandefreitas  
> Created at: 2023-03-08 00:53:29 UTC  
> Updated at: 2023-03-10 04:23:37 UTC  
> Closed at: 2023-03-10 04:23:37 UTC  
> Url: https://github.com/boostorg/url/issues/709  

The library has the convention that `"/"` (string_view) -> `{}` (segments) and `"/./"` (string_view) -> `{""}` (segments). Functions that go through `edit_segments` (`insert`, `replace`, ...) apply prefixes as required to maintain the Sequence representation. Meanwhile, normalization might not respect the mapping from segments -> Sequence.  
  
`url::set_path` and `url::set_encoded_path`, on the other hand, has always assumed it can reuse `edit_segments` but that's incorrect because it should set the complete path string to exactly what the user asked for instead of attempting to maintain their sequence representation. Not fixing this leads to weird results such as `assert(url("").set_path("/./") == "/././")`.   
  
Thus, `set_path` should think in terms of the path string and only include extra prefixes where invalid urls would result. On the other hand, `edit_segments` should only be used when thinking in terms of the sequence: when we want the mapping of the path to the sequence.  
  
Besides these wrong results, making `edit_segments` do two different things is dangerous, setting the complete path at once is more efficient, and removing workarounds from `edit_segments` will also make it more efficient.
