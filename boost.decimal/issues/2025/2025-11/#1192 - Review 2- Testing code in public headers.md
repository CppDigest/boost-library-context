# #1192 - Review 2: Testing code in public headers? [Closed]

> Username: jzmaddock  
> Created at: 2025-11-04 18:41:46 UTC  
> Updated at: 2026-01-29 15:22:52 UTC  
> Closed at: 2025-11-06 19:20:22 UTC  
> Url: https://github.com/boostorg/decimal/issues/1192  

BOOST_DECIMAL_REDUCE_TEST_DEPTH, debug_pattern and bit_string are  
still defined in public headers, when they belong to tests.
