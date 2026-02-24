# #673 - Reduce dependencies [Closed]

> Username: vinniefalco  
> Created at: 2017-07-24 00:35:22 UTC  
> Updated at: 2017-07-27 19:18:15 UTC  
> Closed at: 2017-07-27 19:18:15 UTC  
> Url: https://github.com/boostorg/beast/issues/673  

Some dependencies are brought in just for tests or examples and they are large but Beast only uses a little of it, `program_options` for example. These should be removed.

---

## Comment 1

> Username: akoolenbourke  
> Created at: 2017-07-24 04:11:39 UTC  
> Updated at: 2017-07-24 04:12:45 UTC  
> Url: https://github.com/boostorg/beast/issues/673#issuecomment-317314971  

Relating to this, Vinnie have you contemplated developing a non Boost version of Beast side by side with the Boost one, as ASIO does? I'm being selfish here as we've decided to go with standalone ASIO (Networking TS version) which looks like it will cut Beast out of the running for us.  
  
I don't know how long Beast is planned to be in the incubator for but if it's a couple of years we're likely going to get TS+ implementations appearing and being able to use Beast with that would be very handy for people.  
  
Cheers

---

## Comment 2

> Username: vinniefalco  
> Created at: 2017-07-24 05:19:51 UTC  
> Updated at: 2017-07-24 05:25:32 UTC  
> Url: https://github.com/boostorg/beast/issues/673#issuecomment-317321932  

1. As of July 20th, Beast has been ACCEPTED into Boost without conditions  
2. There are no plans for Beast to support stand-alone Asio  
3. Boost.Asio will be updated this year to the Networking-TS specification  
4. Beast will be released in Boost 1.66.0 (December) to use the updated Boost.Asio  
  
Net-TS is modeled closely after Asio, there isn't a tremendous amount of difference. I would just use Boost.Asio in 1.65.0 and when 1.66.0 is released with the Net-TS changes just make the changes as needed in your program. They are really quite similar.

---

## Comment 3

> Username: akoolenbourke  
> Created at: 2017-07-24 06:24:29 UTC  
> Url: https://github.com/boostorg/beast/issues/673#issuecomment-317330223  

> As of July 20th, Beast has been ACCEPTED into Boost without conditions  
  
Well congratulations, that's great news!  
  
> There are no plans for Beast to support stand-alone Asio  
  
OK, thanks.  
  
> Boost.Asio will be updated this year to the Networking-TS specification  
  
Sigh! I asked Chris the other day about which version of ASIO to use and no mention of a TS Boost.Asio was made; so we chose standalone TS ASIO.  We're obviously very concerned about investing in some technology that we are going to expect to last us for many years to come, so we want to try and make informed decisions on which implementation we go with. It's a shame that things are in flux and we're coming in at a time where Beast is here and there's a networking TS.  A couple of years ago it would have been Boost.Asio without question.  
  
> Beast will be released in Boost 1.66.0 (December) to use the updated Boost.Asio  
  
OK, thanks for the date. From my investigation into Beast, I very much like it and think it could be a worthwhile addition to our software but I need to convince other big stakeholders if we're to embrace it. It being in incubator and TS Asio not being in Boost (Or so I thought) meant we ended up choosing standalone TS ASIO and abandoning Beast. I'll have to open discussion again with this new news. It may be that we look at contributing to the project too as we have a lot of HTTP/2 pipeline stuff to develop.  
  
  
> Net-TS is modeled closely after Asio, there isn't a tremendous amount of difference. I would just use Boost.Asio in 1.65.0 and when 1.66.0 is released with the Net-TS changes just make the changes as needed in your program. They are really quite similar.  
  
Yeah I realise Networking TS is basically Asio but I was under the impression that there were some significant changes with executors and buffers (Dynamic and more in TS) to name a couple.  
  
Thanks for the reply, like I said I'll have to discuss this again with some people at work and see where we end up.

---

## Comment 4

> Username: vinniefalco  
> Created at: 2017-07-24 06:31:53 UTC  
> Updated at: 2017-07-24 06:36:15 UTC  
> Url: https://github.com/boostorg/beast/issues/673#issuecomment-317331275  

> I was under the impression that there were some significant changes with executors and buffers (Dynamic and more in TS) to name a couple.  
  
Executors are an area of difference but again I think its relatively minor. Updating a program to be executor-aware and use the universal asynchronous model (return value hooks) is largely a mechanical process and doesn't require significant changes in logic. Most Asio programs will require minimal changes, its only when you start writing your own composed operations or going deep such as writing your own io_service services that you are going to have code exposed to larger API changes. And if you're writing such things, the work of converting to Net-TS is trivial compared to the significantly greater amount of work and skill required for such implementations.  
  
As for the DynamicBuffer well, just my opinion but I think Beast has the Net-TS reference implementation beat :) Not only does Beast support the DynamicBuffer concept but it comes with a tremendous collection of dynamic buffers and buffer adapters. See:  
http://vinniefalco.github.io/beast/beast/using_i_o/buffer_types.html  
  
Beast's dynamic buffers also out-perform the Net-TS equivalent:  
https://travis-ci.org/vinniefalco/beast/jobs/256358942#L1431  
  
To be fair, the Asio and Net-TS dynamic buffers are designed to be general purpose and not address particular niches (like Beast's `static_buffer` which is circular or `flat_static_buffer`).  
  
We're only talking a time period between now and December, how much code do you think you could write between now and then, especially advanced composed operations and implementation that would require the most work to port over to Net-TS? I doubt it would take more than a few days to update.

---

## Comment 5

> Username: vinniefalco  
> Created at: 2017-07-27 19:18:15 UTC  
> Url: https://github.com/boostorg/beast/issues/673#issuecomment-318460349  

I've reviewed the changes necessary to port Beast to Net-TS. They are minor and mechanical (no change in algorithms). And Beast is a heavy consumer of Asio so most users' porting experience will be easier. I'm going to go ahead and close this for now since there's nothing actionable in it but feel free to re-open it or open a new issue if you have questions or comments, thanks!
