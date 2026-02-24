# #105 Update VxWorks Support, add VxWorks serial support [Closed]

> Username: kuhlenough  
> Created at: 2018-05-01 13:01:44 UTC  
> Updated at: 2019-03-15 02:33:35 UTC  
> Closed at: 2019-03-15 02:33:01 UTC  
> Url: https://github.com/boostorg/asio/pull/105  

This request updates VxWorks ASIO support.   
- missing ifdefs for using select() instead of poll()  
- VxWorks serial port support using the equivalent ioctl()s to the termios support VxWorks does not have

---

## Review 1 [Commented]

> Username: kuhlenough  
> Created_at: 2018-05-01 13:06:37 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/asio/pull/105#pullrequestreview-116560373  

📁 include/boost/asio/detail/socket_types.hpp

```diff
  65 |+ # elif !defined(__SYMBIAN32__) && !defined(__VXWORKS__ )
  66 |+ #  include <sys/poll.h>
  67 |+ # elif defined(__VXWORKS__ ) && (_WRS_VXWORKS_MAJOR > 6 )
```

> Username: kuhlenough  
> Created_at: 2018-05-01 13:06:36 UTC  
> Updated_at: 2018-05-01 13:07:00 UTC  
> Url: https://github.com/boostorg/asio/pull/105#discussion_r185216926  

We added an optional fake poll() in VxWorks 7 that just wraps select(), for instances like ASIO streams where there is no select() alternative.


---

## Review 2 [Commented]

> Username: kuhlenough  
> Created_at: 2018-05-01 13:10:23 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/asio/pull/105#pullrequestreview-116561106  

📁 include/boost/asio/detail/config.hpp

```diff
1104 |-   && !defined(__CYGWIN__)
1104 |+   && !defined(__CYGWIN__) \
1105 |+   && (defined(BOOST_ASIO_HAS_DEV_POLL) || defined(BOOST_ASIO_HAS_DEV_EPOLL))
```

> Username: kuhlenough  
> Created_at: 2018-05-01 13:10:22 UTC  
> Updated_at: 2018-05-01 13:10:23 UTC  
> Url: https://github.com/boostorg/asio/pull/105#discussion_r185217499  

A poll() implementation is required to use stream descriptors, no alternative select() block is provided


---

## Review 3 [Commented]

> Username: kuhlenough  
> Created_at: 2018-05-01 13:12:49 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/asio/pull/105#pullrequestreview-116561579  

📁 include/boost/asio/detail/impl/reactive_serial_port_service.ipp

```diff
  23 |+ 
  24 |+ #ifdef __VXWORKS__
  25 |+ #include <ioLib.h>
```

> Username: kuhlenough  
> Created_at: 2018-05-01 13:12:48 UTC  
> Updated_at: 2018-05-01 13:12:49 UTC  
> Url: https://github.com/boostorg/asio/pull/105#discussion_r185217911  

ioLib. h provides some definitions  that a full POSIX conforming implementation  would provide in stdio.h


---

## Review 4 [Commented]

> Username: kuhlenough  
> Created_at: 2018-05-01 13:22:39 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/asio/pull/105#pullrequestreview-116563619  

📁 include/boost/asio/serial_port_base.hpp

```diff
  36 | # define BOOST_ASIO_OPTION_STORAGE termios
  37 |+ #ifdef __VXWORKS__
  38 |+ struct termios
```

> Username: kuhlenough  
> Created_at: 2018-05-01 13:22:39 UTC  
> Url: https://github.com/boostorg/asio/pull/105#discussion_r185219662  

VxWorks has no termios.h or struct termios, so we construct one here.  Three ioctl() calls provide the equivalent functionality. c_cflags usage follows BSD, BaudRate is handled in a format equivalent to Windows. Flow control is stored separately in the options field of the TTY device.


---

## Comment 5

> Username: kuhlenough  
> Created_at: 2019-03-15 02:33:35 UTC  
> Url: https://github.com/boostorg/asio/pull/105#issuecomment-473136491  

Obsoleted by new cleaner pull

---
