# #90 - List of non BOOST-prefixed macros [Open]

> Username: ned14  
> Created at: 2019-12-16 13:50:59 UTC  
> Updated at: 2022-02-02 05:21:43 UTC  
> Url: https://github.com/boostorg/phoenix/issues/90  

The following macros are missing a BOOST_ prefix, which is against Boost library guidelines:  
  
```  
./boost/phoenix/scope/detail/local_variable.hpp:#ifndef PHOENIX_SCOPE_DETAIL_LOCAL_VARIABLE_HPP  
./boost/phoenix/scope/detail/local_variable.hpp:#define PHOENIX_SCOPE_DETAIL_LOCAL_VARIABLE_HPP  
./boost/phoenix/core/limits.hpp:#define PHOENIX_LIMIT BOOST_PHOENIX_LIMIT  
./boost/phoenix/core/detail/cpp03/function_eval.hpp:        #define PHOENIX_GET_ARG(z, n, data)                                     \  
./boost/phoenix/core/detail/cpp03/function_eval.hpp:        #define PHOENIX_EVAL_ARG(z, n, data)                                    \  
./boost/phoenix/core/detail/cpp03/function_eval.hpp:        #undef PHOENIX_GET_ARG  
./boost/phoenix/core/detail/cpp03/function_eval.hpp:        #undef PHOENIX_EVAL_ARG  
./boost/phoenix/core/detail/cpp03/function_eval.hpp:                BOOST_PP_REPEAT(BOOST_PHOENIX_ITERATION, PHOENIX_GET_ARG, _)  
./boost/phoenix/core/detail/cpp03/function_eval.hpp:                return boost::phoenix::eval(f, ctx)(BOOST_PP_ENUM(BOOST_PHOENIX_ITERATION, PHOENIX_EVAL_ARG, _));  
./boost/phoenix/core/detail/cpp03/function_eval.hpp:                return boost::phoenix::eval(f, ctx)(BOOST_PP_ENUM(BOOST_PHOENIX_ITERATION, PHOENIX_EVAL_ARG, _));  
./boost/phoenix/core/detail/cpp03/expression.hpp:#endif // PHOENIX_DONT_USE_PREPROCESSED_FILES  
./boost/phoenix/bind/bind_member_variable.hpp:#ifndef PHOENIX_BIND_BIND_MEMBER_VARIABLE_HPP  
./boost/phoenix/bind/bind_member_variable.hpp:#define PHOENIX_BIND_BIND_MEMBER_VARIABLE_HPP  
./boost/phoenix/bind/bind_member_function.hpp:#ifndef PHOENIX_BIND_BIND_MEMBER_FUNCTION_HPP  
./boost/phoenix/bind/bind_member_function.hpp:#define PHOENIX_BIND_BIND_MEMBER_FUNCTION_HPP  
./boost/phoenix/bind/bind_function.hpp:#ifndef PHOENIX_BIND_BIND_FUNCTION_HPP  
./boost/phoenix/bind/bind_function.hpp:#define PHOENIX_BIND_BIND_FUNCTION_HPP  
./boost/phoenix/bind/bind_function_object.hpp:#ifndef PHOENIX_BIND_BIND_FUNCTION_OBJECT_HPP  
./boost/phoenix/bind/bind_function_object.hpp:#define PHOENIX_BIND_BIND_FUNCTION_OBJECT_HPP  
./boost/phoenix/function/detail/cpp03/function_operator.hpp:#endif // PHOENIX_DONT_USE_PREPROCESSED_FILES  
```

---

## Comment 1

> Username: jefftrull  
> Created at: 2020-06-15 17:37:57 UTC  
> Url: https://github.com/boostorg/phoenix/issues/90#issuecomment-644272561  

Can you guys LMK what your plans are for fixing this - there is a usage in Wave [here](https://github.com/boostorg/wave/blob/91d653e1c5a53c3ef5d6254286ebf7808891a6a5/include/boost/wave/wave_config.hpp#L219). Would like to coordinate. Thanks!

---

## Comment 2

> Username: djowel  
> Created at: 2020-06-15 23:39:24 UTC  
> Url: https://github.com/boostorg/phoenix/issues/90#issuecomment-644445778  

> Can you guys LMK what your plans are for fixing this - there is a usage in Wave [here](https://github.com/boostorg/wave/blob/91d653e1c5a53c3ef5d6254286ebf7808891a6a5/include/boost/wave/wave_config.hpp#L219). Would like to coordinate. Thanks!  
  
Hi Jeff, if you can do a PR for this, I can merge it in. I'm a bit worried about backward compatibility though, but I'd like to see what's in stake first before diving in.

---

## Comment 3

> Username: jefftrull  
> Created at: 2020-06-16 01:20:15 UTC  
> Url: https://github.com/boostorg/phoenix/issues/90#issuecomment-644473740  

Well, it would basically be prepending `BOOST_` in front of all those macro names. I think the scary thing from my perspective is whether any of them were previously part of the public API of Phoenix, and if so, would that impact other users?  
  
Wave just uses `PHOENIX_THREADSAFE` and `PHOENIX_LIMIT`

---

## Comment 4

> Username: jefftrull  
> Created at: 2020-06-16 01:26:24 UTC  
> Url: https://github.com/boostorg/phoenix/issues/90#issuecomment-644475418  

Also, if it's relevant - Wave uses Classic (!)

---

## Comment 5

> Username: jefftrull  
> Created at: 2022-02-02 05:21:43 UTC  
> Url: https://github.com/boostorg/phoenix/issues/90#issuecomment-1027596815  

How about this: there is already a `BOOST_PHOENIX_LIMIT`, which is set from `PHOENIX_LIMIT` if supplied, for backwards compatibility. Looks like @sithhell did it back in 2011 :) Can we do something like this for `BOOST_THREADSAFE` also?
