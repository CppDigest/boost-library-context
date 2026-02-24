# #94 - Question regarding yield [Closed]

> Username: asharif  
> Created at: 2016-11-10 16:48:38 UTC  
> Updated at: 2016-11-27 18:25:05 UTC  
> Closed at: 2016-11-27 10:51:28 UTC  
> Url: https://github.com/boostorg/fiber/issues/94  

Hi there,  Sorry for the noob question.  From my understanding calling yield from within a fiber will cause the fiber scheduler to give cycles to another fiber.  Now suppose I have a IO call that would block and during the block I would like to give other fibers the cycles.  Consider this:  
  
...  
do_nb_stuff(); // do non blocking stuff  
yield(); // <------if I put yield here won't it context switch before the do_b_stuff?  
do_b_stuff(); // do blocking work  
yield(); // <------if I put yield here won't it context switch after the do_b_stuff?  
  
What I want is to yield during do_b_stuff if that makes sense.  
  
Thanks and sorry for my stupidity or lack of understanding in advance.

---

## Comment 1

> Username: ishitatsuyuki  
> Created at: 2016-11-11 11:49:32 UTC  
> Url: https://github.com/boostorg/fiber/issues/94#issuecomment-259941496  

Use an async IO library, like Asio mentioned in the doc. If you are doing disk IO, there's only a few library that can do it, and many operations are cached and don't block.

---

## Comment 2

> Username: olk  
> Created at: 2016-11-27 10:51:28 UTC  
> Url: https://github.com/boostorg/fiber/issues/94#issuecomment-263115294  

do_b_stuff() must not block the entire thread - for instance a read operation on a socket, the socket must be non-blocking. the fiber can be suspended while waiting on bytes to be read from the socket. if bytes are available the fiber is resumed, reads bytes from the socket and does other stuff.  
what you need is an event dispatcher that resumes the suspended fiber when bytes arrived on the socket. boost.asio's io_service migth serve as a event-dispatcher (io_service uses select()/poll()/epol() ...).

---

## Comment 3

> Username: asharif  
> Created at: 2016-11-27 18:25:05 UTC  
> Url: https://github.com/boostorg/fiber/issues/94#issuecomment-263137943  

Thanks. I got it!
