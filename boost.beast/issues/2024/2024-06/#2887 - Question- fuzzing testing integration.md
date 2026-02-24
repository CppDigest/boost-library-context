# #2887 - Question: fuzzing testing integration [Closed]

> Username: tyler92  
> Created at: 2024-06-17 21:47:34 UTC  
> Updated at: 2024-06-22 17:28:54 UTC  
> Closed at: 2024-06-22 17:28:53 UTC  
> Url: https://github.com/boostorg/beast/issues/2887  

Hi,  
  
I recently created a PR for Boost.Beast fuzzing in the [Google OSS-Fuzz repository](https://github.com/google/oss-fuzz/pull/11994). This PR includes three fuzzing targets: the HTTP request parser, the HTTP response parser, and the WebSocket server. I find these targets quite useful, e.g. they found https://github.com/boostorg/beast/pull/2881 and https://github.com/boostorg/beast/pull/2861 (reported not by me) using AddressSanitizer.  
  
As far as I understood, it’s better to have fuzzing targets in the upstream repo rather than just in OSS-Fuzz. It keeps everything in one place and makes it easier to keep the fuzzing targets up to date with any changes.  
  
Would you be open to integrating these fuzzing targets into the Beast repo? I’m happy to help with the initial setup. This would mean:  
  
- Adding the `*.cc` files for the fuzzing targets  
- Including a "corpus" of sample input data for the fuzzers  
- Optionally setting up an automatic CI job to run the fuzzers on each PR  
  
Let me know what you think.

---

## Comment 1

> Username: ashtum  
> Created at: 2024-06-18 15:46:26 UTC  
> Url: https://github.com/boostorg/beast/issues/2887#issuecomment-2176424931  

It would be great if we could add Boost.Beast to OSS-Fuzz. As you mentioned, https://github.com/boostorg/beast/pull/2881 and https://github.com/boostorg/beast/pull/2861 showed there is always a chance of finding new bugs. You can use Boost.URL as a reference for where to add the required files: https://github.com/boostorg/url/tree/develop/test/fuzz  
  
> Optionally setting up an automatic CI job to run the fuzzers on each PR  
  
Currently, we have an overloaded GitHub Action CI. However, I believe we can add that to Drone. I'll take care of this part.

---

## Comment 2

> Username: tyler92  
> Created at: 2024-06-18 17:24:04 UTC  
> Url: https://github.com/boostorg/beast/issues/2887#issuecomment-2176611320  

Great! I will open a PR and I hope we will do it step by step.

---

## Comment 3

> Username: ashtum  
> Created at: 2024-06-22 17:28:53 UTC  
> Url: https://github.com/boostorg/beast/issues/2887#issuecomment-2184117609  

Addressed in https://github.com/boostorg/beast/pull/2888 and https://github.com/boostorg/beast/pull/2891.
