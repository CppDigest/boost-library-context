# #104 - Boost Python List Append [Closed]

> Username: tfgsupport  
> Created at: 2017-02-03 10:39:38 UTC  
> Updated at: 2017-06-27 21:41:45 UTC  
> Closed at: 2017-06-27 21:41:45 UTC  
> Url: https://github.com/boostorg/python/issues/104  

Hi,  
  
I am using boost python to create a python list of my C++ class objects as below. Below is the error thrown when I call the append function. My thoughts are boost python is unable to ascertain the python object it should create from my C++ class object. How can I resolve this?  
  
**"TypeError: No to_python (by-value) converter found for C++ type: class MyClass",**  
  
```  
class MyClass(); // Class  
MyClass myClass; // Class instance  
  
boost::python::list list;  
list.append( myClass );  
  
BOOST_PYTHON_MODULE( Test )  
{  
     class_<MyClass>( "MyClass" )  
	def( "getSquare", &MyClass::getSquare)  
}  
  
```  
  
Thanks,  
  
Nilufar

---

## Comment 1

> Username: stefanseefeld  
> Created at: 2017-02-03 13:11:38 UTC  
> Url: https://github.com/boostorg/python/issues/104#issuecomment-277243396  

Hi Nilufar,  
your sample code seems very incomplete, as the error you are reporting refers to a class "MYNAMESPACE::Context", which is nowhere to be seen in the code snippet you pasted.

---

## Comment 2

> Username: tfgsupport  
> Created at: 2017-02-08 09:49:55 UTC  
> Url: https://github.com/boostorg/python/issues/104#issuecomment-278281086  

Hi,  
  
Apologies. I've updated the error to the one thrown by my simple test case above.  
  
I've found that you can pass a vector object to a python function from C++ directly, without the need for a boost python list, by exposing the vector using boost python and the vector_indexing_suite. Is there a way I can pass a set? My understanding is that boost python supports vectors, maps and hash_maps only.

---

## Comment 3

> Username: stefanseefeld  
> Created at: 2017-02-08 12:47:03 UTC  
> Url: https://github.com/boostorg/python/issues/104#issuecomment-278319898  

I'm sorry, I don't see what update you are talking about. As I mentioned, the code snippet from your previous comment is incomplete and wouldn't compile. (There is no declaration or definition of the "getSquare" member function anywhere, for example.  
Please submit a minimal self-contained test case so I can attempt to help.  
  
To answer your question: Boost.Python currently has no support for `set`, simply because no-one has added that yet. (It should be straight forward, and I'll try to get to it ASAP.)

---

## Comment 4

> Username: stefanseefeld  
> Created at: 2017-06-27 21:41:45 UTC  
> Url: https://github.com/boostorg/python/issues/104#issuecomment-311494594  

I'm going to close this issue as the code you posted is still invalid / incomplete. Feel free to re-open when you have a minimal self-contained test case.
