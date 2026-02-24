# #1154 - Erase field iterator [Closed]

> Username: asvald  
> Created at: 2018-06-08 07:46:50 UTC  
> Updated at: 2018-06-08 15:57:36 UTC  
> Closed at: 2018-06-08 09:16:50 UTC  
> Url: https://github.com/boostorg/beast/issues/1154  

Hello, recently I encountered the following problem. Code for reproduce:  
```  
		auto& packet = msg.httpFlow().response.parser().get();  
		auto rang = packet.equal_range(boost::beast::string_view("Set-Cookie"));  
		for (auto s = rang.first; s != rang.second; s++)  
		{  
			s = packet.erase(s);  
		}  
```  
Version of beast #define BOOST_BEAST_VERSION 144  
This is what the compiler says:  
> 1>d:\program files\boost_1_66_0\boost\beast\http\impl\fields.ipp(608): error C2039: 'iter': is not a member of 'boost::intrusive::list_iterator<boost::intrusive::mhtraits<Parent,MemberHook,pointer-to-member(0x0)>,true>'  
> 1>        with  
> 1>        [  
> 1>            Parent=boost::beast::http::basic_fields<std::allocator<char>>::value_type,  
> 1>            MemberHook=boost::intrusive::list_member_hook<boost::intrusive::link_mode<boost::intrusive::normal_link>,void,void>  
> 1>        ]  
> 1>d:\program files\boost_1_66_0\boost\intrusive\list.hpp(99): note: see declaration of 'boost::intrusive::list_iterator<boost::intrusive::mhtraits<Parent,MemberHook,pointer-to-member(0x0)>,true>'  
> 1>        with  
> 1>        [  
> 1>            Parent=boost::beast::http::basic_fields<std::allocator<char>>::value_type,  
> 1>            MemberHook=boost::intrusive::list_member_hook<boost::intrusive::link_mode<boost::intrusive::normal_link>,void,void>  
> 1>        ]  
> 1>d:\program files\boost_1_66_0\boost\beast\http\impl\fields.ipp(607): note: while compiling class template member function 'boost::intrusive::list_iterator<boost::intrusive::mhtraits<Parent,MemberHook,pointer-to-member(0x0)>,true> boost::beast::http::basic_fields<std::allocator<char>>::erase(boost::intrusive::list_iterator<boost::intrusive::mhtraits<Parent,MemberHook,pointer-to-member(0x0)>,true>)'  
> 1>        with  
> 1>        [  
> 1>            Parent=boost::beast::http::basic_fields<std::allocator<char>>::value_type,  
> 1>            MemberHook=boost::intrusive::list_member_hook<boost::intrusive::link_mode<boost::intrusive::normal_link>,void,void>  
> 1>        ]  
> 1>d:\program files\gm\ta_dll\trafan\sslfilter.cpp(60): note: see reference to function template instantiation 'boost::intrusive::list_iterator<boost::intrusive::mhtraits<Parent,MemberHook,pointer-to-member(0x0)>,true> boost::beast::http::basic_fields<std::allocator<char>>::erase(boost::intrusive::list_iterator<boost::intrusive::mhtraits<Parent,MemberHook,pointer-to-member(0x0)>,true>)' being compiled  
> 1>        with  
> 1>        [  
> 1>            Parent=boost::beast::http::basic_fields<std::allocator<char>>::value_type,  
> 1>            MemberHook=boost::intrusive::list_member_hook<boost::intrusive::link_mode<boost::intrusive::normal_link>,void,void>  
> 1>        ]  
> 1>d:\program files\boost_1_66_0\boost\beast\http\impl\fields.ipp(609): error C3536: 'next': cannot be used before it is initialized  
> 1>d:\program files\boost_1_66_0\boost\beast\http\impl\fields.ipp(609): error C2100: illegal indirection  
> 1>d:\program files\boost_1_66_0\boost\beast\http\impl\fields.ipp(609): error C2440: 'initializing': cannot convert from 'int' to 'int &'  
> 1>d:\program files\boost_1_66_0\boost\beast\http\impl\fields.ipp(610): error C2664: 'unsigned int boost::intrusive::bstree_impl<ValueTraits,VoidOrKeyOfValue,Compare,SizeType,true,boost::intrusive::RbTreeAlgorithms,HeaderHolder>::erase(const boost::beast::http::basic_fields<std::allocator<char>>::value_type &)': cannot convert argument 1 from 'int' to 'boost::intrusive::tree_iterator<boost::intrusive::mhtraits<Parent,MemberHook,pointer-to-member(0x8)>,true>'  
> 1>        with  
> 1>        [  
> 1>            ValueTraits=boost::intrusive::mhtraits<boost::beast::http::basic_fields<std::allocator<char>>::value_type,boost::intrusive::set_member_hook<boost::intrusive::link_mode<boost::intrusive::normal_link>,void,void,void>,pointer-to-member(0x8)>,  
> 1>            VoidOrKeyOfValue=boost::intrusive::bstree_defaults::key_of_value,  
> 1>            Compare=boost::beast::http::basic_fields<std::allocator<char>>::key_compare,  
> 1>            SizeType=boost::intrusive::bstree_defaults::size_type,  
> 1>            HeaderHolder=boost::intrusive::bstree_defaults::header_holder_type  
> 1>        ]  
> 1>        and  
> 1>        [  
> 1>            Parent=boost::beast::http::basic_fields<std::allocator<char>>::value_type,  
> 1>            MemberHook=boost::intrusive::set_member_hook<boost::intrusive::link_mode<boost::intrusive::normal_link>,void,void,void>  
> 1>        ]  
> 1>d:\program files\boost_1_66_0\boost\beast\http\impl\fields.ipp(610): note: No constructor could take the source type, or constructor overload resolution was ambiguous  
> 1>d:\program files\boost_1_66_0\boost\beast\http\impl\fields.ipp(611): error C2664: 'boost::intrusive::list_iterator<boost::intrusive::mhtraits<Parent,MemberHook,pointer-to-member(0x0)>,false> boost::intrusive::list_impl<boost::intrusive::mhtraits<Parent,MemberHook,pointer-to-member(0x0)>,boost::intrusive::list_defaults::size_type,false,boost::intrusive::list_defaults::header_holder_type>::erase(boost::intrusive::list_iterator<boost::intrusive::mhtraits<Parent,MemberHook,pointer-to-member(0x0)>,true>,boost::intrusive::list_iterator<boost::intrusive::mhtraits<Parent,MemberHook,pointer-to-member(0x0)>,true>,unsigned int)': cannot convert argument 1 from 'int' to 'boost::intrusive::list_iterator<boost::intrusive::mhtraits<Parent,MemberHook,pointer-to-member(0x0)>,true>'  
> 1>        with  
> 1>        [  
> 1>            Parent=boost::beast::http::basic_fields<std::allocator<char>>::value_type,  
> 1>            MemberHook=boost::intrusive::list_member_hook<boost::intrusive::link_mode<boost::intrusive::normal_link>,void,void>  
> 1>        ]  
> 1>d:\program files\boost_1_66_0\boost\beast\http\impl\fields.ipp(611): note: No constructor could take the source type, or constructor overload resolution was ambiguous  
> 1>d:\program files\boost_1_66_0\boost\beast\http\impl\fields.ipp(612): error C2664: 'void boost::beast::http::basic_fields<std::allocator<char>>::delete_element(boost::beast::http::basic_fields<std::allocator<char>>::value_type &)': cannot convert argument 1 from 'int' to 'boost::beast::http::basic_fields<std::allocator<char>>::value_type &'  
> 1>d:\program files\boost_1_66_0\boost\beast\http\impl\fields.ipp(613): error C2440: 'return': cannot convert from 'int' to 'boost::intrusive::list_iterator<boost::intrusive::mhtraits<Parent,MemberHook,pointer-to-member(0x0)>,true>'  
> 1>        with  
> 1>        [  
> 1>            Parent=boost::beast::http::basic_fields<std::allocator<char>>::value_type,  
> 1>            MemberHook=boost::intrusive::list_member_hook<boost::intrusive::link_mode<boost::intrusive::normal_link>,void,void>  
> 1>        ]  
> 1>d:\program files\boost_1_66_0\boost\beast\http\impl\fields.ipp(613): note: No constructor could take the source type, or constructor overload resolution was ambiguous

---

## Comment 1

> Username: asvald  
> Created at: 2018-06-08 09:16:36 UTC  
> Url: https://github.com/boostorg/beast/issues/1154#issuecomment-395702268  

Already fixed in 1.67

---

## Comment 2

> Username: vinniefalco  
> Created at: 2018-06-08 15:57:35 UTC  
> Url: https://github.com/boostorg/beast/issues/1154#issuecomment-395806242  

Glad to hear it! By the way, you can use the overload of `erase` which takes a field and reduce your code to one line:  
```  
msg.httpFlow().response.parser().get().erase(beast::http::field::set_cookie);  
```  
  
Or you can use a string:  
```  
msg.httpFlow().response.parser().get().erase("Set-Cookie");  
```
