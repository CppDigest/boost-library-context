# #189 - Win32 build error: narrowing conversion [Closed]

> Username: strelez77  
> Created at: 2024-03-19 18:33:12 UTC  
> Updated at: 2024-03-20 13:42:12 UTC  
> Closed at: 2024-03-20 13:42:12 UTC  
> Url: https://github.com/boostorg/redis/issues/189  

boost/redis/resp3/impl/parser.ipp(197): error C2397: conversion from 'boost::redis::resp3::int_type' to 'size_t' requires a narrowing conversion  
  
This is the line in parser.ipp:  
"ret = {t, l, depth_, {}};"  
  
Compiler: Visual Studio 2022  
platform x86  
Win32
