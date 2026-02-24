# #1147 - beast flat_buffer move and swap will cancel an outstanding prepare() [Closed]

> Username: paul-d-rose  
> Created at: 2018-05-31 03:54:03 UTC  
> Updated at: 2018-05-31 15:33:11 UTC  
> Closed at: 2018-05-31 13:02:18 UTC  
> Url: https://github.com/boostorg/beast/issues/1147  

### Version of Beast  
BOOST_BEAST_VERSION 167  
  
### Steps necessary to reproduce the problem  
Use beast::flat_buffer together with asio::async_read_until  
  
### All relevant compiler information  
g++ (Ubuntu 7.3.0-16ubuntu3) 7.3.0  
  
beast::flat_buffer move ctor, move assignment and swap all "cancel" outstanding prepare()  
  
This happens when last_ is assigned to out_ instead of other.last_ during move/swap.  
  
This seems like a very sane and reasonable thing to do.  Why would anyone separate their prepare() and commit() with an intervening move()?  
  
However, asio.async_read_until() does exactly this, and depends on the effects of prepare() surviving a move().  
  
Take any simple example that uses asio::async_read_until() and change it from asio.streambuf to beast.flat_buffer, and the reads will never complete.  
  
I locally changed flat_buffer to initialize last_ from other_.last during move ctor, move assign, and swap, and verified that beast.flat_buffer and asio.async_read_until would now play nice together.  
  
I filed a bug on the boost system before noticing that this was the right place.  It was filed under asio, since beast wasn't on the "project" drop box.   
  
  
Thanks,  
Paul Rose

---

## Comment 1

> Username: vinniefalco  
> Created at: 2018-05-31 04:42:29 UTC  
> Url: https://github.com/boostorg/beast/issues/1147#issuecomment-393400292  

Wow yeah... so, hmm... how do I put this? You really stepped in it :) Where do I start...  
  
> Take any simple example that uses asio::async_read_until() and change it from asio.streambuf to beast.flat_buffer  
  
I am so sorry about this because I had a mix-up of understanding about Asio's DynamicBuffer concept. The DynamicBuffer in Beast is not the same as the one in Asio. The Beast dynamic buffers are true storage types, while the Asio ones act more like a reference. You said you're using `asio::async_read_until`. But that can never work correctly, because the Asio routines want to take ownership. You must be using `std::move` or otherwise passing an rvalue as the buffer argument. Unfortunately by doing this there is no way for you to get at the data since you have transferred ownership to the composed operation. And it will be destroyed before your completion handler is invoked.  
  
> asio.async_read_until() does exactly this, and depends on the effects of prepare() surviving a move().  
  
Asio's dynamic buffers are not true storage types. They do not own the memory they refer to. That's why Asio has to have overloads for the legacy `basic_streambuf` (which is *not* a DynamicBuffer):  
  
https://www.boost.org/doc/libs/1_67_0/doc/html/boost_asio/reference/async_read_until/overload5.html  
  
Ultimately, Beast's dynamic buffers as written are not compatible with Asio stream algorithms which accept dynamic buffers using forwarding references (`DynamicBuffer&&`):  
  
https://www.boost.org/doc/libs/1_67_0/doc/html/boost_asio/reference/async_read_until.html  
  
If you want to use one of Beast's dynamic buffers with those Asio routines, you have to write a wrapper. It isn't terribly difficult, just make a class that holds a pointer or reference to the beast dynamic buffer, which also meets the requirements of **DynamicBuffer** by invoking the corresponding members of the beast dynamic buffer. Here's a partial sketch:  
  
```  
template<class DynamicBuffer>  
class dynamic_buffer_reference_wrapper  
{  
    DynamicBuffer& buffer_;  
  
public:  
    using const_buffers_type = typename DynamicBuffer::const_buffers_type;  
  
    ....  
  
    const_buffers_type data() const noexcept  
    {  
        return buffer_.data();  
    }  
  
    ....  
};  
```  
  
Now you can make a utility function which can be used at call sites:  
  
```  
template<class DynamicBuffer>  
dynamic_buffer_reference_wrapper<DynamicBuffer>  
dynamic_buffer_ref(DynamicBuffer& buffer)  
{  
    return dynamic_buffer_reference_wrapper<DynamicBuffer>(buffer);  
}  
```  
  
I believe strongly that the Asio (and Networking.TS) specification of DynamicBuffer is defective, and that Beast is doing it the right way. If you want to know more, check out this paper:  
https://rawgit.com/vinniefalco/cpp/master/d1100r0.html  
  
I'd love to hear your thoughts on all of this, as I will be presenting this paper in November at the San Diego meeting.

---

## Comment 2

> Username: paul-d-rose  
> Created at: 2018-05-31 12:08:09 UTC  
> Url: https://github.com/boostorg/beast/issues/1147#issuecomment-393509399  

Mr. Falco,  
  
Thanks so much for the prompt and detailed reply.  
  
My fault for diving in without fully digesting the Beast docs w.r.t  
DynamicBuffer.  
  
It is unfortunate that the that beast::flat_buffer satisfies the  
asio::is_dynamic_buffer concept query, and that allows the async_read_until  
overload expecting a DynamicBuffer to compile without complaint.  
  
I will certainly read the paper.  
  
I'll also consider using the wrapper, thanks for taking the time to suggest  
it.  Probably, however, I'll forgo read_until and work with  
socket.async_read_some.  
  
  
Thanks again,  
Paul Rose  
  
  
  
  
On Wed, May 30, 2018 at 11:42 PM, Vinnie Falco <notifications@github.com>  
wrote:  
  
> Wow yeah... so, hmm... how do I put this? You really stepped in it :)  
> Where do I start...  
>  
> Take any simple example that uses asio::async_read_until() and change it  
> from asio.streambuf to beast.flat_buffer  
>  
> I am so sorry about this because I had a mix-up of understanding about  
> Asio's DynamicBuffer concept. The DynamicBuffer in Beast is not the same as  
> the one in Asio. The Beast dynamic buffers are true storage types, while  
> the Asio ones act more like a reference. You said you're using  
> asio::async_read_until. But that can never work correctly, because the  
> Asio routines want to take ownership. You must be using std::move or  
> otherwise passing an rvalue as the buffer argument. Unfortunately by doing  
> this there is no way for you to get at the data since you have transferred  
> ownership to the composed operation. And it will be destroyed before your  
> completion handler is invoked.  
>  
> asio.async_read_until() does exactly this, and depends on the effects of  
> prepare() surviving a move().  
>  
> Asio's dynamic buffers are not true storage types. They do not own the  
> memory they refer to. That's why Asio has to have overloads for the legacy  
> basic_streambuf (which is *not* a DynamicBuffer):  
>  
> https://www.boost.org/doc/libs/1_67_0/doc/html/boost_  
> asio/reference/async_read_until/overload5.html  
>  
> Ultimately, Beast's dynamic buffers as written are not compatible with  
> Asio stream algorithms which accept dynamic buffers using forwarding  
> references (DynamicBuffer&&):  
>  
> https://www.boost.org/doc/libs/1_67_0/doc/html/boost_  
> asio/reference/async_read_until.html  
>  
> If you want to use one of Beast's dynamic buffers with those Asio  
> routines, you have to write a wrapper. It isn't terribly difficult, just  
> make a class that holds a pointer or reference to the beast dynamic buffer,  
> which also meets the requirements of *DynamicBuffer* by invoking the  
> corresponding members of the beast dynamic buffer. Here's a partial sketch:  
>  
> template<class DynamicBuffer>  
> class dynamic_buffer_reference_wrapper  
> {  
>     DynamicBuffer& buffer_;  
>  
> public:  
>     using const_buffers_type = typename DynamicBuffer::const_buffers_type;  
>  
>     ....  
>  
>     const_buffers_type data() const noexcept  
>     {  
>         return buffer_.data();  
>     }  
>  
>     ....  
> };  
>  
> Now you can make a utility function which can be used at call sites:  
>  
> template<class DynamicBuffer>  
> dynamic_buffer_reference_wrapper<DynamicBuffer>  
> dynamic_buffer_ref(DynamicBuffer& buffer)  
> {  
>     return dynamic_buffer_reference_wrapper<DynamicBuffer>(buffer);  
> }  
>  
> I believe strongly that the Asio (and Networking.TS) specification of  
> DynamicBuffer is defective, and that Beast is doing it the right way. If  
> you want to know more, check out this paper:  
> https://rawgit.com/vinniefalco/cpp/master/d1100r0.html  
>  
> I'd love to hear your thoughts on all of this, as I will be presenting  
> this paper in November at the San Diego meeting.  
>  
> —  
> You are receiving this because you authored the thread.  
> Reply to this email directly, view it on GitHub  
> <https://github.com/boostorg/beast/issues/1147#issuecomment-393400292>,  
> or mute the thread  
> <https://github.com/notifications/unsubscribe-auth/Al8Y0JIlZpbIlpnNlvftzrbXZXlt3qKgks5t33S7gaJpZM4UUYRZ>  
> .  
>

---

## Comment 3

> Username: paul-d-rose  
> Created at: 2018-05-31 13:02:18 UTC  
> Url: https://github.com/boostorg/beast/issues/1147#issuecomment-393522579  

I hindsight, this should have been obvious to me.   No amount of "fixing" a move operation can cover the fact that "my" flat_buffer was moved from.  Of course I could ever get the data.  
  
My example  had been reduced to a minimum.  The "fix" allowed the read to complete with the right number of bytes, but I didn't inspect my buffer.  Empty, of course.  
  
5 minutes of clear thinking could and should have saved 90 minutes of hacking ( and posting ).

---

## Comment 4

> Username: vinniefalco  
> Created at: 2018-05-31 13:14:44 UTC  
> Updated at: 2018-05-31 13:15:52 UTC  
> Url: https://github.com/boostorg/beast/issues/1147#issuecomment-393525910  

> 5 minutes of clear thinking could and should have saved 90 minutes of hacking ( and posting ).  
  
And yet I made the same mistake myself when trying out Asio's new read untils...  
  
This whole thing is inherently confusing (and it certainly doesn't help that Beast has its own DynamicBuffer concept which is slightly different).

---

## Comment 5

> Username: vinniefalco  
> Created at: 2018-05-31 13:17:25 UTC  
> Url: https://github.com/boostorg/beast/issues/1147#issuecomment-393526646  

> I'll also consider using the wrapper,   
  
I can write it for you and throw it in the beast/experimental directory

---

## Comment 6

> Username: paul-d-rose  
> Created at: 2018-05-31 13:43:47 UTC  
> Url: https://github.com/boostorg/beast/issues/1147#issuecomment-393534425  

That is a very kind offer.  
  
If you think it is valuable for others, then please do.  
  
But I understand the concept now, and I'm happy to write the wrapper  
myself.  I'd likely only use it as a tool to decide if I want to use  
async_read_until, and may ultimately end up discarding it.  
  
I like the *idea* of read_until.  In my case my match_condition is  
efficient ( Stateless: I just peek at a length prefix and consume nothing  
until the distance(begin,end) is sufficient ), and using a single asio call  
is attractive.  Using flat_buffer is attractive because I can pass the  
buffer to legacy handlers that expect a contiguous chunk of bytes.  
  
Looking into the actual implementation of aync_read_until, however, it  
isn't clear whether I may just be better off without it.  
  
  
  
  
  
  
  
On Thu, May 31, 2018 at 8:17 AM, Vinnie Falco <notifications@github.com>  
wrote:  
  
> I'll also consider using the wrapper,  
>  
> I can write it for you and throw it in the beast/experimental directory  
>  
> —  
> You are receiving this because you modified the open/close state.  
> Reply to this email directly, view it on GitHub  
> <https://github.com/boostorg/beast/issues/1147#issuecomment-393526646>,  
> or mute the thread  
> <https://github.com/notifications/unsubscribe-auth/Al8Y0CC6MykW4nUViZn7v3VsM6JCy6Wvks5t3-1pgaJpZM4UUYRZ>  
> .  
>

---

## Comment 7

> Username: vinniefalco  
> Created at: 2018-05-31 14:14:34 UTC  
> Url: https://github.com/boostorg/beast/issues/1147#issuecomment-393544286  

> Looking into the actual implementation of aync_read_until, however, it isn't clear whether I may just be better off without it.  
  
Why is that? `read_until_match_op` does exactly what you want...

---

## Comment 8

> Username: paul-d-rose  
> Created at: 2018-05-31 15:00:56 UTC  
> Url: https://github.com/boostorg/beast/issues/1147#issuecomment-393559754  

Yes, I see.  
  
Using the conceptually clean chained async_read ( one for fixed header, one  
for discovered length ) will give me two system calls for every message (  
verified  just now with strace ).  No magic there.  
  
Using socket.async_read_some with a larger buffer is will reduce the number  
of system calls dramatically (especially with many small messages), and  
async_read_until will do this for me via read_until_match_op.  Re-creating  
this would be a waste.  
  
I'm going to try creating the wrapper myself, for the learning experience  
if nothing else.  Please don't let that stop you from making an "official"  
one if you think it is more broadly useful.  
  
  
  
  
  
  
  
  
  
  
  
On Thu, May 31, 2018 at 9:14 AM, Vinnie Falco <notifications@github.com>  
wrote:  
  
> Looking into the actual implementation of aync_read_until, however, it  
> isn't clear whether I may just be better off without it.  
>  
> Why is that? read_until_match_op does exactly what you want...  
>  
> —  
> You are receiving this because you modified the open/close state.  
> Reply to this email directly, view it on GitHub  
> <https://github.com/boostorg/beast/issues/1147#issuecomment-393544286>,  
> or mute the thread  
> <https://github.com/notifications/unsubscribe-auth/Al8Y0AL2dGaDf8GQS_lfQ23Fmwv5fEh_ks5t3_rZgaJpZM4UUYRZ>  
> .  
>

---

## Comment 9

> Username: vinniefalco  
> Created at: 2018-05-31 15:13:32 UTC  
> Url: https://github.com/boostorg/beast/issues/1147#issuecomment-393563874  

> Using socket.async_read_some with a larger buffer is will reduce the number of system calls dramatically  
  
Yes, that is correct. `beast::websocket::stream` uses the same technique. And your intuition about avoiding the number of system calls is also correct, it is the single largest factor which affects performance. The cost of doing a buffer-copy (which is usually needed when buffering messages) is insignificant compared to doing one less system call.

---

## Comment 10

> Username: paul-d-rose  
> Created at: 2018-05-31 15:33:10 UTC  
> Url: https://github.com/boostorg/beast/issues/1147#issuecomment-393571227  

The wrapper is working beautifully, thank you.  
  
I'd send it, but it hardly seems worth it.  It amounted to about 27 lines,  
which you'd probably have to spend more time formatting to your liking than  
it would take you to create it from scratch :)  
  
  
On Thu, May 31, 2018 at 10:13 AM, Vinnie Falco <notifications@github.com>  
wrote:  
  
> Using socket.async_read_some with a larger buffer is will reduce the  
> number of system calls dramatically  
>  
> Yes, that is correct. beast::websocket::stream uses the same technique.  
> And your intuition about avoiding the number of system calls is also  
> correct, it is the single largest factor which affects performance. The  
> cost of doing a buffer-copy (which is usually needed when buffering  
> messages) is insignificant compared to doing one less system call.  
>  
> —  
> You are receiving this because you modified the open/close state.  
> Reply to this email directly, view it on GitHub  
> <https://github.com/boostorg/beast/issues/1147#issuecomment-393563874>,  
> or mute the thread  
> <https://github.com/notifications/unsubscribe-auth/Al8Y0HqBzzR236wslTFiHLZr9-V4FxNJks5t4AiwgaJpZM4UUYRZ>  
> .  
>
