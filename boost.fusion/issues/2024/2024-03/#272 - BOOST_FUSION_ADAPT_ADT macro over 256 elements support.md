# #272 - BOOST_FUSION_ADAPT_ADT macro over 256 elements support [Closed]

> Username: callmeRambo  
> Created at: 2024-03-20 12:57:51 UTC  
> Updated at: 2025-10-17 05:34:01 UTC  
> Closed at: 2025-10-17 05:34:01 UTC  
> Url: https://github.com/boostorg/fusion/issues/272  

Hi there, Its known BOOST_PP_TUPLE_TO_SEQ support up to 256, Is there a way to modify BOOST_PP_TUPLE_TO_SEQ to support more elements?  
The C headers structs I used are not allowed to modify, they contain values more than 400.
