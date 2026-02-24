# #59 - counting_iterator used as a forward iterator in <algorithm> generates warning in C++17 [Open]

> Username: luke-sterkowicz  
> Created at: 2020-08-07 16:17:33 UTC  
> Updated at: 2020-08-07 16:17:33 UTC  
> Url: https://github.com/boostorg/iterator/issues/59  

Following results in **iterator_adaptor.hpp(318,1): warning C4244: '+=': conversion from '__int64' to 'Base', possible loss of data**  
  
`    std::for_each(std::execution::par,  
        boost::counting_iterator<int>(0),  
        boost::counting_iterator<int>(10),  
        [&](int) {});`
