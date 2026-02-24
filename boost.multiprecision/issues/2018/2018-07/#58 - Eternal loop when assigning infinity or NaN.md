# #58 - Eternal loop when assigning infinity or NaN [Closed]

> Username: jzmaddock  
> Created at: 2018-07-31 17:23:04 UTC  
> Updated at: 2018-08-11 18:52:20 UTC  
> Closed at: 2018-08-11 18:52:20 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/58  

The following statement gets into an eternal loop in release builds (where NDEBUG is defined):  
  
`boost::multiprecision::int128_t inf_i(std::numeric_limits<double>::infinity());`  
  
This is caught by a BOOST_ASSERT otherwise. Same is true for NaN.  
  
Question: should the assignment not simply assign 'infinity' instead (or throw an exception)n in such cases?
