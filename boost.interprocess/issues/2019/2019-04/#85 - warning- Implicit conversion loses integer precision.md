# #85 - warning: Implicit conversion loses integer precision [Closed]

> Username: davecotter  
> Created at: 2019-04-23 02:26:08 UTC  
> Updated at: 2019-05-11 17:30:41 UTC  
> Closed at: 2019-05-11 17:29:59 UTC  
> Url: https://github.com/boostorg/interprocess/issues/85  

boost_1_69_0/boost/interprocess/streams/bufferstream.hpp:241:25: Implicit conversion loses integer precision: 'boost::interprocess::basic_bufferbuf<char, std::__1::char_traits<char> >::off_type' (aka 'long long') to 'int'

---

## Comment 1

> Username: igaztanaga  
> Created at: 2019-05-11 17:30:37 UTC  
> Url: https://github.com/boostorg/interprocess/issues/85#issuecomment-491529984  

Many thanks for the report!

---

## Comment 2

> Username: igaztanaga  
> Created at: 2019-05-11 17:30:41 UTC  
> Url: https://github.com/boostorg/interprocess/issues/85#issuecomment-491529991  

Many thanks for the report!
