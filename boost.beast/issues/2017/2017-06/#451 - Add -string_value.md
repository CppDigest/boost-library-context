# #451 - Add "string_value" [Closed]

> Username: vinniefalco  
> Created at: 2017-06-09 15:07:19 UTC  
> Updated at: 2017-06-12 02:47:42 UTC  
> Closed at: 2017-06-12 02:47:42 UTC  
> Url: https://github.com/boostorg/beast/issues/451  

```  
class string_value  
{  
  static_string_n<64> buf;  
public:  
  // Constructor  
  string_value(string_view s);  
  
  // Constructor  
  template<class T, class = typename std::enable_if<  
    ! std::is_constructible<string_view, T>::value>::type>  
  string_value(T const& t)  
  {  
    // convert t using boost::lexical_cast<static_string_n>  
  }  
  
  // Returns the string  
  string_view  
  str() const;  
  
  operator string_view() const;  
};  
```  
  
Now any function that takes a `string_value` can be passed numbers, objects, whatever. This is for `basic_fields`
