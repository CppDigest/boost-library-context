# #697 fix SHA1 hash in build_with_source() [Merged]

> Username: huanzhang12  
> Created at: 2017-03-24 21:45:30 UTC  
> Updated at: 2017-03-29 17:47:59 UTC  
> Merged at: 2017-03-25 11:32:08 UTC  
> Closed at: 2017-03-25 11:32:08 UTC  
> Url: https://github.com/boostorg/compute/pull/697  

In build_with_source(), when BOOST_COMPUTE_USE_OFFLINE_CACHE is set, we use boost::uuids::detail::sha1 to compute the SHA1 of the program source, options, etc, and use it as the unique key to manage cached objects.  
  
When the hash is converted to a string and passed to load_program_binary() and save_program_binary(), the conversion function of object detail::sha1 is called, which then calls  boost::uuids::detail::sha1::get_digest(). If you take a look at http://www.boost.org/doc/libs/1_63_0/boost/uuid/sha1.hpp, the sha1::get_digest() function actually appends some bytes to the string each time called. Thus, each time when we call get_digest() we get a different SHA1.   
  
Thus, the hash string used as the parameter to load_program_binary() and save_program_binary() are always different, because the string conversion function is called twice. This effectively disables the offline cache as the two hashes are always different.  
  
My patch fixes this issue by converting the SHA1 to string only once, and use the same string for  load_program_binary() and save_program_binary().

---

## Comment 1

> Username: coveralls  
> Created_at: 2017-03-24 23:06:38 UTC  
> Url: https://github.com/boostorg/compute/pull/697#issuecomment-289163757  

[![Coverage Status](https://coveralls.io/builds/10766272/badge)](https://coveralls.io/builds/10766272)  
  
Coverage increased (+0.09%) to 83.407% when pulling **1a7eeedf30d802198d137da3fddfb659b3839223 on huanzhang12:develop** into **d8beaaf227e02f749facfe31afa225d8d829bbe3 on boostorg:develop**.

---

## Comment 2

> Username: jszuppe  
> Created_at: 2017-03-25 11:32:24 UTC  
> Url: https://github.com/boostorg/compute/pull/697#issuecomment-289206303  

Good job! Thanks!

---

## Comment 3

> Username: keryell  
> Created_at: 2017-03-29 17:15:20 UTC  
> Url: https://github.com/boostorg/compute/pull/697#issuecomment-290158980  

Ouch!  
What a nasty bug.  
Perhaps this "feature" from https://github.com/boostorg/uuid should be fixed upstream instead?  
Interestingly, issues are not allowed on this repository. :-)  
  
Another fix could have been to change the logic of `boost/compute/detail/sha1.hpp` to add the caching of the returned `std::string`.  
  
But I think it would be nice to add a few comments in the code explaining the issue and the change.  
Otherwise it is impossible to understand why we put the hash string in an intermediate string and someone else could be bitten again in the future...

---

## Comment 4

> Username: jszuppe  
> Created_at: 2017-03-29 17:43:18 UTC  
> Updated_at: 2017-03-29 17:47:59 UTC  
> Url: https://github.com/boostorg/compute/pull/697#issuecomment-290167086  

>Perhaps this "feature" from https://github.com/boostorg/uuid should be fixed upstream instead?  
Interestingly, issues are not allowed on this repository. :-)  
  
<a href="https://imgflip.com/i/1mblq0"><img src="https://i.imgflip.com/1mblq0.jpg" title="made at imgflip.com"/></a>  
  
>Another fix could have been to change the logic of boost/compute/detail/sha1.hpp to add the caching of the returned std::string.  
  
Not a bad idea.  
  
```cpp  
// Accumulates SHA1 hash of the passed strings.  
class sha1 {  
    public:  
        sha1(const std::string &s = "") {  
            if (!s.empty()) this->process(s);  
        }  
  
        sha1& process(const std::string &s) {  
            hash_str.clear();  
            h.process_bytes(s.c_str(), s.size());  
            return *this;  
        }  
  
        operator std::string() {  
            if(!hash_str.empty())  
                return hash_str;  
  
            unsigned int digest[5];  
            h.get_digest(digest);  
  
            std::ostringstream buf;  
            for(int i = 0; i < 5; ++i)  
                buf << std::hex << std::setfill('0') << std::setw(8) << digest[i];  
  
            hash_str = buf.str();  
            return hash_str;  
        }  
    private:  
        boost::uuids::detail::sha1 h;  
        std::string hash_str;  
};  
```  
  
but  
  
> a few comments in the code explaining the issue and the change.  
Otherwise it is impossible to understand why we put the hash string in an intermediate string and someone else could be bitten again in the future...  
  
seems to be a simpler solution.

---
