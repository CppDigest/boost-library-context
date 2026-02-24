# #93 - intrusive_ptr aliasing constructor? [Open]

> Username: schaumb  
> Created at: 2022-04-21 16:59:17 UTC  
> Updated at: 2022-04-21 16:59:17 UTC  
> Url: https://github.com/boostorg/smart_ptr/issues/93  

Hi all,  
  
I want to ask that is it reliable to implement `intrusive_ptr` aliasing constructor, similar to `shared_ptr` existing one?  
  
Possible usage:  
```  
struct Test1 : boost::intrusive_ref_counter<Test1> {  
  int a;  
};  
  
struct Test2 : boost::intrusive_ref_counter<Test2> {  
  int b;  
};  
  
void fun(boost::intrusive_ptr<int> ctr);  
  
auto iptr1 = boost::intrusive_ptr<Test1>(new Test1);  
auto iptr2 = boost::intrusive_ptr<Test2>(new Test2);  
  
fun( {iptr1, &iptr1->a} );  
fun( {iptr2, &iptr2->b} );  
```
