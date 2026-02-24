# #224 - [MSVC][std:c++latest] boost\date_time build failed due to error C2666: 'boost::date_time::base_time<boost::posix_time::ptime,boost::posix_time::posix_time_system>::operator ==': overloaded functions have similar conversions [Open]

> Username: fangzhouxia  
> Created at: 2022-12-08 02:24:14 UTC  
> Updated at: 2023-06-05 11:17:32 UTC  
> Url: https://github.com/boostorg/date_time/issues/224  

**Description:**  
After the MSVC team implented [P2468R2 The Equality Operator You Are Looking For](https://www.open-std.org/jtc1/sc22/wg21/docs/papers/2022/p2468r2.html), the error we encountered when compiling Trojan(Trojan depends on boost/date_time) under /std:c++latest, the error is due to the operator== https://github.com/boostorg/date_time/blob/develop/include/boost/date_time/time.hpp#L137. In order to fix this the 'ref' log_entry::ref class need to add a corresponding 'operator!=' like so, could you please look this issue? Thanks.  
  
**bool operator!=(const time_type& rhs) const  
    {  
      return time_system::is_equal(time_,rhs.time_);  
    }**  
  
  
**Note:** this issue will be reproduced on next release version of VS(17.6 or later).  
  
**Reproduction steps:**  
  
1. open VS2019 x64 tools command  
2. git clone https://github.com/trojan-gfw/trojan F:\gitP\trojan-gfw\trojan  
3. set CL= /std:c++latest  
4. cd F:\gitP\trojan-gfw\trojan &mkdir build_amd64 &cd F:\gitP\trojan-gfw\trojan\build_amd64  
5. cmake -G "Visual Studio 16 2019" -A x64 -DCMAKE_SYSTEM_VERSION=10.0.18362.0 -DOPENSSL_ROOT_DIR=F:\tools\OpenSSL\64 -DBOOST_ROOT=F:\tools\boost_1_67_0\x64 -DCMAKE_BUILD_TYPE=Release -DENABLE_MYSQL=OFF -DBoost_USE_STATIC_LIBS=ON ..  
6. msbuild /m /p:Platform=x64 /p:Configuration=Release trojan.sln /t:Rebuild  
  
  
**Expected behavior:**  
Build successfully.  
  
**Actual behavior:**  
[build (1).log](https://github.com/boostorg/date_time/files/10181577/build.1.log)  
F:\tools\boost_1_67_0\x64\boost/asio/ssl/detail/io.hpp(152): error C2666: 'boost::date_time::base_timeboost::posix_time::ptime,boost::posix_time::posix_time_system::operator ==': overloaded functions have similar conversions  
F:\tools\boost_1_67_0\x64\boost/date_time/time.hpp(126): note: could be 'bool boost::date_time::base_timeboost::posix_time::ptime,boost::posix_time::posix_time_system::operator ==(const boost::posix_time::ptime &) const'  
F:\tools\boost_1_67_0\x64\boost/date_time/time.hpp(126): note: or 'bool boost::date_time::base_timeboost::posix_time::ptime,boost::posix_time::posix_time_system::operator ==(const boost::posix_time::ptime &) const' [synthesized expression 'y == x']  
F:\tools\boost_1_67_0\x64\boost/bind/arg.hpp(48): note: or 'bool boost::operator ==(const boost::arg &,const boost::arg &)'  
F:\tools\boost_1_67_0\x64\boost/asio/ssl/detail/io.hpp(152): note: 'bool boost::operator ==(const boost::arg &,const boost::arg &)': could not deduce template argument for 'const boost::arg &' from 'boost::posix_time::ptime'  
F:\tools\boost_1_67_0\x64\boost/optional/detail/optional_relops.hpp(165): note: or 'bool boost::operator ==(boost::none_t,const boost::optional &) noexcept'  
  
**Environment:**  
VS version: VS2019(16.11.20)  
Operating system: windows server 2019

---

## Comment 1

> Username: ukaea-chah  
> Created at: 2023-06-05 11:17:32 UTC  
> Url: https://github.com/boostorg/date_time/issues/224#issuecomment-1576602122  

Yes, the mentioned change has landed in VS 17.6 - rolling back to 17.5 avoids the problem for the time being.
