# #84 - Proposal - Add API for getting/setting thread name in Boost? [Closed]

> Username: bobsummerwill  
> Created at: 2016-05-09 23:56:17 UTC  
> Updated at: 2019-11-06 15:30:03 UTC  
> Closed at: 2018-10-09 04:39:29 UTC  
> Url: https://github.com/boostorg/thread/issues/84  

As discussed on the mailing list:  
  
http://lists.boost.org/Archives/boost/2016/05/229385.php  
http://lists.boost.org/Archives/boost/2016/05/229426.php  
http://lists.boost.org/Archives/boost/2016/05/229452.php  
(more to come)  
  
The proposal is for us to add functions to the Boost thread API for getting and setting thread names (to ease debugging).   It appears that there is quite well-known "cut-and-paste" boilerplate code for this on at least Windows, Linux and OS X and probably more.    Can have common API, but the (very simple) implementation will vary.  
  
If nobody else steps up, I would be happy to add this to my own backlog.  
  
I've also been working with an external developer which is getting Ethereum   
C++ working on Alpine Linux, as a statically linked executable using musl,   
rather than glibc.   
One rather confusing element was related to setting and getting thread   
names, which is the process of working through this issue, I find appears   
not to have made its way into either Boost or the C++11 standard library,   
though it must be a very common cross-platform use-case.   
https://github.com/ethereum/libweb3core/pull/73   
https://github.com/ethereum/libweb3core/pull/73/files   
I've just added this comment-block, while fixing the issue:   
/// Set the current thread's log name.   
///   
/// It appears that there is not currently any cross-platform way of setting   
/// thread names either in Boost or in the C++11 runtime libraries. What is   
/// more, the API for 'pthread_setname_np' is not even consistent across   
/// platforms which implement it.   
///   
/// A proposal to add such functionality on the Boost mailing list, which   
/// I assume never happened, but which I should follow-up and ask about.   
/// http://boost.2283326.n4.nabble.com/Adding-an-option-to-set-the-name-of-a-boost-thread-td4638283.html   
///   
/// man page for 'pthread_setname_np', including this crucial snippet of   
/// information ... "These functions are nonstandard GNU extensions."   
/// http://man7.org/linux/man-pages/man3/pthread_setname_np.3.html   
///   
/// Stack Overflow "Can I set the name of a thread in pthreads / linux?"   
/// which includes useful information on the minor API differences between   
/// Linux, BSD and OS X.   
/// http://stackoverflow.com/questions/2369738/can-i-set-the-name-of-a-thread-in-pthreads-linux/7989973#7989973   
///   
/// musl mailng list posting "pthread set name on MIPs" which includes the   
/// information that musl doesn't currently implement 'pthread_setname_np'   
/// https://marc.info/?l=musl&m=146171729013062&w=1   
void setThreadName(std::string const& _n);   
Would I be right in assuming that this never happened?   
http://boost.2283326.n4.nabble.com/Adding-an-option-to-set-the-name-of-a-boost-thread-td4638283.html   
If not, where can I log an issue to request that we revisit that? From   
what I can see, everybody is likely just cut-and-pasting much the same code   
for this functionality.   
http://stackoverflow.com/questions/10121560/stdthread-naming-your-thread   
Cheers,   
Bob Summerwill

---

## Comment 1

> Username: bobsummerwill  
> Created at: 2016-05-09 23:57:47 UTC  
> Url: https://github.com/boostorg/thread/issues/84#issuecomment-218023973  

PS.   This all came out of the work at https://github.com/ethereum/webthree-umbrella/issues/495.

---

## Comment 2

> Username: bobsummerwill  
> Created at: 2016-05-10 08:56:38 UTC  
> Url: https://github.com/boostorg/thread/issues/84#issuecomment-218098116  

@viboes said ...  
  
What I have in mind was to use the boost::thread_attributes, but we are unable to do it in OSX as the set name in OSX sets the name of the current thread :(  
After some thought I believe we could do it by using a trampoline that will do the setting before calling the user main thread function, but this is more complex and needs to store the name in the thread_data context.  
  
IIUC, what you are proposing and the single thing that could be done in an almost portable way and easily is to define two non-member functions to set and get the thread name on namespace this_thread or any other. Is this what you are proposing? If this is the case, this functionality can be developed completely independently of Boost.Thread, isn't' it?

---

## Comment 3

> Username: bobsummerwill  
> Created at: 2016-05-10 09:00:55 UTC  
> Updated at: 2016-05-10 09:01:05 UTC  
> Url: https://github.com/boostorg/thread/issues/84#issuecomment-218099250  

> Is this what you are proposing? If this is the case, this  
> functionality can be developed completely independently of Boost.Thread, isn't' it?  
  
Maybe :-)   I don't necessarily have a specific preference.    I don't have deep knowledge of Boost's architecture.    You would know better than me what the idiomatic way of achieving of this aim within Boost's existing patterns would be.

---

## Comment 4

> Username: viboes  
> Created at: 2018-10-09 04:39:29 UTC  
> Url: https://github.com/boostorg/thread/issues/84#issuecomment-428057235  

Closed as no PR provided since more than 1 year

---

## Comment 5

> Username: bobsummerwill  
> Created at: 2019-11-06 15:30:02 UTC  
> Url: https://github.com/boostorg/thread/issues/84#issuecomment-550362223  

Hey @viboes!  
  
So I "got busy" for 3 years, but I was wondering if this thread-naming functionality is still missing in Boost?  If so, I might have another try at this.  
  
Seems it is still missing in std::thread for sure.
