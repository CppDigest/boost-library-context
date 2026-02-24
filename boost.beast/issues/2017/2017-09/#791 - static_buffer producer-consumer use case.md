# #791 - static_buffer producer/consumer use case [Closed]

> Username: markwrk1  
> Created at: 2017-09-21 19:22:49 UTC  
> Updated at: 2018-04-17 06:44:11 UTC  
> Closed at: 2017-09-21 21:15:22 UTC  
> Url: https://github.com/boostorg/beast/issues/791  

I wanted to share a use case I got bit on, as I think it could use some further discussion.  
  
Background: my use-case is somewhat like a proxy, where I am using some DynamicBuffer in a producer/consumer-type fashion.  I can have an async operation reading data into a prepare()'d part of the buffer, while at *the same time* doing an async write out of data() in a different part of the buffer.  Having a bounded buffer usage is important to this application, and Beast's ability to do flow control is one of the reasons I chose it - thanks for your work!  
  
I had originally chosen a static_buffer, described in the docs as a circular buffer, as a good fit for this use case.  But I ended up with corrupted data passing through.  I've switched to multi_buffer as a workaround in the meantime.  Looking through the beast code, I found the following in static_buffer_base:  
  
```  
inline  
void  
static_buffer_base::  
consume(std::size_t size)  
{  
    if(size < in_size_)  
    {  
        in_off_ = (in_off_ + size) % capacity_;  
        in_size_ -= size;  
    }  
    else  
    {  
        // rewind the offset, so the next call to prepare  
        // can have a longer continguous segment. this helps  
        // algorithms optimized for larger buffesr.  
        in_off_ = 0;  
        in_size_ = 0;  
    }  
}  
```  
  
Looks like consuming can invalidate buffers previously prepare()'d but not commit()'ted.  
  
I have several suggestions to consider:  
1) At the very least, the documentation for static_buffer methods should describe when other buffers are invalidated.  I see this was done for flat_buffer, but I also notice it missing for flat_static_buffer.  More general, I think it would be good to add a section of documentation summarizing the differences in the different buffer types, so that users can answer the question, "Which buffer should I use?"  I know it would have saved me some time.  
  
2) We should next reconsider whether the optimization in static_buffer_base::consume() is worth excluding the producer/consumer use case.  You'll still return a max of only two buffers, and if a single contiguous buffer is really that important to one's use case, they should use flat_static_buffer.  Also, it's not easy to derive your own class either since this is in the "base".  
  
3) Renaming static_buffer to circular_static_buffer would help contrast it with the flat*_buffers.  Then there could be a corresponding circular_buffer with runtime size to round out the options (I'd use this one).  I realize this would be an API change so this is a lower priority suggestion.

---

## Comment 1

> Username: vinniefalco  
> Created at: 2017-09-21 19:37:04 UTC  
> Url: https://github.com/boostorg/beast/issues/791#issuecomment-331259410  

The **DynamicBuffer** requirements already specify that all sequences are invalidated on any call to `prepare` or `consume`:  
http://www.boost.org/doc/libs/develop/libs/beast/doc/html/beast/concepts/DynamicBuffer.html  
  
This is not something I can change, as the concept comes from [networking.ts]. However, I can make this clear by repeating it in the reference for each dynamic buffer member function (see https://github.com/boostorg/beast/issues/792).  
  
Perhaps this might help, there is already an example of an HTTP relay in the documentation:  
http://www.boost.org/doc/libs/develop/libs/beast/doc/html/beast/more_examples/http_relay.html  
  
The documentation already has a page outlining the differences between dynamic buffer implementations, see:  
http://www.boost.org/doc/libs/develop/libs/beast/doc/html/beast/using_io/buffer_types.html  
  
The optimization in `static_buffer_base` is definitely worth it, because the websocket implementation depends on it. Although maybe I can change it. Still, that won't help you because of the possibility that the returned buffer sequences become invalidated on `prepare` or `consume`.

---

## Comment 2

> Username: markwrk1  
> Created at: 2017-09-21 21:15:22 UTC  
> Url: https://github.com/boostorg/beast/issues/791#issuecomment-331284092  

Thanks for the pointers.  I probably did at first read the "Buffer Types" and "DynamicBuffer Requirements" but it must not have sunk in by the time I was looking at the concrete implementations' docs.   My bad.  Thanks for at least opening the issue #792 to make it evident everywhere.  
  
The HTTP relay example is synchronous, which won't have the problem of multiple actions on the buffer at once.  If you wrote that example to be asynchronous to handle large streaming bodies, you might see the same problem.   (I suppose you could wait for the async write to be done before beginning the next async read, but that would be slightly less efficient than overlapping the read and write if you *could*).  
  
My case is a little more involved to make that parenthesized option infeasible, but without getting deep into my details, I think one answer is to write my own circular_buffer that *can* place additional guarantees to allow what I want.   It's all internal use so I don't see any conflicts.  
  
Since static_buffer is so close, I thought I'd make sure it was intended that way.  Sounds like it is so we can close this issue.

---

## Comment 3

> Username: vinniefalco  
> Created at: 2017-09-21 22:48:30 UTC  
> Url: https://github.com/boostorg/beast/issues/791#issuecomment-331302823  

> I think one answer is to write my own circular_buffer that can place additional guarantees to allow what I want  
  
Yep, that's the preferred solution and also the one I had in mind when I wrote the library. It was always the intent of Beast to work with custom dynamic buffer implementations, that's why beast provides the trait and the documentation (which will be discarded once net-ts asio is updated, which has its own traits and documentation for the DynamicBuffer concept).

---

## Comment 4

> Username: petertufvesson  
> Created at: 2018-04-13 08:25:08 UTC  
> Url: https://github.com/boostorg/beast/issues/791#issuecomment-381062780  

Hello  
  
I have a related newbie question.. I am aiming for concurrent asynchronous read/writes to a websocket. Right now I use one multi_buffer for this task, but I also get corrupted data, written data ending up among the read data.  
  
Is it better/possible to use two buffers instead, one for the incoming websocket data, and another for outgoing websocket data? How would you do it...  
  
For the future, please consider to extend the (very good) examples so they not only echo incoming data, but allow asynch read/write at the same time. Perhaps pointing out if it is possible/good to use a single buffer, or not.  
  
Do you know if there are any compiler debug options I can set to be able to debug the buffer content in a good way? (Showing prepared space, committed, etc.)  
I tried "-DBOOST_ASIO_ENABLE_BUFFER_DEBUGGING" but not sure it was the best one...  
  
Thank you very much  
/Peter

---

## Comment 5

> Username: vinniefalco  
> Created at: 2018-04-13 14:01:27 UTC  
> Url: https://github.com/boostorg/beast/issues/791#issuecomment-381144917  

You can't use the same dynamic buffer for both reading and writing concurrently. Declare a second instance of `multi_buffer` and use that.  
  
>Do you know if there are any compiler debug options I can set to be able to debug the buffer content in a good way? (Showing prepared space, committed, etc.)  
  
Hmm... there are no facilities to do that. It isn't something that is commonly done. Dynamic buffers just work (when used correctly).  
  
> I tried "-DBOOST_ASIO_ENABLE_BUFFER_DEBUGGING" but not sure it was the best one...  
  
That enables Asio's run-time checks on buffer sequences, not dynamic buffers.

---

## Comment 6

> Username: petertufvesson  
> Created at: 2018-04-17 06:44:11 UTC  
> Url: https://github.com/boostorg/beast/issues/791#issuecomment-381866382  

Thank you Vinnie. All works well now!
