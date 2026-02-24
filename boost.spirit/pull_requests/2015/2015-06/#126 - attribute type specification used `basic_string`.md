# #126 attribute type specification used `basic_string` [Closed]

> Username: sehe  
> Created at: 2015-06-21 21:57:30 UTC  
> Updated at: 2017-12-11 18:10:37 UTC  
> Closed at: 2017-12-11 18:07:27 UTC  
> Url: https://github.com/boostorg/spirit/pull/126  

It was [brought to my attention](http://stackoverflow.com/questions/11421430/string-parser-with-boost-variant-recursive-wrapper/11422737?noredirect=1#comment49970878_11422737) on SO that the documentation seems to be (confusingly) in error.  
  
I admit I get more questions about "why can't I assign the synthesized attribute to a `std::string`" than I could explain. So I guess this should be fixed.

---

## Comment 1

> Username: djowel  
> Created_at: 2015-06-21 22:48:43 UTC  
> Updated_at: 2015-06-21 22:49:13 UTC  
> Url: https://github.com/boostorg/spirit/pull/126#issuecomment-113965635  

The synthesized attribute of string parser is really a std::string (see boost/spirit/home/qi/lit.hpp):   
  
```  
    typedef std::basic_string<char_type> string_type;  
```  
  
   ...  
  
```  
   template <typename Context, typename Iterator>  
    struct attribute  
    {  
        typedef typename mpl::if_c<  
            no_attribute, unused_type, string_type>::type  
        type;  
    };  
```  
  
What am I missing?

---

## Comment 2

> Username: sehe  
> Created_at: 2015-06-21 22:54:15 UTC  
> Url: https://github.com/boostorg/spirit/pull/126#issuecomment-113966033  

Well. That's embarassing. You're right of course.  
  
Then again, I'm not sure what caused the problem in that SO question I linked to anymore. I'll need to revisit that (or maybe you can give it a quick scan). It's rather old, so maybe you recognize an already fixed issue in there :/  
  
Other than that, disregard this pull request :)

---

## Comment 3

> Username: djowel  
> Created_at: 2015-06-21 22:58:27 UTC  
> Url: https://github.com/boostorg/spirit/pull/126#issuecomment-113966190  

Yes. If you can distill the problem further, then I can go take a look at it and see what's going on.

---

## Comment 4

> Username: djowel  
> Created_at: 2015-06-21 23:05:35 UTC  
> Url: https://github.com/boostorg/spirit/pull/126#issuecomment-113966827  

I had this as 'wontfix' for now, but let's continue with the investigation.

---

## Comment 5

> Username: Kojoley  
> Created_at: 2017-12-11 18:07:27 UTC  
> Updated_at: 2017-12-11 18:08:40 UTC  
> Url: https://github.com/boostorg/spirit/pull/126#issuecomment-350807729  

> The synthesized attribute of string parser is really a std::string  
  
Actually `qi::assign_to`(`x3::move_to`) receives a pair of iterators and not the `std::string`.  
  
I have opened #321 for the issue.

---
