# #57 - hidden virtual function warning [Closed]

> Username: vinniefalco  
> Created at: 2017-10-29 14:50:47 UTC  
> Updated at: 2018-11-24 16:07:48 UTC  
> Closed at: 2018-11-24 16:07:48 UTC  
> Url: https://github.com/boostorg/filesystem/issues/57  

./boost/detail/utf8_codecvt_facet.hpp:181:17: warning: 'boost::filesystem::detail::utf8_codecvt_facet::do_length' hides overloaded virtual function [-Woverloaded-virtual]  
  
https://travis-ci.org/vinniefalco/beast/jobs/294451968#L1424

---

## Comment 1

> Username: Lastique  
> Created at: 2018-11-24 16:07:48 UTC  
> Url: https://github.com/boostorg/filesystem/issues/57#issuecomment-441377879  

`utf8_codecvt_facet` is part of Boost.Detail, please [report](https://github.com/boostorg/detail/issues/new) the problem there if it's still relevant.
