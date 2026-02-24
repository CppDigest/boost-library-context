# #16 Fixed issue #5237. [Closed]

> Username: DenizThatMenace  
> Created at: 2015-08-05 15:20:10 UTC  
> Updated at: 2016-12-06 19:33:01 UTC  
> Closed at: 2016-12-06 19:33:01 UTC  
> Url: https://github.com/boostorg/iostreams/pull/16  

Ticket [#5273](https://svn.boost.org/trac/boost/ticket/5237) in Boost's Trac issue-system describes the following error:  
  
Writing gzipped files with no content results in corrupted files being created which are no valid gzip-files.  
  
This pull-request solves this problem by applying the (very simple) patch described in [this comment](https://svn.boost.org/trac/boost/ticket/5237#comment:4) to the ticket.

---

## Comment 1

> Username: mclow  
> Created_at: 2015-08-05 15:57:06 UTC  
> Url: https://github.com/boostorg/iostreams/pull/16#issuecomment-128048838  

This looks good to me.

---

## Comment 2

> Username: eldiener  
> Created_at: 2016-12-04 00:11:23 UTC  
> Url: https://github.com/boostorg/iostreams/pull/16#issuecomment-264674681  

I believe this has already been fixed on 'develop'.

---
