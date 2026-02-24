# #4 - Hash support breaks Hash requirement [Closed]

> Username: lingfors  
> Created at: 2019-10-07 10:33:08 UTC  
> Updated at: 2019-10-09 16:29:59 UTC  
> Closed at: 2019-10-09 16:29:59 UTC  
> Url: https://github.com/boostorg/flyweight/issues/4  

According to cppreference.com:  
  
"All evaluations of h(k) executed within a given execution of a program (since C++14) yield the same result for the same value of k."  
  
However, since boost::flyweight only hash the address of the flyweight, the hash value might change for a given value k if the underlying flyweight had been released and then re-allocated.

---

## Comment 1

> Username: joaquintides  
> Created at: 2019-10-07 11:12:47 UTC  
> Updated at: 2019-10-07 11:13:25 UTC  
> Url: https://github.com/boostorg/flyweight/issues/4#issuecomment-538957111  

Hi,  
  
`boost::flyweight` equality is defined in terms of _identity_ (not equality) of the underlying value. This, coupled with required stability of factory entries (factories are the components responsible for storing the underlying values associated to flyweights), implies that:  
  
- `h(f)` remains the same during the lifetime of `f` (for `f` of some `boost::flyweight` type and hash of type `std::hash<decltype(f)>`, unless of course if  `f` has been reassigned.  
- if `f1==f2` then `h(f1)==h(f2)`.  
  
On the other hand, two flyweights `f1` and `f2` _whose lifetimes don't overlap_ and are associated to an equivalent underlying value do not necessarily have the same hash:  
  
    #include <boost/flyweight.hpp>  
    #include <functional>  
    #include <iostream>  
      
    int main()  
    {  
      using flyweight=boost::flyweight<int>;  
      using hasher=std::hash<flyweight>;  
        
      std::size_t h1,h2;  
      
      {  
        flyweight f1{0};  
        h1=hasher{}(f1);  
      } // f1 destroyed  
      {  
        flyweight dummy{1}, f2{0};  
        h2=hasher{}(f2);  
      }  
        
      // h1 and h2 will usually be different  
      std::cout<<"h1: "<<h1<<"\n"    
               <<"h2: "<<h2<<"\n";  
    }  
  
According to the semantics of `boost::flyweight`, `f1` and `f2` are just _not_ the same value. If you want to achieve hash stability across non-overlapping lifetimes, you can use [`boost::flyweights::no_tracking`](https://www.boost.org/libs/flyweight/doc/reference/tracking.html#no_tracking) to ensure underlying values are never destroyed.  
  
Does this help? What's your actual problem?  
  
Best,

---

## Comment 2

> Username: lingfors  
> Created at: 2019-10-07 12:15:49 UTC  
> Updated at: 2019-10-07 12:16:30 UTC  
> Url: https://github.com/boostorg/flyweight/issues/4#issuecomment-538981226  

My "problem" is that it seems to break the standard's requirement on std::hash. :)  
  
Consider the following:  
  
```  
auto hashValueX = std::hash<Person>(Person{"FirstName", "LastName"});  
auto hashValueY = std::hash<Person>(Person{"FirstName", "LastName"});  
  
assert(hashValueX == hashValueY);  
```  
  
I would assume this assertion to hold. And it does hold, if Person is using std::string to store first and last name. However, it might not hold if someone has decided to optimize Person by using boost::flyweight to manage first and last name.  
  
Sure, using no_tracking to avoid releasing the memory would solve the problem, but now we have another problem of not releasing unused memory instead.  
  
Now, I admit that I have a hard time coming up with a realistic scenario where this might actually cause problems. However, other people might come up with such a scenario, and then, if they use boost::flyweight, their code would be silently broken because boost::flyweight is broken.  
  
Now, I realize that changing the default behavior is probably not backwards compatible. But it would be nice to maybe have another option to force the hashing functions to forward the hashing to the underlying type? I think that would be necessary to cleanly interop with hashing on, say, containers.  
  
Using identity as a basis for equality is another discussion point, but for boost::flyweight it I guess it works. However, I have seen other cases where identity was used in place of a proper solution, and that code was obviously broken...

---

## Comment 3

> Username: joaquintides  
> Created at: 2019-10-07 15:18:29 UTC  
> Url: https://github.com/boostorg/flyweight/issues/4#issuecomment-539063405  

> My "problem" is that it seems to break the standard's requirement on std::hash. :)  
  
My reading of the situation is that `std::hash<boost::flyweight<...>>` certainly meets [_Cpp17Hash_ requirements](http://eelis.net/c++draft/hash.requirements) and is also compatible with equality (as defined) for use in [unordered associative containers](http://eelis.net/c++draft/unord.req#5). So, I don't think the issue needs fixing.  
  
That said, if a user comes up with a scenario where hash stability is needed beyond flyweight lifetime, it is very simple to just specialize the offending `std::hash` instantiation with whatever alternative semantics needed. I tend to think this is in fact simpler than having to specify a different hashing policy at flyweight definition time --my argument being that if the user is actually aware of the potential problem with hashing then she's already 99% on her way to solve it.

---

## Comment 4

> Username: joaquintides  
> Created at: 2019-10-09 16:29:59 UTC  
> Url: https://github.com/boostorg/flyweight/issues/4#issuecomment-540080423  

Closing as there are no further comments.
