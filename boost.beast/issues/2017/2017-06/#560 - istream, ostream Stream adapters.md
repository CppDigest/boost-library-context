# #560 - istream, ostream Stream adapters [Closed]

> Username: vinniefalco  
> Created at: 2017-06-30 02:50:52 UTC  
> Updated at: 2022-10-12 01:51:30 UTC  
> Closed at: 2022-10-12 01:51:30 UTC  
> Url: https://github.com/boostorg/beast/issues/560  

The documentation suggests that serializing and parsing between ostreams and istreams could be done with stream wrappers, the examples should include both of these and they should be added to the docs.

---

## Comment 1

> Username: klemens-morgenstern  
> Created at: 2022-09-24 06:30:08 UTC  
> Url: https://github.com/boostorg/beast/issues/560#issuecomment-1256879677  

Would this be an io_stream_body? If so example or code?

---

## Comment 2

> Username: vinniefalco  
> Created at: 2022-09-25 00:22:06 UTC  
> Url: https://github.com/boostorg/beast/issues/560#issuecomment-1257089255  

I don't think this is necessary and no it would not be an io_stream_body, this would be an asio SyncReadStream / SyncWriteStream (Which might even already exist).

---

## Comment 3

> Username: klemens-morgenstern  
> Created at: 2022-10-12 01:51:30 UTC  
> Url: https://github.com/boostorg/beast/issues/560#issuecomment-1275481401  

This is sadly not trivial and it's also just useful for a hello world example, so I agree it's not necessary.
