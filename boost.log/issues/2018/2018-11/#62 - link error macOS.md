# #62 - link error macOS [Closed]

> Username: ohhmm  
> Created at: 2018-11-22 14:05:18 UTC  
> Updated at: 2018-12-18 16:52:54 UTC  
> Closed at: 2018-12-18 16:42:40 UTC  
> Url: https://github.com/boostorg/log/issues/62  

Undefined symbols for architecture x86_64:  
  "boost::log::v2s_mt_posix::attribute_set::insert(boost::log::v2s_mt_posix::attribute_name, boost::log::v2s_mt_posix::attribute const&)", referenced from:  
      boost::log::v2s_mt_posix::sources::basic_logger<char, boost::log::v2s_mt_posix::sources::severity_logger<boost::log::v2s_mt_posix::trivial::severity_level>, boost::log::v2s_mt_posix::sources::single_thread_model>::add_attribute_unlocked(boost::log::v2s_mt_posix::attribute_name const&, boost::log::v2s_mt_posix::attribute const&)

---

## Comment 1

> Username: Lastique  
> Created at: 2018-11-22 14:21:59 UTC  
> Url: https://github.com/boostorg/log/issues/62#issuecomment-441044938  

Are you linking with a shared library and forgot to define `BOOST_LOG_DYN_LINK` or `BOOST_ALL_DYN_LINK`?

---

## Comment 2

> Username: ohhmm  
> Created at: 2018-11-22 15:01:32 UTC  
> Url: https://github.com/boostorg/log/issues/62#issuecomment-441055746  

with cmake defaults

---

## Comment 3

> Username: Lastique  
> Created at: 2018-11-22 15:11:14 UTC  
> Url: https://github.com/boostorg/log/issues/62#issuecomment-441058340  

I don't know what that means. Please, verify whether my suggestion works or not.

---

## Comment 4

> Username: Lastique  
> Created at: 2018-12-18 16:42:40 UTC  
> Url: https://github.com/boostorg/log/issues/62#issuecomment-448286594  

No response.

---

## Comment 5

> Username: ohhmm  
> Created at: 2018-12-18 16:52:54 UTC  
> Url: https://github.com/boostorg/log/issues/62#issuecomment-448290280  

Your suggestion works.
