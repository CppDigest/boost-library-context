# #94 - boost::variant(const char*->bool) [Open]

> Username: apolukhin  
> Created at: 2021-09-20 17:32:32 UTC  
> Updated at: 2021-09-20 17:32:32 UTC  
> Url: https://github.com/boostorg/variant/issues/94  

```  
	boost::variant<int, long, double, bool, string> v1("11");  
	cout << v1.type().name()<<endl; //bool******************why?  
	boost::variant<int, long, double, bool, string> v2(string("11"));  
	cout << v2.type().name() << endl;//class std::basic_string<char,struct std::char_traits<char>,class std::allocator<char> >  
	return 0;  
```  
  
Issue was imported from [Trac 13079](https://svn.boost.org/trac10/ticket/13079)
