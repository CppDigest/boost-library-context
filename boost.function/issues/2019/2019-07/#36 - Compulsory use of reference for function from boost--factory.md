# #36 - Compulsory use of reference for function from boost::factory [Closed]

> Username: dalaen  
> Created at: 2019-07-10 16:06:04 UTC  
> Updated at: 2020-03-25 13:35:49 UTC  
> Closed at: 2020-03-25 13:35:49 UTC  
> Url: https://github.com/boostorg/function/issues/36  

Hi,  
  
I am migrating my code from an older Boost version to a newer, and am encountering a problem with the following code snippet (simplified for the purpose of this demo).  
  
Here's a snippet for this purpose:  
```  
#include <boost/function.hpp>  
#include <boost/functional/factory.hpp>  
  
struct MyClass {  
};  
  
struct MyCallback {  
   MyCallback(int z, int y) {}  
   MyCallback(MyClass* obj1) {}  
};  
  
int main()  
{  
   boost::function<void(MyClass*&)>::function(boost::factory<MyCallback*>());  
  
   auto c = boost::factory<MyCallback*>();  
   boost::function<void(int&, int&)>::function(c);  
  
   return 0;  
}  
```  
  
This shows two ways of implementing this, using objects and simple types.  
  
This code compiles, because there are references everywhere in the function signatures.  
  
However, the issue comes if I change the signature like this:  
`boost::function<void(MyClass*)>::function(boost::factory<MyCallback*>());`  
  
> boost-1.70.0\include\boost-1_70\boost\function\function_template.hpp(158): error C2664: 'MyCallback *boost::factory<MyCallback *,void,factory_alloc_for_pointee_and_deleter>::operator ()(void) const' : cannot convert argument 1 from 'MyClass *' to 'MyClass *&'  
  
`boost::function<void(int, int&)>::function(c);`  
  
> boost-1.70.0\include\boost-1_70\boost\function\function_template.hpp(158): error C2664: 'MyCallback *boost::factory<MyCallback *,void,factory_alloc_for_pointee_and_deleter>::operator ()(void) const' : cannot convert argument 1 from 'int' to 'int &'  
  
**The function template seems to require for every parameter in the function signature a _reference_, and no longer takes a simple parameter nor a pointer parameter.**  
  
After investigating quite a bit, it seems to come from *function_template.hpp:32*  
`#   define BOOST_FUNCTION_ARG(J,I,D) static_cast<BOOST_PP_CAT(T,I)&&>(BOOST_PP_CAT(a,I))`  
It looks like the static_cast<&&> is forcing the reference and not letting anything else through.  
The previous correction using boost::forward didn't work either.  
  
GCC on this matter gives the following error:  
> function_template.hpp:158:37: error: cannot bind non-const lvalue reference of type 'MyClass*&' to an rvalue of type 'MyClass*'  
  
But I don't quite see how MyClass* is treated as a rvalue here...  
  
There's also a BOOST_NO_CXX11_RVALUE_REFERENCES macro definition that'd circumvent the problem. But what's the link between Rvalue Reference allowance and forbidding any simple type & pointers?  
  
For further reference, here's the full compiler (MSVC12 2013) error output:  
  
> boost-1.70.0\include\boost-1_70\boost\function\function_template.hpp(158): error C2664: 'MyCallback *boost::factory<MyCallback *,void,factory_alloc_for_pointee_and_deleter>::operator ()(void) const' : cannot convert argument 1 from 'MyClass *' to 'MyClass *&'  
          boost-1.70.0\include\boost-1_70\boost\function\function_template.hpp(152) : while compiling class template member function 'void boost::detail::function::void_function_obj_invoker1<FunctionObj,R,T0>::invoke(boost::detail::function::function_buffer &,T0)'  
          with  
          [  
              FunctionObj=boost::factory<MyCallback *,void,factory_alloc_for_pointee_and_deleter>  
  ,            R=void  
  ,            T0=MyClass *  
          ]  
          boost-1.70.0\include\boost-1_70\boost\function\function_template.hpp(931) : see reference to function template instantiation 'void boost::detail::function::void_function_obj_invoker1<FunctionObj,R,T0>::invoke(boost::detail::function::function_buffer &,T0)' being compiled  
          with  
          [  
              FunctionObj=boost::factory<MyCallback *,void,factory_alloc_for_pointee_and_deleter>  
  ,            R=void  
  ,            T0=MyClass *  
          ]  
          boost-1.70.0\include\boost-1_70\boost\function\function_template.hpp(931) : see reference to class template instantiation 'boost::detail::function::void_function_obj_invoker1<FunctionObj,R,T0>' being compiled  
          with  
          [  
              FunctionObj=boost::factory<MyCallback *,void,factory_alloc_for_pointee_and_deleter>  
  ,            R=void  
  ,            T0=MyClass *  
          ]  
          boost-1.70.0\include\boost-1_70\boost\function\function_template.hpp(720) : see reference to function template instantiation 'void boost::function1<R,T0>::assign_to<Functor>(Functor)' being compiled  
          with  
          [  
              R=void  
  ,            T0=MyClass *  
  ,            Functor=boost::factory<MyCallback *,void,factory_alloc_for_pointee_and_deleter>  
          ]  
          boost-1.70.0\include\boost-1_70\boost\function\function_template.hpp(720) : see reference to function template instantiation 'void boost::function1<R,T0>::assign_to<Functor>(Functor)' being compiled  
          with  
          [  
              R=void  
  ,            T0=MyClass *  
  ,            Functor=boost::factory<MyCallback *,void,factory_alloc_for_pointee_and_deleter>  
          ]  
          boost-1.70.0\include\boost-1_70\boost\function\function_template.hpp(1069) : see reference to function template instantiation 'boost::function1<R,T0>::function1<boost::factory<MyCallback *,void,factory_alloc_for_pointee_and_deleter>>(Functor,int)' being compiled  
          with  
          [  
              R=void  
  ,            T0=MyClass *  
  ,            Functor=boost::factory<MyCallback *,void,factory_alloc_for_pointee_and_deleter>  
          ]  
          boost-1.70.0\include\boost-1_70\boost\function\function_template.hpp(1069) : see reference to function template instantiation 'boost::function1<R,T0>::function1<boost::factory<MyCallback *,void,factory_alloc_for_pointee_and_deleter>>(Functor,int)' being compiled  
          with  
          [  
              R=void  
  ,            T0=MyClass *  
  ,            Functor=boost::factory<MyCallback *,void,factory_alloc_for_pointee_and_deleter>  
          ]  
          testcpp11\consoleapplication1\consoleapplication1.cpp(62) : see reference to function template instantiation 'boost::function<void (MyClass *)>::function<boost::factory<MyCallback *,void,factory_alloc_for_pointee_and_deleter>>(Functor,int)' being compiled  
          with  
          [  
              Functor=boost::factory<MyCallback *,void,factory_alloc_for_pointee_and_deleter>  
          ]  
          testcpp11\consoleapplication1\consoleapplication1.cpp(62) : see reference to function template instantiation 'boost::function<void (MyClass *)>::function<boost::factory<MyCallback *,void,factory_alloc_for_pointee_and_deleter>>(Functor,int)' being compiled  
          with  
          [  
              Functor=boost::factory<MyCallback *,void,factory_alloc_for_pointee_and_deleter>  
          ]

---

## Comment 1

> Username: pdimov  
> Created at: 2019-07-10 16:32:41 UTC  
> Url: https://github.com/boostorg/function/issues/36#issuecomment-510135515  

Your example as written compiles under VS2019 for me and under g++ fails with  
```  
testbed2019.cpp: In function 'int main()':  
testbed2019.cpp:14:74: error: cannot call constructor 'boost::function<void(MyClass*&)>::function' directly [-fpermissive]  
  boost::function<void(MyClass*&)>::function(boost::factory<MyCallback*>());  
                                                                          ^  
testbed2019.cpp:14:74: note: for a function-style cast, remove the redundant '::function'  
testbed2019.cpp:17:47: error: cannot call constructor 'boost::function<void(int&, int&)>::function' directly [-fpermissive]  
  boost::function<void(int&, int&)>::function(c);  
                                               ^  
testbed2019.cpp:17:47: note: for a function-style cast, remove the redundant '::function'  
```  
which seems correct; not sure what the `::function` is supposed to be doing there.

---

## Comment 2

> Username: pdimov  
> Created at: 2019-07-10 16:34:48 UTC  
> Url: https://github.com/boostorg/function/issues/36#issuecomment-510136265  

The modified example:  
```  
	boost::function<void(MyClass*&)> f1((boost::factory<MyCallback*>()));  
  
	auto c = boost::factory<MyCallback*>();  
	boost::function<void(int&, int&)> f2(c);  
```  
compiles under g++. I'm using the develop branch though. I'll check with 1.70.

---

## Comment 3

> Username: pdimov  
> Created at: 2019-07-10 16:39:53 UTC  
> Url: https://github.com/boostorg/function/issues/36#issuecomment-510138113  

Works with 1.70 too. When I modify the example as follows:  
```  
#include <boost/function.hpp>  
#include <boost/functional/factory.hpp>  
#include <iostream>  
  
struct MyClass {  
};  
  
struct MyCallback {  
  
	MyCallback(int z, int y)  
	{  
		std::cout << "MyCallback(" << z << ", " << y << ")\n";  
	}  
  
	MyCallback(MyClass* obj1)  
	{  
		std::cout << "MyCallback(" << obj1 << ")\n";  
	}  
};  
  
int main()  
{  
	boost::function<void(MyClass*&)> f1((boost::factory<MyCallback*>()));  
  
	MyClass* pm = 0;  
	f1(pm);  
  
	auto c = boost::factory<MyCallback*>();  
	boost::function<void(int&, int&)> f2(c);  
  
	int z = 1, y = 2;  
	f2(z, y);  
  
	return 0;  
}  
```  
the output is as expected.

---

## Comment 4

> Username: dalaen  
> Created at: 2019-07-11 11:17:35 UTC  
> Url: https://github.com/boostorg/function/issues/36#issuecomment-510444174  

Thank you for getting back and correcting the code for GCC. I forgot that point.  
  
Sure, adding references in every function parameter given to boost::function<> solves the problem.  
  
But why are today accepted function signatures with only reference arguments, and not simple types or pointers?  
Why does boost::function accept only void(int&, MyClass*&, whatever&) and not void(int, MyClass*, whatever)?  
  
This seemed to be working prior to the introduction of this BOOST_NO_CXX11_RVALUE_REFERENCES, and therefore looks like a regression.  
Or is this new BOOST_NO_CXX11_RVALUE_REFERENCES a restriction to only use references in function signatures?

---

## Comment 5

> Username: pdimov  
> Created at: 2019-07-11 11:33:19 UTC  
> Url: https://github.com/boostorg/function/issues/36#issuecomment-510448505  

Ah, I see what you mean now. Let me try the example without the references.

---

## Comment 6

> Username: pdimov  
> Created at: 2019-07-11 11:41:20 UTC  
> Url: https://github.com/boostorg/function/issues/36#issuecomment-510450797  

The reason the code doesn't compile is that `boost::factory` is unable to take rvalues as arguments.  
```  
	auto c = boost::factory<MyCallback*>();  
	c(1, 2);  
```  
fails.  
  
This used to work before `boost::function` acquired support for rvalue references, because it passed lvalues to the contained function object even in the case it took rvalues. But we changed it to forward properly in order to support f.ex. `unique_ptr` arguments.  
  
The correct fix here is to change `boost::factory` to use perfect forwarding as well under C++11 and above.

---

## Comment 7

> Username: dalaen  
> Created at: 2019-07-11 12:09:12 UTC  
> Updated at: 2019-07-11 13:18:03 UTC  
> Url: https://github.com/boostorg/function/issues/36#issuecomment-510458976  

Alright, thanks for your insight!  
I saw your reference in `boost::factory`, thank you. Will follow up from there.

---

## Comment 8

> Username: pdimov  
> Created at: 2019-07-11 19:35:38 UTC  
> Url: https://github.com/boostorg/function/issues/36#issuecomment-510624346  

We won't be able to fix that for 1.71, which is now closed even for bug fixes, but hopefully 1.72 will be fine.

---

## Comment 9

> Username: glenfe  
> Created at: 2019-08-28 18:01:05 UTC  
> Url: https://github.com/boostorg/function/issues/36#issuecomment-525856413  

Fixed for 1.72.
