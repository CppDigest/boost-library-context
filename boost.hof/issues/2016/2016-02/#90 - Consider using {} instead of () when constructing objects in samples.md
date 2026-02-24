# #90 - Consider using {} instead of () when constructing objects in samples [Open]

> Username: tzlaine  
> Created at: 2016-02-09 14:58:10 UTC  
> Updated at: 2016-03-06 23:43:59 UTC  
> Url: https://github.com/boostorg/hof/issues/90  

For example, in this:  
  
```  
struct id  
{  
    template<class T>  
    T operator()(T x) const  
    {  
        return x;  
    }  
};  
  
auto int_result = boost::fit::result<int>(id());  
static_assert(std::is_same<decltype(int_result(true)), int>::value, "Not the same type");  
```  
  
Consider changing the second-to-last line to:  
  
`auto int_result = boost::fit::result<int>(id{});`  
  
This makes it obvious that the use of "id" is a construction, not a call.
