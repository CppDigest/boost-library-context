# #44 - Compile error with gcc 8.3.1 and C++17 mode [Closed]

> Username: robhancocksed  
> Created at: 2019-07-04 00:53:03 UTC  
> Updated at: 2025-01-03 12:38:11 UTC  
> Closed at: 2025-01-03 12:38:10 UTC  
> Url: https://github.com/boostorg/mpl/issues/44  

Am seeing the following compile error in code that includes <boost/algorithm/string/predicate.hpp> with gcc 8.3.1 (the version from the latest RHEL7 devtoolset-8 package) and with the -std=c++17 flag. This is with boost 1.69 but the relevant code doesn't seem to have changed.  
```  
In file included from /usr/include/boost169/boost/mpl/aux_/include_preprocessed.hpp:37,  
                 from /usr/include/boost169/boost/mpl/bind.hpp:50,  
                 from /usr/include/boost169/boost/mpl/lambda.hpp:18,  
                 from /usr/include/boost169/boost/mpl/apply.hpp:25,  
                 from /usr/include/boost169/boost/iterator/iterator_facade.hpp:36,  
                 from /usr/include/boost169/boost/range/iterator_range_core.hpp:27,  
                 from /usr/include/boost169/boost/range/iterator_range.hpp:13,  
                 from /usr/include/boost169/boost/range/as_literal.hpp:22,  
                 from /usr/include/boost169/boost/algorithm/string/predicate.hpp:19,  
...  
/usr/include/boost169/boost/mpl/aux_/preprocessed/gcc/bind.hpp:466:67: error: template parameter 'template<class T1, class T2, class T3> class F'  
 template< template< typename T1, typename T2, typename T3 > class F, typename Tag >  
                                                                   ^  
In file included from /usr/include/boost169/boost/mpl/aux_/include_preprocessed.hpp:37,  
                 from /usr/include/boost169/boost/mpl/quote.hpp:45,  
                 from /usr/include/boost169/boost/mpl/aux_/full_lambda.hpp:25,  
                 from /usr/include/boost169/boost/mpl/lambda.hpp:22,  
                 from /usr/include/boost169/boost/mpl/apply.hpp:25,  
                 from /usr/include/boost169/boost/iterator/iterator_facade.hpp:36,  
                 from /usr/include/boost169/boost/range/iterator_range_core.hpp:27,  
                 from /usr/include/boost169/boost/range/iterator_range.hpp:13,  
                 from /usr/include/boost169/boost/range/as_literal.hpp:22,  
                 from /usr/include/boost169/boost/algorithm/string/predicate.hpp:19,  
...  
/usr/include/boost169/boost/mpl/aux_/preprocessed/gcc/quote.hpp:64:8: error: redeclared here as 'template<class P1, class P2, class P3> class F'  
 struct quote3  
        ^~~~~~  
```  
It appears this compiler doesn't like the forward declaration using different template argument names from the actual declaration?  
  
Another similar mention of this issue here: https://www.reddit.com/r/cpp/comments/b6wsyy/c_boostunits_survey_on_lack_of_adoption_of_uom/ejtdcgu/?utm_source=share&utm_medium=web2x

---

## Comment 1

> Username: tim-oleksii  
> Created at: 2019-09-19 15:05:56 UTC  
> Url: https://github.com/boostorg/mpl/issues/44#issuecomment-533174546  

+1

---

## Comment 2

> Username: jeking3  
> Created at: 2022-04-30 21:17:49 UTC  
> Url: https://github.com/boostorg/mpl/issues/44#issuecomment-1114055618  

This may be resolved in gcc-8.4.0 as shown by CI testing:  
https://github.com/boostorg/mpl/runs/6242335306?check_suite_focus=true

---

## Comment 3

> Username: jeking3  
> Created at: 2022-05-01 21:25:22 UTC  
> Url: https://github.com/boostorg/mpl/issues/44#issuecomment-1114340616  

@robhancocksed do you want to keep this open?

---

## Comment 4

> Username: robhancock  
> Created at: 2022-05-02 16:08:42 UTC  
> Url: https://github.com/boostorg/mpl/issues/44#issuecomment-1115075023  

@jeking3 I'm not actively using a setup with gcc 8.3 at this point, so this doesn't really affect me now.

---

## Comment 5

> Username: jeking3  
> Created at: 2025-01-03 12:38:10 UTC  
> Url: https://github.com/boostorg/mpl/issues/44#issuecomment-2569161418  

This is not an issue with gcc 8.4.0:  
  
https://github.com/boostorg/mpl/actions/runs/12597783771/job/35111414080
