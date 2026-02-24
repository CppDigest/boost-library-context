# #152 - Provide version of BOOST_HANA_ADAPT_STRUCT not requiring member types [Closed]

> Username: jbytheway  
> Created at: 2015-06-23 02:16:05 UTC  
> Updated at: 2015-09-24 08:24:43 UTC  
> Closed at: 2015-06-28 19:07:25 UTC  
> Url: https://github.com/boostorg/hana/issues/152  

Whenever I use Fusion's ADAPT_STRUCT macro, the first thing I do is wrap it with my own version where one does not have to provide the types (since they can be fetched with decltype).  Could hana provide this version?  
  
So, from your example:  
  
```  
BOOST_HANA_ADAPT_STRUCT(ns::Person,  
    (std::string, name),  
    (int, age)  
);  
```  
  
would become  
  
```  
BOOST_HANA_ADAPT_STRUCT_ALT(ns::Person,  
    (name),  
    (age)  
);  
```  
  
or even  
  
```  
BOOST_HANA_ADAPT_STRUCT_ALT(ns::Person,  
    name,  
    age  
);  
```  
  
This also avoids any awkward problems with types having commas in their names.  
  
Maybe even get rid of the existing one entirely?  I can't think of a use for it.

---

## Comment 1

> Username: ldionne  
> Created at: 2015-06-24 21:09:22 UTC  
> Url: https://github.com/boostorg/hana/issues/152#issuecomment-115013020  

> This also avoids any awkward problems with types having commas in their names.  
  
This is handled properly by the current macros.  
  
> Maybe even get rid of the existing one entirely? I can't think of a use for it.  
  
I wanted the usage of `BOOST_HANA_ADAPT_STRUCT` and `BOOST_HANA_DEFINE_STRUCT` to be as close as possible.

---

## Comment 2

> Username: ldionne  
> Created at: 2015-06-27 19:27:31 UTC  
> Url: https://github.com/boostorg/hana/issues/152#issuecomment-116121993  

Do you think the consistency gain is worth the extra verbosity?  
In that case, we could also modify `BOOST_HANA_DEFINE_STRUCT` to make it as follows:  
  
``` c++  
struct Person {  
    BOOST_HANA_DEFINE_STRUCT(  
        (int, age),  
        (std::string, name)  
    );  
};  
```  
  
Notice that we don't have to pass the name of the struct anymore.

---

## Comment 3

> Username: jbytheway  
> Created at: 2015-06-28 17:57:42 UTC  
> Url: https://github.com/boostorg/hana/issues/152#issuecomment-116303950  

I think I would strive for DRY over consistency.  So indeed, I would prefer the less verbose versions of both of these (although check what happens with empty structs if you make that change to DEFINE_STRUCT).

---

## Comment 4

> Username: ldionne  
> Created at: 2015-06-28 18:11:33 UTC  
> Url: https://github.com/boostorg/hana/issues/152#issuecomment-116304866  

> (although check what happens with empty structs if you make that change to DEFINE_STRUCT).  
  
Crap; I think empty structs can't be detected if we go that way. Is there a way to support empty structs then?

---

## Comment 5

> Username: jbytheway  
> Created at: 2015-06-28 18:26:55 UTC  
> Url: https://github.com/boostorg/hana/issues/152#issuecomment-116310150  

You can use one of the imperfect-but-probably-good-enough emptiness-detection macros (like the (undocumented?) BOOST_PP_IS_EMPTY); I think Boost.VMD may help here.  Or use PP_SEQs rather than variadics.  
  
Or just not support empty structs with that macro.

---

## Comment 6

> Username: ldionne  
> Created at: 2015-06-28 19:03:59 UTC  
> Url: https://github.com/boostorg/hana/issues/152#issuecomment-116314618  

I think I'll leave the name of the struct there, because the gain is not that big and it would make the two macros even more inconsistent. This way, we'll just have to remember that `BOOST_HANA_ADAPT_STRUCT` requires no member types.

---

## Comment 7

> Username: ldionne  
> Created at: 2015-06-28 19:07:25 UTC  
> Updated at: 2015-06-28 19:07:59 UTC  
> Url: https://github.com/boostorg/hana/issues/152#issuecomment-116315676  

Also, I don't want to just provide a version of `BOOST_HANA_ADAPT_STRUCT` that does not require member types, because I don't want the users to have to make a choice in this case. That's why I changed `BOOST_HANA_ADAPT_STRUCT` instead of providing a new one.

---

## Comment 8

> Username: daminetreg  
> Created at: 2015-09-24 08:24:43 UTC  
> Url: https://github.com/boostorg/hana/issues/152#issuecomment-142854589  

@jbytheway no need to wrap the we also have both possibilties in Boost.Fusion : http://www.boost.org/doc/libs/1_59_0/libs/fusion/doc/html/fusion/adapted/adapt_struct.html. Hana is becoming always nicer. :+1:
