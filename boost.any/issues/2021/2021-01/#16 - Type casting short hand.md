# #16 - Type casting short hand [Closed]

> Username: aaangeletakis  
> Created at: 2021-01-18 22:12:13 UTC  
> Updated at: 2021-01-20 07:41:19 UTC  
> Closed at: 2021-01-20 07:41:19 UTC  
> Url: https://github.com/boostorg/any/issues/16  

Please implement the below  
```c++  
template <typename ValueType>  
constexpr operator ValueType() const {return boost::any_cast<ValueType>(*this);}  
```  
As it makes the code look nicer  
```c++  
std::string foo = "Hello World!\n";  
boost::any value = foo;  
std::cout << (std::string)value;  
```

---

## Comment 1

> Username: apolukhin  
> Created at: 2021-01-20 07:41:19 UTC  
> Url: https://github.com/boostorg/any/issues/16#issuecomment-763403891  

* Implicit casts provoke hard detectable errors.  
* C-style casts `(Type)value` provoke hard detectable errors, use static_cast/dynamic_cast  
  
Your example becomes   
```  
std::string foo = "Hello World!\n";  
boost::any value = foo;  
std::cout << static_cast<std::string>(value);  
```  
Which is not better than the current approach.  
  
Finally, adding functionality that does not exist in std::any makes the migration to the standard type much harder.  
  
Sorry, the feature woukd not be implemented.
