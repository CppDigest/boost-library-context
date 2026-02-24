# #75 - function_input_iterator can't be used with `boost::join` [Closed]

> Username: denzor200  
> Created at: 2022-11-09 14:52:51 UTC  
> Updated at: 2022-11-09 21:30:23 UTC  
> Closed at: 2022-11-09 21:29:22 UTC  
> Url: https://github.com/boostorg/iterator/issues/75  

```  
/opt/compiler-explorer/libs/boost_1_80_0/boost/range/concepts.hpp:160:26: error: no matching function for call to 'boost::iterators::function_input_iterator<generator, int>::function_input_iterator(boost::iterators::impl::function_input_iterator<generator, int>&)'  
  160 |                 Iterator i2(++i);  
      |                          ^~  
```  
Example: https://godbolt.org/z/o8hTEj5qx

---

## Comment 1

> Username: denzor200  
> Created at: 2022-11-09 14:55:38 UTC  
> Url: https://github.com/boostorg/iterator/issues/75#issuecomment-1308886667  

It looks like function_input_iterator's `operator++` returns value of unexpected internals type, but should returns value of `function_input_iterator` type

---

## Comment 2

> Username: Lastique  
> Created at: 2022-11-09 21:30:22 UTC  
> Url: https://github.com/boostorg/iterator/issues/75#issuecomment-1309398108  

Thanks for the report.
