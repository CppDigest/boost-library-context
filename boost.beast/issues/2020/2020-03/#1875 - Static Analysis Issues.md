# #1875 - Static Analysis Issues [Closed]

> Username: erikzenkerLogmein  
> Created at: 2020-03-13 14:23:46 UTC  
> Updated at: 2020-05-22 08:24:51 UTC  
> Closed at: 2020-05-22 08:24:50 UTC  
> Url: https://github.com/boostorg/beast/issues/1875  

Boost: 1.71.0_1574063579  
Beast: 266  
  
Hi @vinniefalco thank you for the great work!  
  
We are using the static analysis tool coverity which reports us the following issues:  
  
- In `websocket/impl/write.hpp`  
```  
198            net::async_write(impl.stream(),  
199                buffers_cat(impl.wr_fb.data(), cb_),  
200                    beast::detail::bind_continuation(std::move(*this)));  
201            bytes_transferred_ += clamp(fh_.len);  
   	  
CID 135675 (#3-4 of 4): Identical code for different branches (IDENTICAL_BRANCHES)  
identical_branches: The same code is executed when the condition impl->check_stop_now(ec) is true or false, because the code in the if-then branch and after the if statement is identical. Should the if statement be removed?  
202            if(impl.check_stop_now(ec))  
203                goto upcall;  
   	implicit_else: The code from the above if-then branch is identical to the code after the if statement.  
204            goto upcall;  
205        }  
```  
  
- in `websocket/impl/write.hpp`  
```  
263            impl.wr_fb.clear();  
264            detail::write<flat_static_buffer_base>(  
265                impl.wr_fb, fh_);  
266            n = clamp(remain_, impl.wr_buf_size);  
   	  
CID 135672 (#11-12 of 12): Unchecked return value from library (CHECKED_RETURN)  
9. check_return: Calling boost::asio::buffer_copy(boost::asio::mutable_buffers_1 const(boost::asio::buffer(impl.wr_buf.get(), n)), this->cb_) without checking return value. This library function may fail and return an error code. [Note: The source code implementation of the function has been overridden by a builtin model.]  
267            net::buffer_copy(net::buffer(  
268                impl.wr_buf.get(), n), cb_);  
269            detail::mask_inplace(net::buffer(  
```  
  
- in `websocket/impl/read.hpp`  
```  
602        upcall:  
603            impl.rd_block.try_unlock(this);  
   	  
CID 135670 (#5-8 of 8): Unchecked return value (CHECKED_RETURN)  
24. check_return: Calling maybe_invoke without checking return value (as is done elsewhere 10 out of 12 times).  
604            impl.op_r_close.maybe_invoke();  
605            if(impl.wr_block.try_unlock(this))  
606                impl.op_close.maybe_invoke()  
607                    || impl.op_idle_ping.maybe_invoke()  
608                    || impl.op_ping.maybe_invoke()  
609                    || impl.op_wr.maybe_invoke();  
610            this->complete(cont, ec, bytes_written_);  
```  
- in `core/impl/basic_stream.hpp`  
```  
642template<class Protocol, class Executor, class RatePolicy>  
643basic_stream<Protocol, Executor, RatePolicy>::  
   	  
CID 131433 (#2 of 2): Uncaught exception (UNCAUGHT_EXCEPT)  
exn_spec_violation: An exception of type boost::wrapexcept<boost::system::system_error> is thrown but the throw list noexcept doesn't allow it to be thrown. This will cause a call to unexpected() which usually calls terminate().  
644~basic_stream()  
645{  
646    // the shared object can outlive *this,  
647    // cancel any operations so the shared  
648    // object is destroyed as soon as possible.  
   	fun_call_w_exception: Called function throws an exception of type boost::wrapexcept<boost::system::system_error>. [show details]  
649    impl_->close();  
650}  
```

---

## Comment 1

> Username: madmongo1  
> Created at: 2020-03-13 16:22:11 UTC  
> Url: https://github.com/boostorg/beast/issues/1875#issuecomment-598804436  

Hi @erikzenkerLogmein ,  
  
Many thanks for reporting this.  
  
Are you able to give me the command line use used to create this output? It would make it much easier for me to test against an MCVE.

---

## Comment 2

> Username: vinniefalco  
> Created at: 2020-03-13 16:32:56 UTC  
> Url: https://github.com/boostorg/beast/issues/1875#issuecomment-598809233  

Obviously you would need a Coverity license, and the program installed... I think the author gave sufficient information for this issue to be actionable (or not actionable, as is more likely the case).

---

## Comment 3

> Username: madmongo1  
> Created at: 2020-03-13 17:18:12 UTC  
> Url: https://github.com/boostorg/beast/issues/1875#issuecomment-598828953  

Yeah, they just quoted me $12,000 for a 5-pack. No discount for open source maintainers. It's a bit above my budget :-)

---

## Comment 4

> Username: vinniefalco  
> Created at: 2020-03-13 17:30:20 UTC  
> Url: https://github.com/boostorg/beast/issues/1875#issuecomment-598833982  

The last one, `impl_->close();` is already fixed in _master_, and the others look like false positives.

---

## Comment 5

> Username: stale[bot]  
> Created at: 2020-04-12 17:46:31 UTC  
> Url: https://github.com/boostorg/beast/issues/1875#issuecomment-612651864  

This issue has been open for a while with no activity, has it been resolved?

---

## Comment 6

> Username: erikzenker  
> Created at: 2020-04-15 07:54:29 UTC  
> Url: https://github.com/boostorg/beast/issues/1875#issuecomment-613879166  

Thx @madmongo1, for fixing #1876!  
@vinniefalco so I guess unchecked return value is intentional.  
   
Thus, we will update to the upcoming boost version and mark the other issues as intentional.

---

## Comment 7

> Username: stale[bot]  
> Created at: 2020-05-15 08:11:55 UTC  
> Url: https://github.com/boostorg/beast/issues/1875#issuecomment-629097209  

This issue has been open for a while with no activity, has it been resolved?

---

## Comment 8

> Username: stale[bot]  
> Created at: 2020-05-22 08:24:50 UTC  
> Url: https://github.com/boostorg/beast/issues/1875#issuecomment-632566913  

It looks like this issue has either been abandoned or resolved so I will go ahead and close it. Feel free to re-open this issue if you feel it needs attention, or open new issues as needed. Thanks!
