# #215 - Bug in factory.hpp? [Open]

> Username: ivytin  
> Created at: 2020-08-13 09:49:53 UTC  
> Updated at: 2020-08-14 15:34:35 UTC  
> Url: https://github.com/boostorg/serialization/issues/215  

Hi, there seems to be a bug in marco `BOOST_SERIALIZATION_FACTORY`:  
```  
// factory.hpp  
        return new T(                                     \  
            BOOST_PP_IF(BOOST_PP_GREATER(N, 0)            \  
                , a0, BOOST_PP_EMPTY())                   \  
            BOOST_PP_IF(BOOST_PP_GREATER(N, 1))           \        is the last  closing bracket unnecessary?    
                , BOOST_PP_COMMA, BOOST_PP_EMPTY)()       \  
            BOOST_PP_IF(BOOST_PP_GREATER(N, 1)            \  
                , a1, BOOST_PP_EMPTY())                   \  
            BOOST_PP_IF(BOOST_PP_GREATER(N, 2))           \       also here  
                , BOOST_PP_COMMA, BOOST_PP_EMPTY)()       \  
            BOOST_PP_IF(BOOST_PP_GREATER(N, 2)            \  
                , a2, BOOST_PP_EMPTY())                   \  
            BOOST_PP_IF(BOOST_PP_GREATER(N, 3))           \       and here  
                , BOOST_PP_COMMA, BOOST_PP_EMPTY)()       \  
            BOOST_PP_IF(BOOST_PP_GREATER(N, 3)            \  
                , a3, BOOST_PP_EMPTY())                   \  
        );                                                \  
```

---

## Comment 1

> Username: robertramey  
> Created at: 2020-08-13 15:51:55 UTC  
> Url: https://github.com/boostorg/serialization/issues/215#issuecomment-673558536  

This is totally indecipherable to me now.  Looking a little more into it it seems correct to me now.  But this macro is never used in any tests or anywhere else. I see what the (obscure) purpose is. part of it is used in one of the dll tests.  
  
My question is how did you come upon this.  Have you used this and found it led to an error/problem or ?

---

## Comment 2

> Username: ivytin  
> Created at: 2020-08-14 02:15:13 UTC  
> Url: https://github.com/boostorg/serialization/issues/215#issuecomment-673845448  

I was trying to write a demo following the [boost official document](https://www.boost.org/doc/libs/1_67_0/libs/serialization/doc/special.html#plugins). The  code can be simplified into  
```  
class person {  
public:  
  int age_;  
  string name_;  
 ...  
};  
  
class people : public person {  
public:  
public:  
  int age_;  
  string name_;  
 ...  
};  
  
BOOST_CLASS_VERSION(person, 0)  
BOOST_SERIALIZATION_FACTORY_0(people)  
// error line  
BOOST_SERIALIZATION_FACTORY_2(people, int, std::string)  
  
BOOST_CLASS_EXPORT_GUID(people, "people")  
  
```  
  
The compiler  complains that **error: macro "BOOST_PP_IF" requires 3 arguments, but only 1 given  
 BOOST_SERIALIZATION_FACTORY_2(people, int, std::string)**.   
That's true because the unnecessary closing bracket breaks off `BOOST_PP_IF`'s function and makes the macro to  be replaced by  `return new people(a0 BOOST_PP_IF,BOOST_PP_COMMA,BOOST_PP_EMPTY)()a1 BOOST_PP_IF,BOOST_PP_COMMA,BOOST_PP_EMPTY)()BOOST_PP_IF,BOOST_PP_COMMA,BOOST_PP_EMPTY)());`

---

## Comment 3

> Username: robertramey  
> Created at: 2020-08-14 04:34:31 UTC  
> Url: https://github.com/boostorg/serialization/issues/215#issuecomment-673877990  

OK  - you got me here.  Looks to me that you're doing things as I intended. Note there is an example of the usage of this but only for a type with a constructor with 0 arguments.  So even though I haven't actually tested it I still believe that it's likely correct.  So, try the following and see if things work.  
  
>         return new T(                                     \  
            BOOST_PP_IF(BOOST_PP_GREATER(N, 0)            \  
                , a0, BOOST_PP_EMPTY())                   \  
            BOOST_PP_IF(BOOST_PP_GREATER(N, 1)            \     
                , BOOST_PP_COMMA, BOOST_PP_EMPTY)()       \  
            BOOST_PP_IF(BOOST_PP_GREATER(N, 1)            \  
                , a1, BOOST_PP_EMPTY())                   \  
            BOOST_PP_IF(BOOST_PP_GREATER(N, 2)            \   
                , BOOST_PP_COMMA, BOOST_PP_EMPTY)()       \  
            BOOST_PP_IF(BOOST_PP_GREATER(N, 2)            \  
                , a2, BOOST_PP_EMPTY())                   \  
            BOOST_PP_IF(BOOST_PP_GREATER(N, 3)            \  
                , BOOST_PP_COMMA, BOOST_PP_EMPTY)()       \  
            BOOST_PP_IF(BOOST_PP_GREATER(N, 3)            \  
                , a3, BOOST_PP_EMPTY())                   \  
        );                                                \

---

## Comment 4

> Username: ivytin  
> Created at: 2020-08-14 11:39:39 UTC  
> Url: https://github.com/boostorg/serialization/issues/215#issuecomment-674033525  

Yep, after removing those closing brackets,  the compiler stops complaining and the src code was compiled successfully.

---

## Comment 5

> Username: robertramey  
> Created at: 2020-08-14 15:34:35 UTC  
> Url: https://github.com/boostorg/serialization/issues/215#issuecomment-674132497  

Great - but does the application run?  If you made a test program for this, I'd be curious to see it.
