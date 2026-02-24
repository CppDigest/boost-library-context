# #50 - Problem with MSVC 19.26.28806 [Closed]

> Username: JakaBac  
> Created at: 2020-07-06 15:57:44 UTC  
> Updated at: 2020-08-31 21:14:17 UTC  
> Closed at: 2020-08-31 21:14:17 UTC  
> Url: https://github.com/boostorg/intrusive/issues/50  

With fully updated MSVC (cl.exe version 19.26.28806)  
The below example produces an access violation.  
  
If the list template alias declaration is inside a the class the program crashes since it apparently incorrectly computes the offset of the list member hook, so adding g1 to the list overwrites its vtbl.  
  
On the other hand if the alias declaration appears outside the class after it has been declared it works OK.  
It also works OK if the alias declaration is inside Gadget class.  
  
I am not an expert on C++ standard but this seems like a compiler bug.  
I reported it to Microsoft:  
https://developercommunity.visualstudio.com/content/problem/1103773/problem-with-code-using-boost-intrusive-list.html  
  
```  
#include <iostream>  
#include <boost/intrusive/list.hpp>  
  
class Widget {  
public:    
    virtual ~Widget() {}  
    virtual void foo() = 0;  
  
    boost::intrusive::list_member_hook<  
        boost::intrusive::link_mode<boost::intrusive::normal_link>>  
        listHook_;  
  
    using List = boost::intrusive::list<  
        Widget,  
        boost::intrusive::member_hook<Widget, decltype(Widget::listHook_), &Widget::listHook_>>;  
};  
  
class Gadget : public Widget {  
public:  
    Gadget() {}  
    void foo() {     
        puts("hello");  
    }  
};  
  
  
using List = boost::intrusive::list<  
    Widget,   
    boost::intrusive::member_hook<Widget, decltype(Widget::listHook_), &Widget::listHook_>>;  
  
int main()  
{  
    Gadget g,g1;     
    Widget::List the_list_bad;  
    List the_list;  
    the_list.push_back(g);  
    the_list_bad.push_back(g1); //overwrites vtbl  
    auto& gl1 = the_list.front();  
    auto& gl2 = the_list_bad.front();   
    gl1.foo();  
    gl2.foo(); //crashes  
}  
```

---

## Comment 1

> Username: igaztanaga  
> Created at: 2020-08-31 21:14:17 UTC  
> Url: https://github.com/boostorg/intrusive/issues/50#issuecomment-684043851  

According to the reported issue, it's an ABI limitation, quoting:  
  
> Hi,  
>   
> Thanks for reporting the issue. This is a known ABI limitation.  
>   
> In MSVC, the pointer to member is represented by an offset, but the layout will change when we see the full definition (due to the virtual function table pointer). So the offset used during class definition becomes incorrect.  
>   
> You can observe the different representations of pointer to member between MSVC and GCC: the non-type template argument is shown as 'pointer-to-member(0x0)' in MSVC and '&Widget::listHook_' in GCC.  
>   
> Unfortunately, we can't fix ABI related issues because it will break binary compatibility (the offset is also encoded in the decorated name of the symbol).  
>   
> One possible workaround is to provide a dummy base (with a virtual dtor) to Widget. This way, MSVC doesn't need to change the layout of Widget because the virtual function table pointer is already in the base class.  
>   
> `struct` Dummy { virtual ~Dummy() {} };  
>   
> `class` Widget : Dummy {
