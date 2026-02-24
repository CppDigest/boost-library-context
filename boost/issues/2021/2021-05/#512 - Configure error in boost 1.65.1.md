# #512 - Configure error in boost 1.65.1 [Open]

> Username: Neoblock8  
> Created at: 2021-05-05 06:30:33 UTC  
> Updated at: 2021-05-05 06:36:43 UTC  
> Url: https://github.com/boostorg/boost/issues/512  

Hi,  
Using ubuntu 18.04.  
Installed with "sudo apt-get install libboost-all-dev".   
libboost version is 1.65.1.  
When i was configure it will return   
checking for boostlib >= 1.58.0... yes  
checking whether the Boost::System library is available... no  
checking whether the Boost::Filesystem library is available... no  
checking whether the Boost::Thread library is available... no  
checking whether the Boost::Random library is available... no  
checking whether the Boost::Chrono library is available... no  
checking whether the Boost::Unit_Test_Framework library is available... no  
checking for dynamic linked boost test... no  
checking for mismatched boost c++11 scoped enums... ok  
configure: error: No working boost sleep implementation found.  
  
How to solve this?
