# #283 - boost serialization can not be sc stoped in windows platform [Closed]

> Username: gucoi  
> Created at: 2023-05-19 07:50:00 UTC  
> Updated at: 2023-08-24 18:47:39 UTC  
> Closed at: 2023-08-24 18:47:39 UTC  
> Url: https://github.com/boostorg/serialization/issues/283  

i use boost serialization like below, but the class definition in the DLL, and exe used for communication between two ends, but when i use the sc stop to shutdown my windows exe, it throw a abort, like below this, i check the stack, not found my code in this.  
  
```C++  
struct base {  
    virtual std::string get_name () {  
        return STRUCT_STR(base);  
    }  
    template<class Archive>  
    void serialize(Archive &ar, const int version) {  
    }  
};  
  
struct A : public base {  
    virtual std::string get_name () {  
        return STRUCT_STR(A);  
    }  
    template<class Archive>  
    void serialize(Archive &ar, const int version) {  
        ar & boost::serialization::base_object<base>(*this);  
    }  
};  
  
struct B : public A {  
    B(int _e) {e = _e;}  
    B() {}  
    virtual std::string get_name () {  
        std::cout << e;  
        return STRUCT_STR(B);  
    }  
  
    template<class Archive>  
    void serialize(Archive &ar, const int version) {  
        ar & boost::serialization::base_object<A>(*this);  
        ar & e;  
        ar & p;  
    }  
  
    int e;  
    ProcessStatus p = UNINITIALIZED;  
};  
  
void register_oa_class_type(boost::archive::text_oarchive& oa) {  
    oa.register_type<base>();  
    oa.register_type<A>();  
    oa.register_type<B>();  
}  
  
void register_ia_class_type(boost::archive::text_iarchive& ia) {  
    ia.register_type<base>();  
    ia.register_type<A>();  
    ia.register_type<B>();  
}  
```  
abort  
```  
boost::serialization::singleton<std::set<boost::serialization::void_cast_detail::void_caster const * __ptr64,boost::serialization::void_cast_detail::void_caster_compare,std::allocator<boost::serialization::void_cast_detail::void_caster const * __ptr64> > >::is_destroyed  
```  
i don't know it is a issue or not, but in the linux platform, so -> binary, it's not found this question, if you have any answer about the this question or special usage in windows.  
  
Thanks very much.

---

## Comment 1

> Username: gucoi  
> Created at: 2023-05-19 07:51:39 UTC  
> Url: https://github.com/boostorg/serialization/issues/283#issuecomment-1554580771  

my boost version is the 1.79.0, windows is the AMD64

---

## Comment 2

> Username: robertramey  
> Created at: 2023-08-24 17:58:44 UTC  
> Url: https://github.com/boostorg/serialization/issues/283#issuecomment-1692172535  

Is this still a problem?  If so, please include a freestanding test which illustrates the problem.  Given what you've written above, this should not be difficult

---

## Comment 3

> Username: robertramey  
> Created at: 2023-08-24 18:47:39 UTC  
> Url: https://github.com/boostorg/serialization/issues/283#issuecomment-1692234696  

fixed and merged
