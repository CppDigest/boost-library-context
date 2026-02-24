# #12 Fixed padded output of UUID when std::left is set [Merged]

> Username: erichkeane  
> Created at: 2015-12-29 21:12:38 UTC  
> Updated at: 2015-12-30 19:50:22 UTC  
> Merged at: 2015-12-30 19:47:46 UTC  
> Closed at: 2015-12-30 19:47:46 UTC  
> Url: https://github.com/boostorg/uuid/pull/12  

The defect https://svn.boost.org/trac/boost/ticket/11385 reports a  
situation where a uuid's single-hex-digit values are improperly padded  
when std::left is set on the stream.  
  
It is fixed by ensuring that the ostream overload always uses std::right  
to be consistent with the default behavior.

---

## Comment 1

> Username: mclow  
> Created_at: 2015-12-29 21:18:41 UTC  
> Url: https://github.com/boostorg/uuid/pull/12#issuecomment-167879039  

Needs a test.

---

## Comment 2

> Username: erichkeane  
> Created_at: 2015-12-29 21:41:28 UTC  
> Url: https://github.com/boostorg/uuid/pull/12#issuecomment-167883555  

Thanks for the feedback @mclow.  I believe I added a satisfactory test to the test_io in commit 2449c17.

---

## Comment 3

> Username: mclow  
> Created_at: 2015-12-30 19:33:47 UTC  
> Url: https://github.com/boostorg/uuid/pull/12#issuecomment-168061171  

Sorry; somehow I missed that.  I am concerned that you're changing the alignment of the stream, which may lead to confused users. Of course, the code already does that for `std::hex`.  
  
Maybe this should use http://www.boost.org/doc/libs/1_60_0/libs/io/doc/ios_state.html to save and restore the state of the stream. What do you think?

---

## Comment 4

> Username: erichkeane  
> Created_at: 2015-12-30 19:42:33 UTC  
> Url: https://github.com/boostorg/uuid/pull/12#issuecomment-168062485  

@mclow It seems that lines 34 and 35 of uuid_io.hpp already use the flags/fill saver to do that.  I tested in a sample app (though perhaps I should add a unit test) to ensure that the stream-alignment is restored.  
  
Or does the state_saver do something different from the flags_saver that I'm missing?

---

## Comment 5

> Username: mclow  
> Created_at: 2015-12-30 19:47:57 UTC  
> Url: https://github.com/boostorg/uuid/pull/12#issuecomment-168063251  

That will teach me to look at the whole file, rather than the just the diff. Thanks for your patience.

---

## Comment 6

> Username: erichkeane  
> Created_at: 2015-12-30 19:50:22 UTC  
> Url: https://github.com/boostorg/uuid/pull/12#issuecomment-168063568  

Great, thanks!  I appreciate the comments, it definitely got me double checking everything.

---
