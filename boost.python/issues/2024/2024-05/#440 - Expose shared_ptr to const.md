# #440 - Expose shared_ptr to const [Closed]

> Username: MasterBe  
> Created at: 2024-05-27 18:00:06 UTC  
> Updated at: 2024-05-31 23:58:13 UTC  
> Closed at: 2024-05-31 23:58:13 UTC  
> Url: https://github.com/boostorg/python/issues/440  

Hello,  
  
I am trying to expose ```std::shared_ptr<const Foo>``` and especially in my case I need ```std::vector<std::shared_ptr<const Foo>>```. I tried:  
1. ```boost::python::implicitly_convertible<std::shared_ptr<Foo>,std::shared_ptr<const Foo>>();```  
2. ```boost::python::register_ptr_to_python<std::shared_ptr<const Foo>>();```  
  
Both 1 and 2 don't work in jupyter notebook getting error: ```TypeError: No to_python (by-value) converter found for C++ type```. Could you please advise on how to achieve this.
