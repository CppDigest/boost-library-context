# #72 - Non blocking request progress. [Closed]

> Username: aminiussi  
> Created at: 2018-09-13 07:35:01 UTC  
> Updated at: 2018-12-09 22:19:55 UTC  
> Closed at: 2018-12-09 22:19:54 UTC  
> Url: https://github.com/boostorg/mpi/issues/72  

This is a copy of [SVN ticket 12829](https://svn.boost.org/trac10/ticket/12829) by @tilsche  
  
> There is a inconspicuous note in the point-to-point documentation:  
>   
>     Moreover, the MPI standard does not guarantee that the receive makes any progress before a call to "wait" or "test", although most implementations of the C MPI do allow receives to progress before the call to "wait" or "test". Boost.MPI, on the other hand, generally requires "test" or "wait" calls to make progress.  
>   
> That sounds like MPI makes no additional restrictions compared to the MPI standard, but is more strict than other MPI implementations. That is not correct. The MPI standard explicitly requires a correct MPI implementation to complete a send after the other process "posts the matching (nonblocking) receive even if process one has not yet reached the completing wait call." (Section 3.7.4). The provided Example  
>   
> The MPI standard gives an example that should not deadlock, translating it to boost.mpi with serialized datatypes (see attachment), it hangs. This is due to the two-phase transfer, where the matching receive is actually only posted on request::wait.  
>   
> Unfortunately I don't see any way around that limitation. However, the documentation should be improved. It should clearly state that this is a limitation compared to the MPI standard progress guarantee. I believe the wording should also be more clear, i.e. "A synchronous send may not complete until the matching (nonblocking) receive has reached the completing wait call."  
>

---

## Comment 1

> Username: aminiussi  
> Created at: 2018-09-13 07:42:53 UTC  
> Url: https://github.com/boostorg/mpi/issues/72#issuecomment-420914528  

If a read 3.7.4, that means that just calling the communicator::irecv  method (or any  on blocking operation ) should initiate a communication.  
  
In the current implementation, this probably hangue to the two step (size/payload) protocol.  
  
But it does also hang in #71 despite the one step communication protocol.

---

## Comment 2

> Username: aminiussi  
> Created at: 2018-09-13 07:43:45 UTC  
> Url: https://github.com/boostorg/mpi/issues/72#issuecomment-420914730  

Hanging test case.  
  
```  
#include <boost/mpi.hpp>  
#include <chrono>  
#include <vector>  
#include <boost/serialization/vector.hpp>  
  
namespace mpi = boost::mpi;  
  
int main()  
{  
  mpi::environment env;  
  mpi::communicator world;  
    
  if (world.rank() == 0) {  
    // make sure message is large enough so it is not transmitted eagerly  
    std::vector<int> msg0(100000);  
    std::vector<int> msg1(1);  
    world.send(1, 0, msg0);  
    world.send(1, 1, msg1);  
  } else {  
    mpi::request req;  
    std::vector<int> msgs[2];  
    req = world.irecv(0, 0, msgs[0]);  
    world.recv(0, 1, msgs[1]);  
    req.wait();  
  }  
}  
```

---

## Comment 3

> Username: aminiussi  
> Created at: 2018-09-13 07:56:44 UTC  
> Updated at: 2018-09-13 08:16:50 UTC  
> Url: https://github.com/boostorg/mpi/issues/72#issuecomment-420917914  

@tilsche: since I do not have access to the svn tracker (just saw the ticket), I'm copying it here.  
I suspect we might be able to actually fix it in #71.

---

## Comment 4

> Username: aminiussi  
> Created at: 2018-09-13 09:20:34 UTC  
> Url: https://github.com/boostorg/mpi/issues/72#issuecomment-420941403  

The standard mention that `MPI_Iprobe`should guarantee progress - that is, if a matching message has been sent and not caught by someone else, it will eventually succeed (3.8.1). It also mention that an initiated communication will progress (3.5). It does not explicitly say (or I haven't found it) that an `MPI_Iprobe` initiate a receiv.  
  
 Said otherwise: should example 3.14 still be okay if we replace `MPI_Irecv` with `MPI_Iprobe` ?  
  
I suspect the answer is yes but would like to find the wording.

---

## Comment 5

> Username: tilsche  
> Created at: 2018-09-20 12:35:26 UTC  
> Url: https://github.com/boostorg/mpi/issues/72#issuecomment-423166360  

First, thank you so much for taking care of these MPI issues!  
  
Unfortunately I'm afraid this won't work.  
  
You can never expect a send to complete before the receiver provided actual memory. `MPI_Irecv` does provide such memory, `MPI_Iprobe` does not.  
  
Going for the wording of example 3.14:   
  
> The first synchronous send of process zero must complete after process one **posts the matching (nonblocking) receive** even if process one has not yet reached the completing wait call. Thus, process zero will continue and execute the second send, allowing process one to complete execution.  
  
Calling `MPI_Iprobe` does not *post a matching receive* - therefore you will block the first synchronous send on process zero.  
  
I see no good way around that, even after getting rid of the size messages. I suspect you would have to ensure progress on the serialized `irecv`s in **anything** that calls MPI **and repeatedly during any blocking** call.  
```  
for (request : open_serialized_irecvs)  
    if (request->Improbe()) {  
        request->Imrecv();  
        request->recv_posted = true;  
        open_serialized_irecvs->remove(request)  
    }  
```  
So you could never call a blocking MPI function if there is an open serialized irecv for which the actual irecv has not been posted.

---

## Comment 6

> Username: aminiussi  
> Created at: 2018-09-21 05:57:40 UTC  
> Url: https://github.com/boostorg/mpi/issues/72#issuecomment-423422244  

Well, it won't work better in all situations (because you're right, probe does not post anything, I had it confirmed in a discussion on the MPI standard list and), but it will take care of the situations where we currently have a  tag mismatch between the size message and the content message. It's true that it won't behave the same way as a recv post on a single message in the sense that it will requires calls to request::test or request::wait but this is already the case (as we currently won't post the content receive before we get the size from the size message, there is some text in our documentation that could be improved) so we do not loose any functionality. (Also, message currently handled with one request are still handled with a receive, the probe/blocking recv implementation  is limited to serialized messages).  
  
So I would argue the this implementation covers more situation than the current one (considering the tag mismatch  issues disappear) and use twice as less messages for the serialized case messages.  
  
We still  need to call test/wait to ensure progress, just not for the same reason. Current implementation requires it to move from the size message to the content message, this implementation requires it until the probe find the message in the received messages queue.  
  
We still have deadlock situation we wouldn't have  with primitive messages, but we already have those, just for a different reason.  
  
I think pretending we can deal with serialized messages the same way (from user perspective) as we deal with primitive message is a little lie that fires back to us and I don't see a good way around that either. But I think this new implementation is better than the current one and covers more situation while not introducing problem we did not have.  
  
I suspect we would need acces to lower layers of the MPI implementation (which already deals with buffer sie issues internally) to fix that.  
  
It is still possible to get the old behavior by sending explicitly the archive in two steps although I cannot think of situation where that would be better.  
  
It is also possible (although, again  cannot think of a benefit in practice) to select the type of communication at run time (not at any point though, not message must be pending) by providing user access to the request::probe_message property.  
  
Also the new framework allows us to add other specialized handler in the future.

---

## Comment 7

> Username: aminiussi  
> Created at: 2018-09-21 06:14:10 UTC  
> Url: https://github.com/boostorg/mpi/issues/72#issuecomment-423425105  

In the future, if needed/justified, we could add low level api to the request objet` (test|wait)_size` and `(test|wait)_content`,  some of them would be no op for primitive messages.

---

## Comment 8

> Username: tilsche  
> Created at: 2018-09-21 08:56:33 UTC  
> Url: https://github.com/boostorg/mpi/issues/72#issuecomment-423464018  

I agree with everything you wrote. I would only make sure that the documentation reflects the guarantees that serialized communication does and does not make. That was also the original intention of my ticket.

---

## Comment 9

> Username: aminiussi  
> Created at: 2018-09-21 09:54:12 UTC  
> Url: https://github.com/boostorg/mpi/issues/72#issuecomment-423479422  

Granted, one question though: should we document the possibility to select between the two implementations at run time and what should be the default ? That could impact the documentation.  
  
There are pros and cons, the cons includes the fact that what is documented must be maintained and add more stuff to read for the user. It's flexible enough to be kept backward compatible without to much trouble though.  
  
The current default is: use probe, except with Intel MPI prior to 2019 (I hope to be able to test 2019  soon which is supposed to fix their issue with MPI_[XX]Probe but there is a license issue on "my" local cluster).

---

## Comment 10

> Username: tilsche  
> Created at: 2018-09-21 10:42:55 UTC  
> Url: https://github.com/boostorg/mpi/issues/72#issuecomment-423491150  

With two implementations you mean the current two-phase and the new probe approach? I don't see any reason to use the previous approach other than MPI implementation support. On the other hand it should be easy for a user to figure out which implementation is used such that they can determine if they are affected by the matching issue.

---

## Comment 11

> Username: aminiussi  
> Created at: 2018-09-21 11:05:44 UTC  
> Url: https://github.com/boostorg/mpi/issues/72#issuecomment-423496133  

Ok

---

## Comment 12

> Username: aminiussi  
> Created at: 2018-12-09 22:19:54 UTC  
> Url: https://github.com/boostorg/mpi/issues/72#issuecomment-445577293  

Merged on develop
