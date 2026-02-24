# #17 Add text_ipc_message_queue_backend [Closed]

> Username: Lingxi-Li  
> Created at: 2015-10-15 10:59:27 UTC  
> Updated at: 2016-05-29 12:16:52 UTC  
> Closed at: 2016-05-29 12:16:52 UTC  
> Url: https://github.com/boostorg/log/pull/17  

It's common to use a viewer process to monitor the interaction of multiple logger processes. I think it may be appropriate for Boost.Log to provide an IPC backend out of the box. Added is a backend that sends formatted log messages to an interprocess message queue created by `boost::interprocess::message_queue`. Users may encode record attributes to the message using JSON and alike. The viewer can then decode the attributes and perform filtering there.

---

## Comment 1

> Username: Lastique  
> Created_at: 2015-10-15 14:23:29 UTC  
> Url: https://github.com/boostorg/log/pull/17#issuecomment-148400827  

Thank you for the submission. I took a quick glance and have some comments, in no particular order:  
- Please, fix the copyrights.  
- Documentation is missing. As part of it, there should probably be a motivation for using this sink backend instead of e.g. text_ostream_backend with a socker stream from Boost.ASIO.  
- Please, avoid including Boost.Interprocess headers in public Boost.Log headers. Pimpl design is specifically used for this reason.  
- Pass message queue name by const reference.  
- A few naming corrections are needed: replace msg_queue with message_queue everywhere, set_msg_queue should be set_message_queue_name, m_pQue should be m_pQueue or something.  
- Avoid assigning 0 to pointers, use NULL.  
- Why the IPC queue is created with new? Can't it be stored as a regular data member?  
- Don't swallow any exceptions. Queue creation failure and failures to enqueue log records should be signalled to the caller.  
- Log message can be silently truncated when passed to try_send. This is not acceptable as it may result in information loss. If there is a limit due to the queue implementation, it should be configurable, and the behavior on exceeding the limit should be configurable.  
- The name try_send also implies that the method may fail to enqueue the record. Silently ignoring the failure does not look well to me.  
- Why the sink only opens a queue and never creates it? The user should not be required to deal with creating a proper queue himself. The queue implementation should be a hidden detail of the sink backend.  
- There should be a corresponding component for reading messages from the queue.  
- Avoid using macros in examples.

---

## Comment 2

> Username: Lingxi-Li  
> Created_at: 2015-10-15 16:29:30 UTC  
> Url: https://github.com/boostorg/log/pull/17#issuecomment-148445909  

Thanks for the detailed reply. I'm going to fix the issues. Following are explanations for a few of your comments:  
- **Pass message queue name by const reference.** It may seem weird at first glance. I did it mainly for efficiency considerations. Specifically, if a C-style string is passed, a copy is made anyway. But a non-const `string` allows efficient swap with the class member, requiring no additional copy.  
- **Avoid assigning 0 to pointers, use NULL.** Mainly to avoid include `<cstddef>` and alike for the definition of `NULL`. Never mind, gonna fix it.  
- **Why the IPC queue is created with new? Can't it be stored as a regular data member?** No, it can't. Current [interface](http://www.boost.org/doc/libs/1_59_0/doc/html/boost/interprocess/message_queue_t.html) of `boost::interprocess::message_queue` does not support lazy initialization. I found this issue and have already submitted a [patch](https://github.com/boostorg/interprocess/pull/20) for review.  
- **Queue creation failure and failures to enqueue log records should be signalled to the caller.** Please see the following.  
- **...If there is a limit due to the queue implementation, it should be configurable, and the behavior on exceeding the limit should be configurable.** Yes, this is a limit of the queue implementation. It is configurable, but only at queue creation. In the current design, the sink backend simply opens the queue, not creates it.  
- **The name try_send also implies that the method may fail to enqueue the record. Silently ignoring the failure does not look well to me.** The only way to signal such failures seems to be throwing an exception. However, this may hamper performance considerably. Consider the case when the queue is full, and nobody is reading it (e.g., the viewer process is closed before the logging process exits), then there will be an exception thrown for each subsequent log record.  
- **Why the sink only opens a queue and never creates it?** I think queue creation should be a task of the viewer, not the logger (the sink backend, to be specific). The viewer creates the queue and dictates its various settings, such as maximum queue length and maximum size allowed per message. It's also the viewer's responsibility to destroy the queue. The logger does not create the queue on its own, for logging to a queue with no viewer (consumer) is considered meaningless (like a source with no sink).

---

## Comment 3

> Username: Lastique  
> Created_at: 2015-10-15 17:49:44 UTC  
> Url: https://github.com/boostorg/log/pull/17#issuecomment-148471051  

On 15.10.2015 19:29, Li, Lingxi wrote:  
  
> - _Why the IPC queue is created with new? Can't it be stored as a  
>   regular data member?_ No, it can't. Current interface  
>   http://www.boost.org/doc/libs/1_59_0/doc/html/boost/interprocess/message_queue_t.html  
>   of |boost::interprocess::message_queue| does not support lazy  
>   initialization. I found this issue and have already submitted a  
>   patch https://github.com/boostorg/interprocess/pull/20 for review.  
  
I see. Then, at least, make it a |unique_ptr| then (see src/unique_ptr.hpp).  
  
> - _Queue creation failure and failures to enqueue log records should  
>   be signalled to the caller._ Please see the following.  
> - _...If there is a limit due to the queue implementation, it should  
>   be configurable, and the behavior on exceeding the limit should be  
>   configurable._ Yes, this is a limit of the queue implementation. It  
>   is configurable, but only at queue creation. In the current design,  
>   the sink backend simply opens the queue, not creates it.  
  
I'll expand on the choice who creates the queue below. As for string   
truncation, I think it should be an option of the backend, with silent   
truncation being one (not the default) option. I can suggest throwing an   
exception or dropping the message altogether as other alternatives (with   
exception being the default).  
  
There is also a matter of marking the fact of truncation. In my projects   
I found that with simple truncation log messages are often ambiguous, as   
one can't tell if the truncation actually occurred or the message is   
complete. That's not something to implement as a part of this sink, but   
it's the first sink in Boost.Log that might require such feature. I   
think I'll need to implement a decorator for this.  
  
> - _The name try_send also implies that the method may fail to enqueue  
>   the record. Silently ignoring the failure does not look well to me._  
>   The only way to signal such failures seems to be throwing an  
>   exception. However, this may hamper performance considerably.  
>   Consider the case when the queue is full, and nobody is reading it  
>   (e.g., the viewer process is closed before the logging process  
>   exits), then there will be an exception thrown for each subsequent  
>   log record.  
  
I think this should be controlled with a policy or an option for the   
backend. There are cases when data loss is undesirable, and the backend   
is better block when the queue is full. Together with async frontends   
this may be a viable option. There may be cases when queue overflow is   
not expected, and signalling it with an exception would be fitting.   
Dropping log records on queue overflow is also a possible option, but   
IMO, as any data loss, it should be an opt in on the user's behalf.  
  
> - _Why the sink only opens a queue and never creates it?_ I think  
>   queue creation should be a task of the viewer, not the logger (the  
>   sink backend, to be specific). The viewer creates the queue and  
>   dictates its various settings, such as maximum queue length and  
>   maximum size allowed per message. It's also the viewer's  
>   responsibility to destroy the queue. The logger does not create the  
>   queue on its own, for logging to a queue with no viewer (consumer)  
>   is considered meaningless (like a source with no sink).  
  
I think that both ways have their merits. Having an IPC queue created by   
the sink has an advantage that the writer is more self-contained and in   
control. Different sinks may have different requirements on the queue   
parameters, and it is the viewer(s) who may need to adapt. I can see the   
case where there are multiple readers of a single queue (does   
|interprocess::message_queue| allow this, BTW?)  
  
There are other points to consider. I'd like the IPC queue   
implementation (or the underlying OS mechanism used for IPC) to be an   
implementation detail of Boost.Log. This means that the user in no case   
should have to use Boost.Interprocess to set up the queue. In fact, I   
want to be able to replace |interprocess::message_queue| with something   
else at some point or be able to use different mechanisms on different   
platforms. In this design whether the reader or the writer creates the   
queue becomes less important as both have to be part of Boost.Log. I   
think it will also make the use of the library easier since the user   
only has to work with Boost.Log components to set up logging.

---

## Comment 4

> Username: Lingxi-Li  
> Created_at: 2015-10-20 13:36:39 UTC  
> Url: https://github.com/boostorg/log/pull/17#issuecomment-149570373  

Hi,  
  
This pull request is now ready for a second-round review. All previously mentioned issues should have been fixed by now :smiley:

---

## Comment 5

> Username: Lastique  
> Created_at: 2015-10-23 08:16:29 UTC  
> Url: https://github.com/boostorg/log/pull/17#issuecomment-150505626  

Thanks. I'm planning to have a closer look later, after Boost 1.60 is released. If everything is ok, I will merge the PR then. I don't want to add major features to the library before the release as this would complicate merging fixes to master, if there appear any.  
  
I have a comment about the test. Could you integrate it to the auto-testing structure? Unless the test has some special requirements, I think it should be enough to move it to test/run and name it with sink_ prefix. Also, instead of asserts you should use Boost.Test as asserts are no-op in release builds.

---

## Comment 6

> Username: Lingxi-Li  
> Created_at: 2015-10-25 05:58:07 UTC  
> Url: https://github.com/boostorg/log/pull/17#issuecomment-150894903  

Thanks for the reply. I will continue improving the PR during the time.

---

## Comment 7

> Username: Lingxi-Li  
> Created_at: 2015-12-29 15:00:21 UTC  
> Url: https://github.com/boostorg/log/pull/17#issuecomment-167804758  

Any news on this?

---

## Comment 8

> Username: Lastique  
> Created_at: 2015-12-29 15:08:57 UTC  
> Url: https://github.com/boostorg/log/pull/17#issuecomment-167807175  

Yes, I merged this PR to a local branch and started some cleanup/refactor work in it. When I'm done with it I will merge it to develop and close this PR.

---

## Comment 9

> Username: Lastique  
> Created_at: 2016-05-29 12:16:52 UTC  
> Url: https://github.com/boostorg/log/pull/17#issuecomment-222357584  

Merged to develop. Thanks again.

---
