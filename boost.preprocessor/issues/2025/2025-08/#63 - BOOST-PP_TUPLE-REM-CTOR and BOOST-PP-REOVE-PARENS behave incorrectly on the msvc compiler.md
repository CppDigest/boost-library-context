# #63 - BOOST-PP_TUPLE-REM-CTOR and BOOST-PP-REOVE-PARENS behave incorrectly on the msvc compiler [Open]

> Username: yangrcaaa  
> Created at: 2025-08-26 15:32:26 UTC  
> Updated at: 2025-08-26 15:32:26 UTC  
> Url: https://github.com/boostorg/preprocessor/issues/63  

BOOST_PP_TUPLE_REM_CTOR((x, y, z)) // expands to x,But the document says it will expand into x, y, z  
BOOST_PP_REMOVE_PARENS((a, b, c)) //expands to a,But I think it will unfold into a, b, c
