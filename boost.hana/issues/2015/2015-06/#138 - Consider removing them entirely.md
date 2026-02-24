# #138 - [Operators] Consider removing them entirely [Closed]

> Username: ldionne  
> Created at: 2015-06-17 22:25:22 UTC  
> Updated at: 2015-06-21 13:32:36 UTC  
> Closed at: 2015-06-21 13:32:36 UTC  
> Url: https://github.com/boostorg/hana/issues/138  

There are several problems with concepts providing operators. First the choice of which operators are provided is arbitrary. Also, how to make these operators available to user code is a big problem. We could simply remove them and then each data type would document which operators it provides. This way, they would not be tied to any concept anymore.  
  
That would also fix #30.

---

## Comment 1

> Username: viboes  
> Created at: 2015-06-18 05:21:24 UTC  
> Url: https://github.com/boostorg/hana/issues/138#issuecomment-113041058  

I'm all for this. Haskel has user defined operators which makes it easier to choose a different one for each type class operation. The choice in C++ is much shorter and conflicting.  
  
This issue should be signaled in the Boost ML.  
  
In my prototype of opaque types [1] I used some mixin similar to what Boost.TypeErasure uses.  
  
``` c++  
class Identifier : opaque::new_class<Identifier, int,  
    mpl::vector<  
        opaque::using_totally_ordered  
    >  
{  
    // ...  
};  
  
```  
  
[1] https://github.com/viboes/opaque

---

## Comment 2

> Username: ldionne  
> Created at: 2015-06-19 22:50:43 UTC  
> Url: https://github.com/boostorg/hana/issues/138#issuecomment-113664406  

I'm gradually removing them. By the way, it is not because of some philosophical reason but simply because doing it generally in such a way that users could also use these operators is too hard, and I judge it is out of Hana's scope.
