# #181 - Provide a substitute to BOOST_FUSION_ADAPT_STRUCT_xxx macros [Open]

> Username: gnzlbg  
> Created at: 2015-09-18 13:52:58 UTC  
> Updated at: 2015-10-26 23:36:20 UTC  
> Url: https://github.com/boostorg/hana/issues/181  

Would it be possible to provide a substitute for `BOOST_FUSION_ADAPT_STRUCT_xxx` macros to be able to use classes and structs as heterogeneous (+associative?) sequences?  
  
I don't know if there are any C++>=11 features that would allow improving these macros or designing a new/better version of them, but that might be worth looking into.

---

## Comment 1

> Username: ldionne  
> Created at: 2015-09-19 15:05:28 UTC  
> Url: https://github.com/boostorg/hana/issues/181#issuecomment-141678401  

The initial idea was that user-defined types were models of the `Struct` concept, not some kind of heterogeneous sequence themselves. However, one could convert any `Struct` to a heterogeneous sequence or a map with e.g. `to<tuple_tag>(user_defined_type)` or `to<map_tag>(user_defined_type)`. Unfortunately, this currently requires copying the members of the user defined type, so you can't e.g. modify the members of the struct in place as-if they were a sequence right now. That problem should be resolved with issue #175.  
  
Do you see another reason to allow seeing a user defined type as an heterogeneous sequence directly, rather than using the intermediate `Struct` concept? The advantage of the `Struct` concept over seeing a UDT directly as a map is that `Struct`s specify the order of their members to be the same as inside the UDT's definition, whereas a map does not enforce any ordering.

---

## Comment 2

> Username: ldionne  
> Created at: 2015-10-26 22:04:38 UTC  
> Url: https://github.com/boostorg/hana/issues/181#issuecomment-151297551  

@gnzlbg ping

---

## Comment 3

> Username: gnzlbg  
> Created at: 2015-10-26 23:36:20 UTC  
> Url: https://github.com/boostorg/hana/issues/181#issuecomment-151314479  

> The advantage of the Struct concept over seeing a UDT directly as a map is that Structs specify the order of their members to be the same as inside the UDT's definition, whereas a map does not enforce any ordering.  
  
I think that is a pretty important advantage that might turn out to be very useful for serialization (and thus worth the trouble, but see below).   
  
> Unfortunately, this currently requires copying the members of the user defined type, so you can't e.g. modify the members of the struct in place as-if they were a sequence right now. That problem should be resolved with issue #175.  
  
That wouldn't really be acceptable but it seems that it would only be temporary and that there is a plan to fix it.   
  
> Do you see another reason to allow seeing a user defined type as an heterogeneous sequence directly, rather than using the intermediate Struct concept?  
  
IIUC one will be able to create an intermediate model of Struct with value/&/const&/&& semantics from an UDT. Heterogeneous algorithms would work on the intermediate model of Struct, and allow   &/const&/&& access to the struct. Is that what you have in mind?   
  
If so, I don't see any reason to allow seeing an user defined type as an heterogeneous sequence (one can just write non-member non-friends functions that "adapt" the UDT as a model of Struct by returning an intermediate type).  
  
I've tried to stretch my imagination but even in the hypothetical case that types might ever be able to use heterogeneous algorithms "on themselves", then they will probably also be able to adapt themselves as a model of Struct and work on that. So I am not really able to find an use case where allowing an user defined type to be an heterogeneous would be required.
