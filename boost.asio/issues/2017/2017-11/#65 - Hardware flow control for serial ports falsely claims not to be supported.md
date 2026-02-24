# #65 - Hardware flow control for serial ports falsely claims not to be supported. [Closed]

> Username: jpherbst  
> Created at: 2017-11-16 22:48:07 UTC  
> Updated at: 2020-12-30 00:44:13 UTC  
> Closed at: 2020-12-30 00:44:12 UTC  
> Url: https://github.com/boostorg/asio/issues/65  

I am using linux and whenever I try to enable hardware flow control on a serial port I get a boost::asio::error::operation_not_supported.  If I enable flow control manually using termios everything works fine.  
  
Some sifting through the source code has gotten me to the assumption that _BSD_SOURCE is not defined on my system which appears to gate the standard unix flow control setup in the asio code.  According to this website, http://man7.org/tlpi/code/faq.html#use_default_source, use of _BSD_SOURCE is depricated and maybe was removed in glibc, but I'm not really sure what the correct fix is.  
  
I am using Linux 4.9.48 with glibc 2.25.  Please let me know if more information is needed.

---

## Comment 1

> Username: mossheim  
> Created at: 2018-12-01 16:43:47 UTC  
> Url: https://github.com/boostorg/asio/issues/65#issuecomment-443439332  

I also have this issue on macOS (10.13.6). Not sure of the implementation version, but the check in `/usr/include/sys/termios.h` that enables the control flag definitions is this:  
  
```cpp  
#if !defined(_POSIX_C_SOURCE) || defined(_DARWIN_C_SOURCE)  
#define CCTS_OFLOW	0x00010000	/* CTS flow control of output */  
#define CRTSCTS		(CCTS_OFLOW | CRTS_IFLOW)  
#define CRTS_IFLOW	0x00020000	/* RTS flow control of input */  
#define	CDTR_IFLOW	0x00040000	/* DTR flow control of input */  
#define CDSR_OFLOW	0x00080000	/* DSR flow control of output */  
#define	CCAR_OFLOW	0x00100000	/* DCD flow control of output */  
#define	MDMBUF		0x00100000	/* old name for CCAR_OFLOW */  
#endif  
```  
  
Of course, this is not correct according to https://linux.die.net/man/3/termios, but it would be nice if I didn't have to circumvent this library to get basic functionality, so I hope you'll consider adding a check to enable this.

---

## Comment 2

> Username: jpherbst  
> Created at: 2018-12-06 22:15:16 UTC  
> Url: https://github.com/boostorg/asio/issues/65#issuecomment-445049527  

This seems to be fixed for my condition at least as early as Boost 1.66.

---

## Comment 3

> Username: mossheim  
> Created at: 2018-12-07 00:26:01 UTC  
> Url: https://github.com/boostorg/asio/issues/65#issuecomment-445079597  

Not fixed for me in 1.66.0

---

## Comment 4

> Username: ghost  
> Created at: 2020-12-30 00:44:11 UTC  
> Url: https://github.com/boostorg/asio/issues/65#issuecomment-752288177  

This issue was moved by [chriskohlhoff](https://github.com/chriskohlhoff) to [chriskohlhoff/asio#592](https://github.com/chriskohlhoff/asio/issues/592).
