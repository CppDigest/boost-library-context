# #1725 - add_certificate_authority not used properly in example [Closed]

> Username: ichernev  
> Created at: 2019-10-08 10:25:34 UTC  
> Updated at: 2019-10-08 10:32:04 UTC  
> Closed at: 2019-10-08 10:32:03 UTC  
> Url: https://github.com/boostorg/beast/issues/1725  

### Version of Beast  
  
I used `1.69.0`, but the code looks broken in latest master.  
  
### Steps necessary to reproduce the problem  
  
The file `example/common/root_certificates.hpp` does not use `add_certificate_authority` properly. The function [accepts 1 certificate](https://www.boost.org/doc/libs/1_69_0/doc/html/boost_asio/reference/ssl__context/add_certificate_authority/overload1.html) at a time, and the file tries to load multiple ones.  
  
I'd suggest to put certs in a `std::vector<std::string>`, and loading those in a for loop.  
  
Moreover, it (tries to) load the same cert twice, even though the comments say one is `DigiCert`, and the other `GeoTrust`.  
  
### All relevant compiler information  
  
I don't think it is relevant, but I used `g++ 9.1.0`.

---

## Comment 1

> Username: ichernev  
> Created at: 2019-10-08 10:32:03 UTC  
> Url: https://github.com/boostorg/beast/issues/1725#issuecomment-539453537  

Ah sorry, your example was fixed in 1.69, I looked at the web example with slightly older version.
