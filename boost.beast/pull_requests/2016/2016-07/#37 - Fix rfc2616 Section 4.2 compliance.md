# #37 Fix rfc2616 Section 4.2 compliance: [Closed]

> Username: vinniefalco  
> Created at: 2016-07-05 18:00:46 UTC  
> Updated at: 2017-07-25 17:44:16 UTC  
> Closed at: 2016-07-22 12:42:48 UTC  
> Url: https://github.com/boostorg/beast/pull/37  

`basic_headers` no longer combines fields with the same name by appending a comma and concatenating the two values together. This was breaking certain header fields which expect each value to be distinct, such as the "Set-Cookie" header.  
  
Now the container behaves more like a multi set with respect to insertion of multiple values with the same field name. Additional member functions are provided to provide extra functionality.  
  
Reviewers: @HowardHinnant @scottschurr @willwray

---

## Comment 1

> Username: willwray  
> Created_at: 2016-07-05 18:32:06 UTC  
> Url: https://github.com/boostorg/beast/pull/37#discussion_r69614387  

return set_.count(name);  ?

---

## Comment 2

> Username: vinniefalco  
> Created_at: 2016-07-05 18:33:05 UTC  
> Url: https://github.com/boostorg/beast/pull/37#discussion_r69614540  

Whoops!!! Why use existing functions when you can write them again yourself!

---

## Comment 3

> Username: willwray  
> Created_at: 2016-07-05 18:42:17 UTC  
> Url: https://github.com/boostorg/beast/pull/37#discussion_r69616007  

Hmm, the combination of _list_ and _set_ reminds me of a certain coding  
test...  
  
On Tue, Jul 5, 2016 at 11:33 AM, Vinnie Falco notifications@github.com  
wrote:  
  
> In include/beast/http/impl/basic_headers.ipp  
> https://github.com/vinniefalco/Beast/pull/37#discussion_r69614540:  
>   
> > @@ -205,6 +206,18 @@ basic_headers(FwdIt first, FwdIt last)  
> >  }  
> >   
> >  template<class Allocator>  
> > +std::size_t  
> > +basic_headers<Allocator>::  
> > +count(boost::string_ref const& name) const  
>   
> Whoops!!! Why use existing functions when you can write them again  
> yourself!  
>   
> —  
> You are receiving this because you were mentioned.  
> Reply to this email directly, view it on GitHub  
> https://github.com/vinniefalco/Beast/pull/37/files/db58cf1c0f4f215c9bc15450e096e41e2c169748#r69614540,  
> or mute the thread  
> https://github.com/notifications/unsubscribe/ADBpbhHFg-DlL482gKS3VusJJWL6pkcRks5qSqNjgaJpZM4JFX2l  
> .

---

## Comment 4

> Username: codecov-io  
> Created_at: 2016-07-05 18:44:57 UTC  
> Url: https://github.com/boostorg/beast/pull/37#issuecomment-230566557  

## [Current coverage](https://codecov.io/gh/vinniefalco/Beast/pull/37?src=pr) is **98.04%**  
  
> Merging [#37](https://codecov.io/gh/vinniefalco/Beast/pull/37?src=pr) into [master](https://codecov.io/gh/vinniefalco/Beast/branch/master?src=pr) will decrease coverage by **0.05%**  
  
``` diff  
@@             master        #37   diff @@  
==========================================  
  Files            72         72            
  Lines          5073       5075     +2     
  Methods           0          0            
  Messages          0          0            
  Branches          0          0            
==========================================  
- Hits           4977       4976     -1     
- Misses           96         99     +3     
  Partials          0          0            
```  
  
> Powered by [Codecov](https://codecov.io?src=pr). Last updated by [999e2fa...db58cf1](https://codecov.io/gh/vinniefalco/Beast/compare/999e2fa0318b5982736d3ea01a418770ea802671...db58cf1c0f4f215c9bc15450e096e41e2c169748)

---

## Comment 5

> Username: scottschurr  
> Created_at: 2016-07-06 00:47:19 UTC  
> Url: https://github.com/boostorg/beast/pull/37#discussion_r69661718  

Since we're now set up for duplicate entries, I just wanted to make sure we're happy that `find()` always returns the first entry (inserted in time sequence).  I think that's the behavior we'll see (since `insert()` always appends), and I think it's a reasonable behavior.  I just thought I'd mention it...

---

## Comment 6

> Username: scottschurr  
> Created_at: 2016-07-06 00:59:43 UTC  
> Url: https://github.com/boostorg/beast/pull/37#discussion_r69662552  

Doesn't it behave more like a `std::list`, at least if iterators are used?  `begin()` and `end()` return a `list_t::iterator`.

---

## Comment 7

> Username: scottschurr  
> Created_at: 2016-07-06 01:02:08 UTC  
> Url: https://github.com/boostorg/beast/pull/37#discussion_r69662717  

Sorry, yup, it's clearly good and expected.  The documentation in the *.hpp file says so.

---

## Comment 8

> Username: scottschurr  
> Created_at: 2016-07-06 01:10:04 UTC  
> Url: https://github.com/boostorg/beast/pull/37#issuecomment-230646593  

:+1: Looks good to me.

---

## Comment 9

> Username: vinniefalco  
> Created_at: 2016-07-06 01:56:23 UTC  
> Url: https://github.com/boostorg/beast/pull/37#discussion_r69666154  

Yeah you're right, the comment is incorrect. Will fix

---

## Comment 10

> Username: HowardHinnant  
> Created_at: 2016-07-06 17:54:45 UTC  
> Url: https://github.com/boostorg/beast/pull/37#issuecomment-230852553  

👍 Looks good here.  Passes tests on macOS.

---
