# #68 - function_output_iterator does not support specialization [Closed]

> Username: livicitu  
> Created at: 2021-09-06 07:54:30 UTC  
> Updated at: 2024-10-30 18:13:59 UTC  
> Closed at: 2024-10-30 18:13:57 UTC  
> Url: https://github.com/boostorg/iterator/issues/68  

I have a vector of **std::byte** and I want to call **boost::unhex** for this vector by creating a custom output iterator with my own unary function to convert `unsiged char` to `std::byte`. Note that `std::byte` is not an integral type so I cannot pass it directly to **boost::unhex**  
It is possible to create a `function_output_iterator` where we can specify the `value_type` (ex, `function_output_iterator<unsigned char>`)?  
The alternative I have is to create a vector of `unsigned char` then copy it into a vector of `std::byte` but this is what I want to avoid.  
  
This is what I tried:  
```  
	const std::string hexString = "0A";  
	auto outputVector = std::vector<std::byte>{};  
	auto unary_func = [&](const unsigned char& c) { outputVector .push_back(std::byte{c}); };  
	auto func_out_iterator = boost::make_function_output_iterator(unary_func);  
	boost::algorithm::unhex(hexString, func_out_iterator );  
```  
and I got:  
```  
 error C2893: Failed to specialize function template 'boost::enable_if<boost::is_integral<hex_iterator_traits<OutputIterator>::value_type>,OutputIterator>::type boost::algorithm::detail::decode_one(InputIterator &,InputIterator,OutputIterator,EndPred)'  
```

---

## Comment 1

> Username: Lastique  
> Created at: 2024-10-30 18:13:57 UTC  
> Url: https://github.com/boostorg/iterator/issues/68#issuecomment-2447981900  

`function_output_iterator` has an output iterator category, and for such iterators [`value_type` may be defined to `void`](http://eel.is/c++draft/iterator.requirements#iterator.traits-1).  
  
I think, it would be more reasonable to add support for explicitly specifying parsed integer type to `boost::algorithm::unhex` rather than add a workaround to `function_output_iterator` as this will enable support for all kinds of output iterators. Please, create an issue for [Boost.Algorithm](https://github.com/boostorg/algorithm/issues).
