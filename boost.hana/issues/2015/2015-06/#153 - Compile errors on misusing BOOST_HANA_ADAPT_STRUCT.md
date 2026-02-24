# #153 - Compile errors on misusing BOOST_HANA_ADAPT_STRUCT [Closed]

> Username: jbytheway  
> Created at: 2015-06-23 02:23:26 UTC  
> Updated at: 2015-06-26 19:48:10 UTC  
> Closed at: 2015-06-24 21:07:10 UTC  
> Url: https://github.com/boostorg/hana/issues/153  

I see in the tutorial you have a warning about using BOOST_HANA_ADAPT_STRUCT at global namespace scope.  I think this is probably going to be the most common usage error, and I'm wondering whether the error message could be improved.  Since it seems likely that the error will mention the type of the template you specialize in the macro, perhaps you could add a specialization of a type named something like remember_that_adapt_struct_must_be_called_at_global_scope to provide a more informative error.  
  
Another likely mistake is not comma-separating the list of members, since the FUSION equivalent does not comma-separate them.  The error from this is rather cryptic and I have no suggestion on improving it.  Is there any particular reason you decided to diverge from Fusion on this point?

---

## Comment 1

> Username: ldionne  
> Created at: 2015-06-24 21:01:51 UTC  
> Url: https://github.com/boostorg/hana/issues/153#issuecomment-115011282  

> Is there any particular reason you decided to diverge from Fusion on this point?  
  
Members are provided as a list of pairs, and I thought the most intuitive way to go was to separate them with commas. I would also believe that Fusion does not use commas because they want to use `BOOST_PP_SEQ_xxx` in the implementation, but not for any "philosophical" reason.

---

## Comment 2

> Username: jbytheway  
> Created at: 2015-06-25 02:25:59 UTC  
> Url: https://github.com/boostorg/hana/issues/153#issuecomment-115074527  

There is a philosophical reason to use BOOST_PP_SEQ in that it's possible to have an empty BOOST_PP_SEQ, but not an empty comma-separated list (since perfect emptiness-detection is not possible in the preprocessor, and even if it was that's not enough because empty arguments are OK for preprocesor macros, so if there is nothing in the list then that's a single entry with no tokens rather than no entries; Paul Mensonides explains it better than me).  
  
I just tested it, and indeed BOOST_HANA_ADAPT_STRUCT seems to not work correctly (compile error) on an empty struct.  I'll let you decide whether you care about that.  
  
The practical reason to follow Fusion's example is to aid those migrating their code from Fusion.  Of course, you might reasonably believe people who are not coming from Fusion would expect the comma and be confused in the opposite direction.

---

## Comment 3

> Username: ldionne  
> Created at: 2015-06-25 11:42:17 UTC  
> Url: https://github.com/boostorg/hana/issues/153#issuecomment-115217698  

> I just tested it, and indeed BOOST_HANA_ADAPT_STRUCT seems to not work correctly (compile error) on an empty struct. I'll let you decide whether you care about that.  
  
The following works fine on both master and develop:  
  
``` c++  
struct Person { };  
BOOST_HANA_ADAPT_STRUCT(Person);  
```

---

## Comment 4

> Username: jbytheway  
> Created at: 2015-06-26 03:54:55 UTC  
> Url: https://github.com/boostorg/hana/issues/153#issuecomment-115492904  

Ah, of course.  I was using  
  
```  
BOOST_HANA_ADAPT_STRUCT(Person, );  
```  
  
which is more of the same confusion.

---

## Comment 5

> Username: ldionne  
> Created at: 2015-06-26 19:48:09 UTC  
> Url: https://github.com/boostorg/hana/issues/153#issuecomment-115852511  

The way I see it, you're calling the `BOOST_HANA_ADAPT_STRUCT` macro with arguments, some of which are pairs `(type, member)`. When there is only one argument (the name of the struct), the struct is considered as empty. On the other hand, I think Fusion's way is less intuitive, since it uses very advanced preprocessor techniques (preprocessor sequences) to achieve its goal.
