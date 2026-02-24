# #8 added 'construct_custodian_for' and test [Open]

> Username: ukoethe  
> Created at: 2014-05-11 11:24:51 UTC  
> Updated at: 2015-03-26 13:12:22 UTC  
> Url: https://github.com/boostorg/python/pull/8  

(note: I will update the documentation as well when this pull request gets accepted)  
  
This pull request proposes a fix for the long-standing problem that `with_custodian_and_ward_postcall` doesn't work in connection with `make_constructor` (see e.g. https://mail.python.org/pipermail/cplusplus-sig/2007-June/012145.html).  
  
Consider the following class definitions:  
  
``` c++  
struct A {  
    int i_;  
  
    A(int i)  
    : i_(i)  
    {}  
};  
  
struct B {  
    A * a_;  
  
    B(A * a)  
    : a_(a)  
    {}  
  
    int a_content()   
    {  
        return a_ == 0 ? 0 : a_->i_;  
    }  
};  
  
B * create_B(A * a)  
{  
    return new B(a);  
}      
```  
  
and the corresponding boost::python glue code  
  
``` c++  
BOOST_PYTHON_MODULE(test_custodian_and_ward)  
{  
    class_<A>("A", no_init)  
        .def(init<int>())  
        // #1  
        .def("create_B", &create_B, with_custodian_and_ward_postcall<0,1,  
                                    return_value_policy<manage_new_object> >())  
        ;  
  
    class_<B>("B1", no_init)  
        // #2  
        .def(init<A*>()[with_custodian_and_ward_postcall<1,2>()])  
        .def("a_content", &B::a_content)  
        ;  
  
    class_<B>("B2", no_init)  
        // #3  
        .def("__init__", make_constructor(&create_B, with_custodian_and_ward_postcall<1,2>()))  
        .def("a_content", &B::a_content)  
        ;  
  
    class_<B>("B3", no_init)  
        // #4  
        .def("__init__", make_constructor(&create_B, with_custodian_and_ward_postcall<0,1>()))  
        .def("a_content", &B::a_content)  
        ;  
}  
```  
  
In this code  
- `#1` works as desired  
- `#2` works, but is a bit counter-intuitive because indexing suddenly starts at 1  
- `#3` causes an "argument index out of range" error  
- `#4` runs, but fails to establish the desired custodian-and-ward relationship (i.e. `B3.a_content()` may crash because the containd `A` instance can be deleted prematurely)  
  
The pull request introduces a new policy `construct_custodian_for` which solves problems `#2-4`. It is used like this:  
  
``` c++  
BOOST_PYTHON_MODULE(test_custodian_and_ward)  
{  
    ...  
  
    class_<B>("B1", no_init)  
        .def(init<A*>()[construct_custodian_for<1>()])  
        .def("a_content", &B::a_content)  
        ;  
  
    class_<B>("B2", no_init)  
        .def("__init__", make_constructor(&create_B, construct_custodian_for<1>()))  
        .def("a_content", &B::a_content)  
        ;  
}  
```

---

## Comment 1

> Username: hotgloupi  
> Created_at: 2014-09-21 18:42:40 UTC  
> Updated_at: 2014-09-21 18:46:13 UTC  
> Url: https://github.com/boostorg/python/pull/8#issuecomment-56308173  

Sorry to ask that here, but how do you actually test that the correct custodian-and-ward relationship is established or not ?  
  
In the example 4, it seems that the problem will appear, or not, depending on how long B3.a_content is referenced in python. Do you have advice on how prevent or debug such misbehavior ?   
  
EDIT: I just spotted the sys.getrefcount for the testing

---

## Comment 2

> Username: hotgloupi  
> Created_at: 2014-11-04 09:01:24 UTC  
> Url: https://github.com/boostorg/python/pull/8#issuecomment-61610643  

Bump !  
Why this doesn't get merged (or approved) ? It's backward compatible, well explained and improve readability !

---

## Comment 3

> Username: ukoethe  
> Created_at: 2014-11-04 11:33:31 UTC  
> Url: https://github.com/boostorg/python/pull/8#issuecomment-61627031  

I'm wondering if this might be the wrong repository. Where is the main boost::python development going on?

---

## Comment 4

> Username: hotgloupi  
> Created_at: 2014-11-04 11:41:44 UTC  
> Updated_at: 2014-11-04 11:43:30 UTC  
> Url: https://github.com/boostorg/python/pull/8#issuecomment-61627811  

According to [this](https://svn.boost.org/trac/boost/wiki/TryModBoost) page, it seems to be the right place. However, boost.python development is not really active (sadly), perhaps contributors that do not wish to invest time anymore should leave so we can see who is actually committed to this project.

---

## Comment 5

> Username: stefanseefeld  
> Created_at: 2015-03-26 13:12:22 UTC  
> Url: https://github.com/boostorg/python/pull/8#issuecomment-86507806  

Hi,  
  
this is definitely the right place for such discussions and patches.  
  
The explanation is very clear, and the code looks sensible, too. I'll try to find some time to test it, and (hopefully) eventually merge it.  
  
Thanks !

---
