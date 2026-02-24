# #168 - Enforce valid XML tags in NVP [Closed]

> Username: correaa  
> Created at: 2019-08-17 07:58:20 UTC  
> Updated at: 2019-09-20 19:18:42 UTC  
> Closed at: 2019-09-20 19:18:42 UTC  
> Url: https://github.com/boostorg/serialization/issues/168  

In some situations it is possible to compile code that will generate XML tags that are invalid.  
  
For example:  
```  
template<class Ar> void serialize(Ar& ar, int){  
   ar & (this->member); // compiles but generates an invalid XML tag ("this->member").  
}  
```  
  
The validity of the tag can be checked at runtime by the library.  
  
It can also be (over) constrained at compile time by redefining `BOOST_SERIALIZATION_NVP` as   
  
```  
#define BOOST_SERIALIZATION_NVP(name)                              \  
    ([](){struct name{};}), boost::serialization::make_nvp(BOOST_PP_STRINGIZE(name), name))  
```  
Since valid C++ names are a subset of the possible XML tags.  
  
(Original problem: https://stackoverflow.com/a/25602424/225186)

---

## Comment 1

> Username: robertramey  
> Created at: 2019-08-19 16:30:02 UTC  
> Url: https://github.com/boostorg/serialization/issues/168#issuecomment-522653476  

> template<class Ar> void serialize(Ar& ar, int){  
>    ar & (this->member); // compiles but generates an invalid XML tag ("this->member").  
> }  
  
Hmmm - doesn't this fail to compile for an xml_archive for lack of an NVP wrapper?  
  
> #define BOOST_SERIALIZATION_NVP(name)                              \  
>     ([](){struct name{};}), boost::serialization::make_nvp(BOOST_PP_STRINGIZE(name), name))  
  
I'd have to think about what this does.  In any case, using it would then require C++11+ .  I don't think this would be a great hit with many users.  One could condition it on the usage of C++11 but then the system would operate differently depending on the compiler.  So it seems introducing this would make things more complex.  And of course require enhancement to the documentation to explain the "unexpected" errors when moving to different compilers.

---

## Comment 2

> Username: alfC  
> Created at: 2019-08-19 16:47:34 UTC  
> Url: https://github.com/boostorg/serialization/issues/168#issuecomment-522660088  

On Mon, Aug 19, 2019 at 09:30 Robert Ramey <notifications@github.com> wrote:  
  
> template void serialize(Ar& ar, int){  
> ar & (this->member); // compiles but generates an invalid XML tag  
> ("this->member").  
> }  
>  
> Hmmm - doesn't this fail to compile for an xml_archive for lack of an NVP  
> wrapper?  
>  
  
Yes, sorry the example should read `ar &  
BOOST_SERIALIZATION_NVP(this->member);  
  
This is just and example of what can go wrong. Parenthesis, semicolons and  
angle brackets can describe valid references but are invalid xml tags (to  
be discovered at runtime.  
  
  
#define BOOST_SERIALIZATION_NVP(name)  
> ({struct name{};}),  
> boost::serialization::make_nvp(BOOST_PP_STRINGIZE(name), name))  
>  
> I'd have to think about what this does.  
>  
  
It defines a struct with name “name” inside a lambda. The lambda returns  
void, which is the ignored because is at the left of the (non overloaded)  
comma operator with nvp. For this to compile, name has to be a valid  
structure name, for example no spaces. In turn this is a valid xml tag.  
  
> In any case, using it would then require C++11+ . I don't think this would  
> be a great hit with many users. One could condition it on the usage of  
> C++11 but then the system would operate differently depending on the  
> compiler. So it seems introducing this would make things more complex. And  
> of course require enhancement to the documentation to explain the  
> "unexpected" errors when moving to different compilers.  
>  
  
Yes, this is just an example on how to solve it. Maybe there is a similar  
solution that doesn’t depend on C++11.  
  
  
> —  
> You are receiving this because you are subscribed to this thread.  
> Reply to this email directly, view it on GitHub  
> <https://github.com/boostorg/serialization/issues/168?email_source=notifications&email_token=AARR6YGTUAIJGYILM6GPHKDQFLDAXA5CNFSM4IMOU7F2YY3PNVWWK3TUL52HS4DFVREXG43VMVBW63LNMVXHJKTDN5WW2ZLOORPWSZGOD4TQ6JA#issuecomment-522653476>,  
> or mute the thread  
> <https://github.com/notifications/unsubscribe-auth/AARR6YEDJC76JS4XJGGIVILQFLDAXANCNFSM4IMOU7FQ>  
> .  
>
