# #282 Simplify no wstreambuf support. [Merged]

> Username: jzmaddock  
> Created at: 2023-04-29 12:38:32 UTC  
> Updated at: 2023-08-23 18:52:32 UTC  
> Merged at: 2023-08-23 18:52:32 UTC  
> Closed at: 2023-08-23 18:52:32 UTC  
> Url: https://github.com/boostorg/serialization/pull/282  

See https://github.com/boostorg/serialization/pull/224.

---

## Comment 1

> Username: jzmaddock  
> Created_at: 2023-04-30 08:15:43 UTC  
> Url: https://github.com/boostorg/serialization/pull/282#issuecomment-1528967144  

@robertramey : Looks like the CI is stalled indefinitely waiting for a runner - that's because the ubuntu-18 image has been deprecated (for example) and available runners are stupidly scarce.  Shall I update the images used as part of this PR?  The catch is that older gcc versions will no longer be available.  They *are* available with the drone CI runners that the cpp alliance provides for us though...

---

## Comment 2

> Username: robertramey  
> Created_at: 2023-04-30 10:23:33 UTC  
> Url: https://github.com/boostorg/serialization/pull/282#issuecomment-1528989814  

Don’t worry about it. Boost I never really works for me anyway. I’ll except the PR, sync, my own machine, and run the tests locally. I’m sure this will be fine with a change this modest. Many thanks to you both for your help on these very Obscure issues. Robert Rameywww.rrsd.comOn Apr 30, 2023, at 1:15 AM, jzmaddock ***@***.***> wrote:﻿  
@robertramey : Looks like the CI is stalled indefinitely waiting for a runner - that's because the ubuntu-18 image has been deprecated (for example) and available runners are stupidly scarce.  Shall I update the images used as part of this PR?  The catch is that older gcc versions will no longer be available.  They are available with the drone CI runners that the cpp alliance provides for us though...  
  
—Reply to this email directly, view it on GitHub, or unsubscribe.You are receiving this because you were mentioned.Message ID: ***@***.***>

---

## Comment 3

> Username: robertramey  
> Created_at: 2023-05-02 18:01:16 UTC  
> Url: https://github.com/boostorg/serialization/pull/282#issuecomment-1531910400  

looking more carefully at the PR I find a number of statements similar to the following:  
  
[ test-bsl-run_files test_helper_support : : : [ requires std_wstreambuf ] ]  
  
OK.  But when I look at the source for test_helper_support , I don't see any reference to wide streams or wchar or anything else related.  Why is the "requires" statement required here specifically?  How does one know whether it's going to be needed or not?

---

## Comment 4

> Username: jzmaddock  
> Created_at: 2023-05-02 18:04:07 UTC  
> Url: https://github.com/boostorg/serialization/pull/282#issuecomment-1531914773  

@robertramey, I added the requirement to all of the tests which had linker errors when building the tests with -DBOOST_NO_STD_LOCALE.  Basically anything which used the xml_archives anywhere.

---

## Comment 5

> Username: ArielSilver  
> Created_at: 2023-07-18 14:59:28 UTC  
> Updated_at: 2023-07-18 15:07:58 UTC  
> Url: https://github.com/boostorg/serialization/pull/282#issuecomment-1640393983  

**solution of this, _### boost serialization can not be sc stoped in windows platform #283_  
  
**  
```  
  
`struct base {  
virtual std::string get_name () {  
return STRUCT_STR(base);  
}  
template  
void serialize(Archive &ar, const int version) {  
}  
};  
  
struct A : public base {  
virtual std::string get_name () {  
return STRUCT_STR(A);  
}  
template  
void serialize(Archive &ar, const int version) {  
ar & boost::serialization::base_object(*this);  
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
oa.register_type();  
oa.register_type();  
oa.register_type();  
}  
  
void register_ia_class_type(boost::archive::text_iarchive& ia) {  
ia.register_type();  
ia.register_type();  
ia.register_type();  
}`  
```

---
