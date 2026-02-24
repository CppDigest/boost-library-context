# #150 Correct logic about BOOST_NO_CTYPE_FUNCTIONS [Closed]

> Username: coypoop  
> Created at: 2017-05-29 16:55:57 UTC  
> Updated at: 2017-05-29 19:08:49 UTC  
> Closed at: 2017-05-29 19:08:45 UTC  
> Url: https://github.com/boostorg/config/pull/150  

Now it will no longer apply for netbsd.  
  
netbsd: https://nxr.netbsd.org/xref/src/include/ctype.h  
freebsd: https://grok.dragonflybsd.org/xref/freebsd/include/ctype.h  
dragonflybsd: https://grok.dragonflybsd.org/xref/dragonfly/include/ctype.h  
openbsd: https://grok.dragonflybsd.org/xref/openbsd/include/ctype.h

---

## Comment 1

> Username: jzmaddock  
> Created_at: 2017-05-29 17:26:05 UTC  
> Url: https://github.com/boostorg/config/pull/150#issuecomment-304704430  

Is it possible to version this change?  Clearly older BSD versions will still have the issue, but I don't know enough about how BSD is versioned...

---

## Comment 2

> Username: coypoop  
> Created_at: 2017-05-29 19:08:44 UTC  
> Url: https://github.com/boostorg/config/pull/150#issuecomment-304716924  

I suppose it doesn't actually matter, since both options seem to work, I thought it applies to more than just netbsd at first.  
sorry for the noise

---
