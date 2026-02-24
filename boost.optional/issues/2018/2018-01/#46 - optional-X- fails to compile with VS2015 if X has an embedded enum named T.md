# #46 - optional<X> fails to compile with VS2015 if X has an embedded enum named T [Closed]

> Username: petamas  
> Created at: 2018-01-03 09:21:17 UTC  
> Updated at: 2018-01-03 13:08:33 UTC  
> Closed at: 2018-01-03 13:08:33 UTC  
> Url: https://github.com/boostorg/optional/issues/46  

Example code:  
  
```  
struct STATUS  
{  
	enum T  
	{  
		DISCONNECTED,  
		CONNECTING,  
		CONNECTED,  
	};  
  
	T mValue;  
};  
  
void test_member_T()  
{  
  boost::optional<STATUS> x = STATUS();  
  x->mValue = STATUS::CONNECTED;  
    
  BOOST_TEST(x->mValue == STATUS::CONNECTED);  
}  
```  
  
Compile error:  
```  
d:\boost\boost_git\boost/optional/optional.hpp(753): error C2838: '{dtor}': illegal qualified name in member declaration  
  
d:\boost\boost_git\boost/optional/optional.hpp(753): note: while compiling class template member function 'void boost::optional_detail::optional_base<T>::destroy_impl(void)'  
        with  
        [  
            T=STATUS  
        ]  
d:\boost\boost_git\boost/optional/optional.hpp(739): note: see reference to function template instantiation 'void boost::optional_detail::optional_base<T>::destroy_impl(void)' being compiled  
        with  
        [  
            T=STATUS  
        ]  
d:\boost\boost_git\boost/optional/optional.hpp(831): note: see reference to class template instantiation 'boost::optional_detail::optional_base<T>' being compiled  
        with  
        [  
            T=STATUS  
        ]  
test\optional_test_member_T.cpp(34): note: see reference to class template instantiation 'boost::optional<STATUS>' being compiled  
d:\boost\boost_git\boost/optional/optional.hpp(753): error C2274: 'function-style cast': illegal as right side of '.' operator  
```

---

## Comment 1

> Username: petamas  
> Created at: 2018-01-03 09:21:43 UTC  
> Url: https://github.com/boostorg/optional/issues/46#issuecomment-354967125  

I've got a fix for this, will send pull request in a few days

---

## Comment 2

> Username: petamas  
> Created at: 2018-01-03 10:33:33 UTC  
> Url: https://github.com/boostorg/optional/issues/46#issuecomment-354980792  

Pull request sent: https://github.com/boostorg/optional/pull/47
