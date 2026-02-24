# #776 - second_order_root_finder (near) infinite loop [Closed]

> Username: TFiFiE  
> Created at: 2022-03-12 01:35:47 UTC  
> Updated at: 2022-03-13 08:51:11 UTC  
> Closed at: 2022-03-13 08:51:11 UTC  
> Url: https://github.com/boostorg/math/issues/776  

`halley_iterate` and `schroder_iterate` can get stuck in a (virtually) infinite loop even when given a limited `max_iter`, because in the function:  
https://github.com/boostorg/math/blob/b22105f141b44016b8257288f55bb874ba636a7f/include/boost/math/tools/roots.hpp#L483  
the variable `count` might already be zero when either of the following functions is called:  
https://github.com/boostorg/math/blob/b22105f141b44016b8257288f55bb874ba636a7f/include/boost/math/tools/roots.hpp#L612  
https://github.com/boostorg/math/blob/b22105f141b44016b8257288f55bb874ba636a7f/include/boost/math/tools/roots.hpp#L638  
where it will wrap around by any of:  
https://github.com/boostorg/math/blob/b22105f141b44016b8257288f55bb874ba636a7f/include/boost/math/tools/roots.hpp#L383  
https://github.com/boostorg/math/blob/b22105f141b44016b8257288f55bb874ba636a7f/include/boost/math/tools/roots.hpp#L402  
https://github.com/boostorg/math/blob/b22105f141b44016b8257288f55bb874ba636a7f/include/boost/math/tools/roots.hpp#L439  
https://github.com/boostorg/math/blob/b22105f141b44016b8257288f55bb874ba636a7f/include/boost/math/tools/roots.hpp#L458  
becoming `std::numeric_limits<boost::uintmax_t>::max()` before reaching:  
https://github.com/boostorg/math/blob/b22105f141b44016b8257288f55bb874ba636a7f/include/boost/math/tools/roots.hpp#L662
