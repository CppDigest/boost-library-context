# #89 - `BOOST_PFR_FUNCTIONS_FOR` doesn't nest [Open]

> Username: u3shit  
> Created at: 2021-07-31 18:32:45 UTC  
> Updated at: 2021-07-31 18:32:45 UTC  
> Url: https://github.com/boostorg/pfr/issues/89  

I don't know if this is a bug or feature, but `BOOST_PFR_FUNCTIONS_FOR` requires structure members to have an `std::hash` specialization, but it doesn't itself provide one, so the following example fails to compile:  
  
```c++  
#include <boost/pfr/functions_for.hpp>  
  
struct A { int a; };  
BOOST_PFR_FUNCTIONS_FOR(A);  
// namespace std { template<> struct hash<A> { size_t operator()(A); }; }  
  
struct B { A a; };  
BOOST_PFR_FUNCTIONS_FOR(B);  
```  
[Godbolt](https://godbolt.org/#g:!((g:!((g:!((h:codeEditor,i:(fontScale:14,fontUsePx:'0',j:1,lang:c%2B%2B,selection:(endColumn:4,endLineNumber:5,positionColumn:4,positionLineNumber:5,selectionStartColumn:4,selectionStartLineNumber:5,startColumn:4,startLineNumber:5),source:'%23include+%3Cboost/pfr/functions_for.hpp%3E%0A%0Astruct+A+%7B+int+a%3B+%7D%3B%0ABOOST_PFR_FUNCTIONS_FOR(A)%3B%0A//+namespace+std+%7B+template%3C%3E+struct+hash%3CA%3E+%7B+size_t+operator()(A)%3B+%7D%3B+%7D%0A%0Astruct+B+%7B+A+a%3B+%7D%3B%0ABOOST_PFR_FUNCTIONS_FOR(B)%3B'),l:'5',n:'0',o:'C%2B%2B+source+%231',t:'0')),k:50,l:'4',n:'0',o:'',s:0,t:'0'),(g:!((g:!((h:compiler,i:(compiler:g112,filters:(b:'0',binary:'1',commentOnly:'0',demangle:'0',directives:'0',execute:'1',intel:'0',libraryCode:'0',trim:'1'),flagsViewOpen:'1',fontScale:14,fontUsePx:'0',j:1,lang:c%2B%2B,libs:!((name:boost,ver:'176')),options:'-std%3Dc%2B%2B17',selection:(endColumn:1,endLineNumber:1,positionColumn:1,positionLineNumber:1,selectionStartColumn:1,selectionStartLineNumber:1,startColumn:1,startLineNumber:1),source:1),l:'5',n:'0',o:'x86-64+gcc+11.2+(Editor+%231,+Compiler+%231)+C%2B%2B',t:'0')),k:50,l:'4',m:22.319688109161792,n:'0',o:'',s:0,t:'0'),(g:!((h:output,i:(compiler:1,editor:1,fontScale:14,fontUsePx:'0',wrap:'1'),l:'5',n:'0',o:'Output+of+x86-64+gcc+11.2+(Compiler+%231)',t:'0')),header:(),l:'4',m:77.68031189083821,n:'0',o:'',s:0,t:'0')),k:50,l:'3',n:'0',o:'',t:'0')),l:'2',n:'0',o:'',t:'0')),version:4)  
  
Uncommenting the hash specialization will make this code compile, but it's bothersome to do this for every type that just needs some comparison and print operations. Would it be possible to have a `BOOST_PFR_FUNCTIONS_FOR` version that doesn't try to generate a hash function? (Especially that it generates a `hash_value` function that is only compatible with boost containers, not an `std::hash` specialization that is a more compatible solution with C++11 or later.) Or at least make `hash_fields` compatible with `hash_value`?
