# #95 Fix gcc 8 warning. [Closed]

> Username: Romain-Geissler-1A  
> Created at: 2018-02-22 16:46:03 UTC  
> Updated at: 2018-09-08 12:27:35 UTC  
> Closed at: 2018-09-08 12:27:35 UTC  
> Url: https://github.com/boostorg/serialization/pull/95  

Hi,  
  
This fixes a very simple warning that gcc 8 throws with -Wall -Wextra -Werror:  
In file included from /data3/mwrep/rgeissler/SI/br_5-1.wk2/common/include/si/common/HTHSegmentationCacheInfoSerialization.h:20,  
                 from src/SEI_DUCommandGetInfoHTHSegmentationCacheExec.cpp:16:  
/remote/mw/combld/delivery/pack18.continuous/components.osp/osp/Boost/18-0-0-0/include/boost/archive/text_oarchive.hpp: In member function ‘void boost::archive::text_oarchive_impl<Archive>::save(const boost::archive::version_type&)’:   
/remote/mw/combld/delivery/pack18.continuous/components.osp/osp/Boost/18-0-0-0/include/boost/archive/text_oarchive.hpp:68:47: error: type qualifiers ignored on cast result type [-Werror=ignored-qualifiers]   
         save(static_cast<const unsigned int>(t));  
                                               ^  
/remote/mw/combld/delivery/pack18.continuous/components.osp/osp/Boost/18-0-0-0/include/boost/archive/text_oarchive.hpp: In member function ‘void boost::archive::text_oarchive_impl<Archive>::save(const boost::serialization::item_version_type&)’:      
/remote/mw/combld/delivery/pack18.continuous/components.osp/osp/Boost/18-0-0-0/include/boost/archive/text_oarchive.hpp:71:47: error: type qualifiers ignored on cast result type [-Werror=ignored-qualifiers]   
         save(static_cast<const unsigned int>(t));  
                                               ^  
  
Cheers,  
Romain

---

## Comment 1

> Username: robertramey  
> Created_at: 2018-04-30 20:20:29 UTC  
> Url: https://github.com/boostorg/serialization/pull/95#issuecomment-385516631  

personally I think the warning is wrong.

---

## Comment 2

> Username: Romain-Geissler-1A  
> Created_at: 2018-04-30 21:07:34 UTC  
> Updated_at: 2018-04-30 21:08:23 UTC  
> Url: https://github.com/boostorg/serialization/pull/95#issuecomment-385529131  

Hi,  
  
On my side I consider it similar to the warning you get in gcc for years when the return type of a function is "const int" rather than just "int" and consider it justified. You wrongly convey that somehow what you return is not modifiable while absolutely nothing prevents you to modify it anyway, and it has absolutely no nasty side effect to do it. Also in some cases it might simply mean you have forgotten to add a "&" or a "*" after the type, so it might find real errors.  
  
So what do we do with this ? I guess that if we consider this warning is not valid (which is not my case), then a bug should be opened to gcc developers. In the meantime, Boost users get this, so what do we do ?  
  
Cheers,  
Romain

---

## Comment 3

> Username: Flamefire  
> Created_at: 2018-05-01 12:53:44 UTC  
> Url: https://github.com/boostorg/serialization/pull/95#issuecomment-385664099  

The warning is a bit strange but actually valid according to the GCC docu:  
  
> -Wignored-qualifiers (C and C++ only)  
> --  
> Warn if the return type of a function has a type qualifier such as "const".  For ISO C such a type qualifier has no effect, since the value returned by a function is not an lvalue.  For C++, the warning is only emitted for scalar types or "void".  ISO C prohibits qualified "void" return types on function definitions, so such return types always receive a warning even without this option.  
  
However in template contexts this warning might become a nightmare... But in this case the fix is simple and valid. The `const` there is not really required.

---

## Comment 4

> Username: alfC  
> Created_at: 2018-05-01 14:49:40 UTC  
> Url: https://github.com/boostorg/serialization/pull/95#issuecomment-385689597  

If you use std::decay or Boost equivalent it is easy to remove qualifiers  
from the return type.  
  
On Tue, May 1, 2018, 05:53 Alexander Grund <notifications@github.com> wrote:  
  
> The warning is a bit strange but actually valid according to the GCC docu:  
>  
> -Wignored-qualifiers (C and C++ only)  
>  
> Warn if the return type of a function has a type qualifier such as  
> "const". For ISO C such a type qualifier has no effect, since the value  
> returned by a function is not an lvalue. For C++, the warning is only  
> emitted for scalar types or "void". ISO C prohibits qualified "void" return  
> types on function definitions, so such return types always receive a  
> warning even without this option.  
>  
> However in template contexts this warning might become a nightmare... But  
> in this case the fix is simple and valid. The const there is not really  
> required.  
>  
> —  
> You are receiving this because you are subscribed to this thread.  
> Reply to this email directly, view it on GitHub  
> <https://github.com/boostorg/serialization/pull/95#issuecomment-385664099>,  
> or mute the thread  
> <https://github.com/notifications/unsubscribe-auth/ACMfYLT80B3EDPpLRkGMYYf402SX0zTdks5tuFrYgaJpZM4SPoF3>  
> .  
>

---

## Comment 5

> Username: robertramey  
> Created_at: 2018-05-01 17:03:31 UTC  
> Url: https://github.com/boostorg/serialization/pull/95#issuecomment-385725176  

I've been unable to provoke the warning with my gcc 6.2 compiler even while adding -Wall -Wextra and  -Wignored-qualifiers  
  
Still, I'm thinking that the appropriate fix in this context would be to change  
save(static_cast<const unsigned int>(t));  
to  
save(static_cast<const unsigned int &>(t));

---

## Comment 6

> Username: Flamefire  
> Created_at: 2018-05-01 17:09:35 UTC  
> Url: https://github.com/boostorg/serialization/pull/95#issuecomment-385726827  

I found a few projects that have to deal with that since GCC 8, so it might be a stronger enforcement now.  
  
Is `<const unsigned int &>` even possible? The casts in this examples invoke the user defined conversion operator and that returns an `unsigned int`. And especially for small types: Why would you want them a (const) reference?

---

## Comment 7

> Username: robertramey  
> Created_at: 2018-05-01 17:37:46 UTC  
> Url: https://github.com/boostorg/serialization/pull/95#issuecomment-385735050  

the reason I did this is because here is the code snipped  
 `  
    template<class T>  
    void save(const T & t){  
        this->newtoken();  
        basic_text_oprimitive<std::ostream>::save(t);  
    }  
    void save(const version_type & t){  
        save(static_cast<const unsigned int &>(t));  
    }  
    void save(const boost::serialization::item_version_type & t){  
        save(static_cast<const unsigned int &>(t));  
    }  
`  
The intention is that save(version_type &t) be forwarded to save(const T & t) so it's my belief that the signatures should match.  I'm guessing that the warning comes about because the reference is transformed to an instance - where const is not necessary.  My intention is that no transformation actually occur - only a cast.  So I believe that this is correct and that this will eliminate the warning.  
  
Of course the real question is why included the specializations for version and item version in the first place.  I don't remember now.  It may have been a harmless mistake or some work around for some flakey compiler.  The serialization library contains a lot of those.  In any case, this would be considered an exercise for the reader.

---

## Comment 8

> Username: Flamefire  
> Created_at: 2018-05-01 17:54:50 UTC  
> Url: https://github.com/boostorg/serialization/pull/95#issuecomment-385739903  

`version_type` is a strong typedef (aka wrapped integer): https://github.com/boostorg/serialization/blob/eb35302b7658556e743f54a7bd8ddb17090fcf99/include/boost/archive/basic_archive.hpp#L75 Same for `item_version_type? : https://github.com/boostorg/serialization/blob/9a2c1f11ab1f35c0f1db4631cbdc0df432ccc9bb/include/boost/serialization/item_version_type.hpp#L25 This answers your question about the "why" they are there: To have a dedicated version type that may be handled differently than the underlying integral.  
  
So a conversion does occur: The types are not the same.  
  
The forwarding works either way. `const T&` also takes a `unsigned` or any other integral (if it has to the compiler will create a temporary reference but in this case it will be elided due to inlining later on)  
  
The warning (IMO) comes from the definition of the function `static_cast` as something like `template<class T, class U> T static_cast(U val)` (I guess the GCC guys at least treat it like this). So substituting the types yields: `const unsigned static_cast(version_type val)`. So yes we have an integral instance where `const` is not required. I even believe that this is a GCC bug, as a cast like that should be allowed without warning, but whatever.  
  
Bottom line: We need a conversion, not cast. IMO simply `unsigned` is a better choice as it is shorter/simpler. I'm not even sure if `const unsigned&` would work as there is only a conversion to `unsigned` in the class.

---

## Comment 9

> Username: robertramey  
> Created_at: 2018-05-01 18:41:46 UTC  
> Url: https://github.com/boostorg/serialization/pull/95#issuecomment-385752802  

>This answers your question about the "why" they are there: To have a dedicated version type that may be handled differently than the underlying integral.  
  
That isn't my question.  My question why the safe specializations are there and not matched with the save(T ...

---

## Comment 10

> Username: Flamefire  
> Created_at: 2018-05-01 18:58:59 UTC  
> Url: https://github.com/boostorg/serialization/pull/95#issuecomment-385757561  

Because it is one easy solution. You could also override the `<<-operator` for `std::ostream` and those types as that is what is beeing called by `save`->`basic_text_oprimitive<std::ostream>::save`->`safe_impl` but I guess this was faster and more localized. Like "at this point we treat it like an int"

---

## Comment 11

> Username: robertramey  
> Created_at: 2018-05-01 19:21:21 UTC  
> Url: https://github.com/boostorg/serialization/pull/95#issuecomment-385763318  

I see it now.  I could have added a save function to item_version and item_version_type.  I probably considered this but specifically chose to do it this way- in any case I'm happy with my solution.  You should verify that it actually eliminates the warning as I can't reproduce it.

---

## Comment 12

> Username: Flamefire  
> Created_at: 2018-05-01 20:34:46 UTC  
> Url: https://github.com/boostorg/serialization/pull/95#issuecomment-385781797  

Still don't understand why you would want to convert the converted temporary to a reference instead of the simple `unsigned`. There is no difference that I can see and it is longer.  
  
@Romain-Geissler-1A would need to verify as I don't have GCC-8 either. Maybe add it to travis?

---

## Comment 13

> Username: robertramey  
> Created_at: 2018-05-01 20:59:33 UTC  
> Url: https://github.com/boostorg/serialization/pull/95#issuecomment-385788543  

the reason is that a I believe a cast from T -> T & will deference the original value.  Then when it's forwarded to the the save<T ... the address of that temporary will be passed rather than the address of the original.  If I cast to a reference, it just take the address the of version and transforms it to an address of an unsigned int - a zero cost compile time operation.   
a) I might have this wrong - but I don't think so  
b) since it's heavily inlined code and the compiler has all the information, it should optimize away just about everything so it should not matter.  
c) btw - all the other archive classes have the same issue. Not sure why it surfaces in only one of them.  Not that it matters now - I fixed them all.

---

## Comment 14

> Username: robertramey  
> Created_at: 2018-05-01 21:01:19 UTC  
> Url: https://github.com/boostorg/serialization/pull/95#issuecomment-385789020  

I'm not totally convinced about adding the -W switches to travis.  I can't guarantee no warnings because I'm working on multiple compilers and also the serialization library sometimes has to skate close to the edge of undefined behavior.  Some warnings are unavoidable.

---

## Comment 15

> Username: Flamefire  
> Created_at: 2018-05-01 21:17:46 UTC  
> Url: https://github.com/boostorg/serialization/pull/95#issuecomment-385793270  

A conversion from `const version_type&` to `const unsigned&` should not be directly possible because it has to invoke the conversion operator. So at that point it has an `unsigned`. If you `static_cast` that to a reference or bind it to the `const T&` argument should not matter. It is a 2nd conversion anyway. However everything is inlined anyway (especially the conversion, the whole class is effectively removed) so it doesn't matter

---

## Comment 16

> Username: Flamefire  
> Created_at: 2018-05-04 08:35:20 UTC  
> Url: https://github.com/boostorg/serialization/pull/95#issuecomment-386537719  

@robertramey Found something very relevant to this.  
  
I tried it in godbolt and using the conversion operator to `unsigned` does dereference the value. Even worse: Your version fails on GCC: https://godbolt.org/g/Ca9J7S This is a bug in various GCC versions: https://stackoverflow.com/questions/31428980/user-defined-conversion-function-and-casting-to-reference  
  
Note how the `const` conversion returns a `T` and the non-const a `T&`.  
  
There is a `BOOST_STRONG_TYPEDEF` define which could be used instead and would work (change the 2 const conversions). However that define is not used neither for `archive::version_type` nor for `serialization::item_version_type`.  
  
What works is `static_cast<unsigned>` for all cases. Note that a dereference DOES occur at that point but if you change the call to `use` to actually inline use it ( `std::cout << val`) then there is NO overhead.  
  
Summary: Either change the types to have a `operator const unsigned&` (manually or with `BOOST_STRONG_TYPEDEF`) or use `static_cast<unsigned>`

---

## Comment 17

> Username: Romain-Geissler-1A  
> Created_at: 2018-09-08 12:27:35 UTC  
> Url: https://github.com/boostorg/serialization/pull/95#issuecomment-419638559  

Changes were apparently released in Boost 1.68, closing this PR.

---
