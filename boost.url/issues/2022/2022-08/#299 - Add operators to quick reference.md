# #299 - Add operators to quick reference [Closed]

> Username: vinniefalco  
> Created at: 2022-08-02 17:40:04 UTC  
> Updated at: 2022-08-03 22:15:25 UTC  
> Closed at: 2022-08-03 22:15:25 UTC  
> Url: https://github.com/boostorg/url/issues/299  

All the stream operations (`operator<<`) and comparisons (e.g. `operator==`, `operator<`) need to be added to the quick reference. And they need to follow the format of javadoccing hidden friends.

---

## Comment 1

> Username: alandefreitas  
> Created at: 2022-08-03 00:57:07 UTC  
> Url: https://github.com/boostorg/url/issues/299#issuecomment-1203363410  

Doxygen+docca is not creating a page for each of the operators anymore.   
  
All hidden friend operators are associated with the corresponding class in the index now.  
  
```  
operator!=  
ipv4_address, ipv4_address::operator!=  
ipv6_address, ipv6_address::operator!=  
pct_encoded_view, pct_encoded_view::operator!=  
url_view, url_view::operator!=  
  
operator()  
grammar::lut_chars, grammar::lut_chars::operator()  
  
operator*  
recycled_ptr, recycled_ptr::operator*  
  
operator+  
grammar::lut_chars, grammar::lut_chars::operator+  
  
operator-  
grammar::lut_chars, grammar::lut_chars::operator-  
  
operator->  
recycled_ptr, recycled_ptr::operator->  
  
operator<  
pct_encoded_view, pct_encoded_view::operator<  
url_view, url_view::operator<  
  
operator<<  
authority_view, authority_view::operator<<  
ipv4_address, ipv4_address::operator<<  
ipv6_address, ipv6_address::operator<<  
pct_encoded_view, pct_encoded_view::operator<<  
segments_encoded_view, segments_encoded_view::operator<<  
segments_view, segments_view::operator<<  
url, url::operator<<  
url_view, url_view::operator<<  
  
operator<=  
pct_encoded_view, pct_encoded_view::operator<=  
url_view, url_view::operator<=  
  
operator=  
authority_view, authority_view::operator=  
basic_static_pool, basic_static_pool::operator=  
grammar::range, grammar::range::operator=  
ipv4_address, ipv4_address::operator=  
ipv6_address, ipv6_address::operator=  
params, params::operator=  
params_encoded, params_encoded::operator=  
params_encoded_view, params_encoded_view::operator=  
params_view, params_view::operator=  
recycled_ptr, recycled_ptr::operator=  
segments, segments::operator=  
segments_encoded, segments_encoded::operator=  
segments_encoded_view, segments_encoded_view::operator=  
segments_view, segments_view::operator=  
static_url, static_url::operator=  
url, url::operator=  
url_view, url_view::operator=  
  
operator==  
ipv4_address, ipv4_address::operator==  
ipv6_address, ipv6_address::operator==  
pct_encoded_view, pct_encoded_view::operator==  
url_view, url_view::operator==  
  
operator>  
pct_encoded_view, pct_encoded_view::operator>  
url_view, url_view::operator>  
  
operator>=  
pct_encoded_view, pct_encoded_view::operator>=  
url_view, url_view::operator>=  
  
operator[]  
authority_view, authority_view::operator[]  
params_encoded, params_encoded::operator[]  
  
operator~  
grammar::lut_chars, grammar::lut_chars::operator~  
```

---

## Comment 2

> Username: vinniefalco  
> Created at: 2022-08-03 01:18:30 UTC  
> Url: https://github.com/boostorg/url/issues/299#issuecomment-1203375388  

I showed you how to do this in another issue

---

## Comment 3

> Username: vinniefalco  
> Created at: 2022-08-03 01:19:07 UTC  
> Updated at: 2022-08-03 01:19:18 UTC  
> Url: https://github.com/boostorg/url/issues/299#issuecomment-1203375714  

I think for the comparison (less than, etc) we don't need them in the global landing page. We only need all instances of `operator<<`  
  
I can take care of this if necessary.

---

## Comment 4

> Username: alandefreitas  
> Created at: 2022-08-03 01:25:31 UTC  
> Url: https://github.com/boostorg/url/issues/299#issuecomment-1203379052  

Do we have an example? I can't find anything in Boost.Json or Boost.Beast.

---

## Comment 5

> Username: vinniefalco  
> Created at: 2022-08-03 02:22:00 UTC  
> Updated at: 2022-08-03 02:22:11 UTC  
> Url: https://github.com/boostorg/url/issues/299#issuecomment-1203408341  

> Do we have an example? I can't find anything in Boost.Json or Boost.Beast.  
  
Yes, see `url` and `url_view`  
  
![image](https://user-images.githubusercontent.com/1503976/182510331-85205015-b36a-4415-8e38-7d62ef9ddc29.png)  
  
I think we should only make `operator<<` global. The rest can stay friends.
