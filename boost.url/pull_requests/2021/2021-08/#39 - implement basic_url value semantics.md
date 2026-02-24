# #39 implement basic_url value semantics [Closed]

> Username: AlexandreBossard  
> Created at: 2021-08-29 19:35:11 UTC  
> Updated at: 2021-08-30 22:03:52 UTC  
> Closed at: 2021-08-30 21:51:37 UTC  
> Url: https://github.com/boostorg/url/pull/39  

This pull request proposes an implementation of copy/assignment/swap operators for basic_url.

---

## Comment 1

> Username: vinniefalco  
> Created_at: 2021-08-30 21:41:41 UTC  
> Url: https://github.com/boostorg/url/pull/39#issuecomment-908720223  

This project is still being developed and not really ready for any contributions. If you will note, I have pushed a new implementation which uses `json::storage_ptr` which is preferred over _Allocator_.

---

## Comment 2

> Username: AlexandreBossard  
> Created_at: 2021-08-30 21:51:36 UTC  
> Url: https://github.com/boostorg/url/pull/39#issuecomment-908726208  

I did note that :D. As it is, this library is more useful and suitable to my use case than anything else I found. The only thing is missing to me, is value semantics as per #36.  
  
I'll close this for know and rebase and re-submit a new PR when I've got time.

---

## Comment 3

> Username: vinniefalco  
> Created_at: 2021-08-30 21:52:20 UTC  
> Url: https://github.com/boostorg/url/pull/39#issuecomment-908726603  

What makes it more useful? What specifically, so I know to make that part great

---

## Comment 4

> Username: AlexandreBossard  
> Created_at: 2021-08-30 22:03:52 UTC  
> Url: https://github.com/boostorg/url/pull/39#issuecomment-908733315  

I've used skyr-url until know, It has a lot of dependencies (like lt::expected, rangeV3, nlohmann::json and std::filesystem) that I don't need / are cumbersome. boost-url is just... boost that I already use for asio and beast. So boost-url is just smaller and easier to integrate into my project already.  
  
Then I just need to extract the host (and port) from an url encoded string and the "target" (everything right of the first /) of the url. Not much more I would say.

---
