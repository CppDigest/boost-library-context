# #71 - Memoryleak in combination with std::set [Closed]

> Username: geraldmwangi  
> Created at: 2019-10-11 12:02:20 UTC  
> Updated at: 2019-10-14 11:55:17 UTC  
> Closed at: 2019-10-14 11:55:17 UTC  
> Url: https://github.com/boostorg/smart_ptr/issues/71  

## Subject: Memory leak in boost smart_ptr 1.65   
I have discovered a strange bug/memory leak when combining shared_ptr with std::set.   
The following sample code is self contained, all you need is:  
## Dependencies  
Linux (I tested it on Ubuntu 18.04)  
libboost1.65-dev from the repos  
valgrind  
  
## Sample Code  
[boostBug.zip](https://github.com/boostorg/smart_ptr/files/3717378/boostBug.zip)

---

## Comment 1

> Username: geraldmwangi  
> Created at: 2019-10-11 12:02:46 UTC  
> Url: https://github.com/boostorg/smart_ptr/issues/71#issuecomment-541035637  

The same leak also occurs with std::vector

---

## Comment 2

> Username: geraldmwangi  
> Created at: 2019-10-11 12:14:02 UTC  
> Url: https://github.com/boostorg/smart_ptr/issues/71#issuecomment-541039122  

In line 83 in sharedptr.cpp: Forget the comment "//cl3 takes ownership of cl". I wanted to design it that way (so that I don't have to delete cl ) but that would have complicated the code, so I KISS

---

## Comment 3

> Username: glenfe  
> Created at: 2019-10-11 17:14:56 UTC  
> Url: https://github.com/boostorg/smart_ptr/issues/71#issuecomment-541149101  

You're creating a cycle with your `candidateCluster->elements = elements;` assignment. The problem you have can effectively be reduced to the following (which doesn't even involve `std::set` at all):  
  
```  
#include <boost/make_shared.hpp>  
  
class S {  
public:  
    typedef boost::shared_ptr<S> P;  
    void f(P p) {  
        p_ = p;  
        p->p_ = p_;  
    }  
private:  
    P p_;  
    char c_[3000000];  
};  
  
int main()  
{  
    S::P x = boost::make_shared<S>();  
    S::P y = boost::make_shared<S>();  
    y->f(x);  
}  
```

---

## Comment 4

> Username: geraldmwangi  
> Created at: 2019-10-14 07:26:17 UTC  
> Updated at: 2019-10-14 10:03:19 UTC  
> Url: https://github.com/boostorg/smart_ptr/issues/71#issuecomment-541530588  

But why does this leak? If you substitute the shared ptr's with ordinary c++ pointers:  
  
```  
#include <boost/make_shared.hpp>  
  
class S {  
public:  
    typedef S* P; //boost::shared_ptr<S> P;  
    S()  
    {  
            p_=0;  
    }  
    void f(P p) {  
        p_ = p;  
        p->p_ = p_;  
    }  
private:  
    P p_;  
    char c_[3000000];  
};  
  
int main()  
{  
    S::P x = new S();// boost::make_shared<S>();  
    S::P y = new S(); // boost::make_shared<S>();  
    y->f(x);  
    delete x;  
    delete y;  
}  
```  
  
  
  
 it won't leak despite the member `x.p_ `pointing to `x` itself after `y->f(x)`.  
Is the constructor of class S called multiple times when copying shared_ptr's of type `boost::shared_ptr<S>`?

---

## Comment 5

> Username: geraldmwangi  
> Created at: 2019-10-14 09:55:13 UTC  
> Url: https://github.com/boostorg/smart_ptr/issues/71#issuecomment-541586406  

Thanks for taking the time to break down my code to a simpler form :)

---

## Comment 6

> Username: geraldmwangi  
> Created at: 2019-10-14 10:39:14 UTC  
> Updated at: 2019-10-14 10:41:21 UTC  
> Url: https://github.com/boostorg/smart_ptr/issues/71#issuecomment-541604461  

Ok got it now after bringing it to the point:  
```  
#include <boost/make_shared.hpp>  
  
class S {  
public:  
    typedef boost::shared_ptr<S> P;  
    P p_;  
    char c_[3000000];  
};  
  
int main()  
{  
    S::P x = boost::make_shared<S>();  
    x->p_ = x;  
}   
```  
The object which x points to holds a pointer to itself and thus the memory cannot be freed.  
Thanks alot
