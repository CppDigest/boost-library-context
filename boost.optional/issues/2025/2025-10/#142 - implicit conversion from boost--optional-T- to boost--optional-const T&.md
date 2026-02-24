# #142 - implicit conversion from boost::optional<T> to boost::optional<const T&> [Closed]

> Username: dmenendez-gruposantander  
> Created at: 2025-10-29 11:29:22 UTC  
> Updated at: 2026-02-20 15:21:54 UTC  
> Closed at: 2026-02-20 15:21:54 UTC  
> Url: https://github.com/boostorg/optional/issues/142  

I am using boost::optional<const T&> to pass parameters by "optional const-reference", to replace some uses of raw pointers.  
  
Old code:  
```  
void f(const A* a) {  
    if (a) ... // do something with *a  
    else ... // do something else without *a  
}  
  
// client code  
void g1(const A& a) { f( &a ); }  
void g2() { f( nullptr ); }  
void g3(boost::optional<A> a) { f( a.get_ptr() ); }  
```  
  
New code:  
```  
void f(boost::optional<const A&> a) {  
    if (a) ... // do something with *a  
    else ... // do something else without *a  
}  
  
// client code  
void g1(const A& a) { f( a ); }  
void g2() { f( boost::none ); }  
void g3(boost::optional<A> a) { f(  ???  ); }  
```  
  
The simplest way I see right now is to make the conversion explicitly at each call site using boost::optional<T>::map()   
```  
a.map( [](const A& x) -> const A& {return x;} )  
```  
which I consider a bit redundant.  
  
AFAIU there is no implicit conversion from `boost::optional<T>` to `boost::optional<const T&>`, but there could be, to make these use cases easier.  
  
What do you think about this feature request?

---

## Comment 1

> Username: akrzemi1  
> Created at: 2025-11-03 22:53:42 UTC  
> Url: https://github.com/boostorg/optional/issues/142#issuecomment-3482932370  

Hi @dmenendez-gruposantander, thank you for the report. Upon first glance, this enhancement makes perfect sense to me.  
  
It should be noted that `boost::optional<T&>` avoids binding to rvalues (as per [this docs](https://www.boost.org/doc/libs/latest/libs/optional/doc/html/boost_optional/design/optional_references.html)), so while the following would work:  
  
```c++  
void f(boost::opitonal<const T&> ot);  
  
int main() {  
  const boost::optional<T> ot {};  
  f(ot);  
}  
```     
  
the following would not:  
  
```c++  
boost::optional<T> make_T();  
  
int main() {  
  f(make_T()); // Error  
}  
```  
  
Would that still work for you?

---

## Comment 2

> Username: akrzemi1  
> Created at: 2025-11-04 01:25:11 UTC  
> Url: https://github.com/boostorg/optional/issues/142#issuecomment-3483304294  

More comprehensively, that would be:  
```c++  
optional<T&>       o = declval< optional<T>& >();  // OK  
  
optional<const T&> o = declval< optional<T>& >();  // OK  
optional<const T&> o = declval< optional<const T>& >();  // OK  
optional<const T&> o = declval< const optional<T>& >();  // OK  
optional<const T&> o = declval< const optional<const T>& >();  // OK  
  
optional<T&>       o = declval< optional<T> >();  // Error: binding reference to a temporary  
optional<const T&> o = declval< optional<T> >();  // Error: binding reference to a temporary  
  
optional<T&>       o = declval< optional<const T>& >();  // Error: binding to reference to const   
optional<T&>       o = declval< const optional<T>& >();  // Error: binding to reference to const   
```

---

## Comment 3

> Username: akrzemi1  
> Created at: 2026-02-20 15:21:54 UTC  
> Url: https://github.com/boostorg/optional/issues/142#issuecomment-3935565825  

This has now been implemented in develop and master; targeting Boost release 1.91.
