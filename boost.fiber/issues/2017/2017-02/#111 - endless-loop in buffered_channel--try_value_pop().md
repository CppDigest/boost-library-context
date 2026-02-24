# #111 - endless-loop in buffered_channel::try_value_pop() [Closed]

> Username: tschuett  
> Created at: 2017-02-23 12:28:16 UTC  
> Updated at: 2017-04-07 04:16:50 UTC  
> Closed at: 2017-02-25 09:26:53 UTC  
> Url: https://github.com/boostorg/fiber/issues/111  

I am calling try_value_pop() in the buffered channel and end up in an endless loop.  
  
``  
try_pop_: full=0 empty=0 capacity=1 closed=0 producers=0 consumers=0 consumer_idx_=0 slots_[0].cycle.load()=2  
``  
  
My guess is that ``idx`` is 0 in ``try_value_pop_()``. ``diff`` is 1 (2-(idx+1)) and the loop reloads ``consumer_idx_`` which is 0.  
  
Do you have any ideas how I ended up here? Thanks in advance.

---

## Comment 1

> Username: olk  
> Created at: 2017-02-23 18:27:11 UTC  
> Url: https://github.com/boostorg/fiber/issues/111#issuecomment-282077648  

I'm wondering, buffered_channel doesn't have a try_value_pop() member function.

---

## Comment 2

> Username: tschuett  
> Created at: 2017-02-23 19:37:16 UTC  
> Url: https://github.com/boostorg/fiber/issues/111#issuecomment-282097204  

My bad. I called try_pop (https://github.com/boostorg/fiber/blob/master/include/boost/fiber/buffered_channel.hpp#L374) which calls try_pop_ (https://github.com/boostorg/fiber/blob/master/include/boost/fiber/buffered_channel.hpp#L120) which calls try_value_pop_(https://github.com/boostorg/fiber/blob/master/include/boost/fiber/buffered_channel.hpp#L99). Here I ran in the endless loop.

---

## Comment 3

> Username: olk  
> Created at: 2017-02-23 20:44:51 UTC  
> Url: https://github.com/boostorg/fiber/issues/111#issuecomment-282115426  

could you provide test-code that demonstrates your problem

---

## Comment 4

> Username: tschuett  
> Created at: 2017-02-24 10:08:47 UTC  
> Url: https://github.com/boostorg/fiber/issues/111#issuecomment-282254077  

Here is the reproducer. The last output I get is:  
``ct2_inner calling tryp_pop``  
``try_pop_: full=0 empty=0 capacity=1 closed=0 producers=0 consumers=0 consumer_idx_=0 slots_[0].cycle.load()=2``  
The try_pop call never returns.  
  
[reproducer.zip](https://github.com/boostorg/fiber/files/798921/reproducer.zip)

---

## Comment 5

> Username: olk  
> Created at: 2017-02-25 09:26:53 UTC  
> Url: https://github.com/boostorg/fiber/issues/111#issuecomment-282472372  

buffered_channel's capacity must be a power of two - I've changed the test in the constructor

---

## Comment 6

> Username: jondo2010  
> Created at: 2017-04-07 00:00:15 UTC  
> Url: https://github.com/boostorg/fiber/issues/111#issuecomment-292367951  

I have a use case for a capacity of 1, is it possible to relax this power-of-2 requirement?

---

## Comment 7

> Username: olk  
> Created at: 2017-04-07 04:16:50 UTC  
> Url: https://github.com/boostorg/fiber/issues/111#issuecomment-292430372  

Unfortunately no - buffered_channel requires a capacity of power of two because positions inside the buffer are calculated via shift operations (for performance reasons).  
  
What is your use case for a capacity of two? Maybe unbuffered_channel is a option?
