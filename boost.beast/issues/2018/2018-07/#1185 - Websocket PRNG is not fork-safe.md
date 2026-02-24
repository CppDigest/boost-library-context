# #1185 - Websocket PRNG is not fork-safe [Open]

> Username: djarek  
> Created at: 2018-07-11 18:30:35 UTC  
> Updated at: 2022-09-24 06:12:26 UTC  
> Url: https://github.com/boostorg/beast/issues/1185  

Websocket secure PRNG is not reseeded after fork, which may result in reuse of (Key, Nonce) pairs in the ChaCha20 function.

---

## Comment 1

> Username: vinniefalco  
> Created at: 2018-07-11 18:32:17 UTC  
> Url: https://github.com/boostorg/beast/issues/1185#issuecomment-404267968  

Handling a fork is out of scope for Beast. That said, the caller can reseed the generator themselves by calling `websocket::prng_seed`.

---

## Comment 2

> Username: stale[bot]  
> Created at: 2018-08-10 19:28:35 UTC  
> Url: https://github.com/boostorg/beast/issues/1185#issuecomment-412182340  

This issue has been open for a while with no activity, has it been resolved?

---

## Comment 3

> Username: vinniefalco  
> Created at: 2018-08-10 19:35:24 UTC  
> Updated at: 2018-08-10 20:48:26 UTC  
> Url: https://github.com/boostorg/beast/issues/1185#issuecomment-412183946  

I suppose we could add a `websocket_stream_service`, but how should it handle a fork?

---

## Comment 4

> Username: djarek  
> Created at: 2018-08-10 22:41:56 UTC  
> Updated at: 2018-08-10 22:42:09 UTC  
> Url: https://github.com/boostorg/beast/issues/1185#issuecomment-412224853  

After forking the PRNG has to be reseeded.

---

## Comment 5

> Username: vinniefalco  
> Created at: 2018-09-10 14:40:40 UTC  
> Url: https://github.com/boostorg/beast/issues/1185#issuecomment-419937655  

There is no guaranteed secure way for Beast to reseed the generator, that is why we provide the reseed function so that users can get the necessary entropy in the best way for their platform. So on a fork, the user would have to manually reseed anyway. I'm not sure this can be fixed.

---

## Comment 6

> Username: vinniefalco  
> Created at: 2018-09-10 14:41:03 UTC  
> Url: https://github.com/boostorg/beast/issues/1185#issuecomment-419937789  

At a minimum this should be documented in a comment in the source file, maybe also in the docs.

---

## Comment 7

> Username: stale[bot]  
> Created at: 2018-11-09 15:31:14 UTC  
> Url: https://github.com/boostorg/beast/issues/1185#issuecomment-437395105  

This issue has been open for a while with no activity, has it been resolved?

---

## Comment 8

> Username: vinniefalco  
> Created at: 2018-11-09 15:45:22 UTC  
> Url: https://github.com/boostorg/beast/issues/1185#issuecomment-437399751  

I suppose we could give the user a way to provide a secure source of entropy, and if they fork without providing such a source then an exception is thrown.

---

## Comment 9

> Username: vinniefalco  
> Created at: 2022-06-16 16:26:35 UTC  
> Url: https://github.com/boostorg/beast/issues/1185#issuecomment-1157876721  

We should at least document it

---

## Comment 10

> Username: sehe  
> Created at: 2022-06-16 20:58:15 UTC  
> Url: https://github.com/boostorg/beast/issues/1185#issuecomment-1158122745  

I could add this to my stack. Right away I don't understand how we can securely seed the PRNG for the main process, yet don't have a secure source of entropy to re-seed after a fork? Would not both require the same source of entropy?

---

## Comment 11

> Username: vinniefalco  
> Created at: 2022-06-16 21:09:16 UTC  
> Url: https://github.com/boostorg/beast/issues/1185#issuecomment-1158132872  

Beast does not securely seed the PRNG. If the user wants a secure seeding, they have to do it themselves. That's because getting high quality entropy is highly platform dependent. OpenSSL has a way I believe, but that requires OpenSSL. So it is completely up to the user.

---

## Comment 12

> Username: alandefreitas  
> Created at: 2022-06-18 21:15:47 UTC  
> Url: https://github.com/boostorg/beast/issues/1185#issuecomment-1159565054  

Which of the options are still on the table?  
  
- Only better documentation for `websocket` and `websocket::prng_seed`  
- Some form of `websocket_stream_service`  
- Reseed poorly anyway  
- Throwing an exception  
- Some API to provide a secure source of entropy

---

## Comment 13

> Username: vinniefalco  
> Created at: 2022-06-19 14:52:19 UTC  
> Updated at: 2022-06-19 14:52:29 UTC  
> Url: https://github.com/boostorg/beast/issues/1185#issuecomment-1159747417  

A sentence or two in the documentation is more than sufficient. Note the issue label.

---

## Comment 14

> Username: klemens-morgenstern  
> Created at: 2022-09-24 06:12:26 UTC  
> Url: https://github.com/boostorg/beast/issues/1185#issuecomment-1256877118  

Are you sure this shouldn't be handled through asio's notifh_fork?
