# #838 - Feature request: SHA256 for GitHub release downloads [Closed]

> Username: ecorm  
> Created at: 2023-12-18 22:40:52 UTC  
> Updated at: 2025-09-04 09:39:54 UTC  
> Closed at: 2025-09-04 09:39:54 UTC  
> Url: https://github.com/boostorg/boost/issues/838  

I'm using CMake's FetchContent to download the latest Boost release as part of my project's CMake build process. It's possible to pass an SHA256 hash to FetchContent so that it can verify the integrity of the download.  
  
However, if I look at the assets listed in the Boost GitHub [release page](https://github.com/boostorg/boost/releases/tag/boost-1.84.0), there aren't any files there containing meta-information like SHA256 hashes.  
  
I tried using the SHA256 hash available [here](https://boostorg.jfrog.io/artifactory/main/release/1.84.0/source/boost_1_84_0.tar.gz.json) at jfrog.io. CMake complains of SHA256 mismatch when I try using that hash:  
  
```  
verifying file...  
[cmake]        file='/home/build/_deps/fetchboost-subbuild/fetchboost-populate-prefix/src/boost-1.84.0.tar.gz'  
[cmake] -- SHA256 hash of  
[cmake]     /home/build/_deps/fetchboost-subbuild/fetchboost-populate-prefix/src/boost-1.84.0.tar.gz  
[cmake]   does not match expected value  
[cmake]     expected: 'a5800f405508f5df8114558ca9855d2640a2de8f0445f051fa1c7c3383045724'  
[cmake]       actual: '4d27e9efed0f6f152dc28db6430b9d3dfb40c0345da7342eaa5a987dde57bd95'  
```  
Of course, now I know that the hash is supposed to be `4d27e9efed0f6f152dc28db6430b9d3dfb40c0345da7342eaa5a987dde57bd95`, but it would be better if that were published somewhere in an official manner.  
  
- - -  
  
To clarify, it's the GitHub release downloads that lack a published SHA256.

---

## Comment 1

> Username: amadou-6e  
> Created at: 2023-12-31 16:11:44 UTC  
> Url: https://github.com/boostorg/boost/issues/838#issuecomment-1872985284  

Same problem for me.

---

## Comment 2

> Username: Lastique  
> Created at: 2024-04-16 00:27:06 UTC  
> Url: https://github.com/boostorg/boost/issues/838#issuecomment-2058028279  

SHA256 checksums for the official archives are published in [release notes](https://www.boost.org/users/history/version_1_85_0.html).

---

## Comment 3

> Username: ecorm  
> Created at: 2024-04-16 00:34:21 UTC  
> Url: https://github.com/boostorg/boost/issues/838#issuecomment-2058034216  

> SHA256 checksums for the official archives are published in [release notes](https://www.boost.org/users/history/version_1_85_0.html).  
  
I don't remember exactly, but I think that SHA256 only works with the jfrog download link, and not the GitHub release tarball.

---

## Comment 4

> Username: Lastique  
> Created at: 2024-04-16 00:45:21 UTC  
> Url: https://github.com/boostorg/boost/issues/838#issuecomment-2058042457  

The checksums in the release notes only cover the archives on jfrog. The archives on GitHub are different and there are no published checksums for those.

---

## Comment 5

> Username: ecorm  
> Created at: 2024-04-16 00:49:07 UTC  
> Url: https://github.com/boostorg/boost/issues/838#issuecomment-2058045227  

> The archives on GitHub are different and there are no published checksums for those.  
  
That's why I raised this issue. I should have made that clearer in my description.

---

## Comment 6

> Username: mclow  
> Created at: 2024-05-23 23:05:41 UTC  
> Url: https://github.com/boostorg/boost/issues/838#issuecomment-2128171697  

The archives on GitHub are not official releases.

---

## Comment 7

> Username: ecorm  
> Created at: 2024-05-23 23:11:16 UTC  
> Url: https://github.com/boostorg/boost/issues/838#issuecomment-2128176511  

> The archives on GitHub are not official releases.  
  
Not official, no, but I get a feeling that they're more reliable as a download source than the hosting provider that seems to change every few years. First it was `boost.org`, then something about a frog, now `boost.io`. The download provider changes keep breaking my CMake scripts.

---

## Comment 8

> Username: pdimov  
> Created at: 2024-05-23 23:13:03 UTC  
> Url: https://github.com/boostorg/boost/issues/838#issuecomment-2128177992  

I should probably do something about having SHA256 hashes for these archives, but I'm not sure what.

---

## Comment 9

> Username: mclow  
> Created at: 2024-05-23 23:19:11 UTC  
> Url: https://github.com/boostorg/boost/issues/838#issuecomment-2128182803  

> > The archives on GitHub are not official releases.  
>   
> Not official, no, but I get a feeling that they're more reliable as a download source than the hosting provider that seems to change every few years. First it was `boost.org`, then something about a frog, now `boost.io`. The download provider changes keep breaking my CMake scripts.  
  
If they work for you, then you should keep using them.  
But do so with the knowledge that they are *different* from the official releases, are neither tested nor supported.

---

## Comment 10

> Username: ecorm  
> Created at: 2024-05-23 23:22:57 UTC  
> Url: https://github.com/boostorg/boost/issues/838#issuecomment-2128185445  

> If they work for you, then you should keep using them.  
But do so with the knowledge that they are different from the official releases, are neither tested nor supported.  
  
I will keep using them, and the lack of a "official" SHA256 hash for the GitHub version is an inconvenience, but not a showstopper.  
  
I'll edit the title of this issue to make it clearer that it's a feature request.

---

## Comment 11

> Username: Lastique  
> Created at: 2024-05-23 23:25:58 UTC  
> Url: https://github.com/boostorg/boost/issues/838#issuecomment-2128187656  

> I should probably do something about having SHA256 hashes for these archives, but I'm not sure what.  
  
Usually, projects publish .sig files along with tarballs. This is secure because the public key to verify the signature is published elsewhere - e.g. on www.boost.org.  
  
If you publish a checksum on GitHub, whether in a separate file or in the release description, it cannot be used as a method of ensuring authenticity of the tarballs, because an attacker with access to the GitHub account could forge the checksum along with the tarball. The only solution in this case would be to publish the checksums on www.boost.org as well. But that would be difficult to do - you'd probably have to create a PR to the website from the release CI, and then someone has to merge it, too.

---

## Comment 12

> Username: pdimov  
> Created at: 2024-05-24 00:05:54 UTC  
> Url: https://github.com/boostorg/boost/issues/838#issuecomment-2128255772  

An attacker with access to the Github account would be able to forge the signatures as well. He would just rerun the Github action that creates the release artifacts.  
  
Signatures would only provide security if someone creates them locally and uploads them separately.

---

## Comment 13

> Username: Lastique  
> Created at: 2024-05-24 00:07:45 UTC  
> Url: https://github.com/boostorg/boost/issues/838#issuecomment-2128257191  

The attacker would not be able to forge the signatures because he would not have the private key to sign the tarballs.

---

## Comment 14

> Username: Lastique  
> Created at: 2024-05-24 00:13:49 UTC  
> Url: https://github.com/boostorg/boost/issues/838#issuecomment-2128262095  

Although, yes, you are correct he could re-run the CI. So if the CI has access to the private key then the key can be considered compromised.

---

## Comment 15

> Username: pdimov  
> Created at: 2024-05-24 00:19:30 UTC  
> Url: https://github.com/boostorg/boost/issues/838#issuecomment-2128267004  

We can in principle have a private key as a Github secret etc, but that's smoke and mirrors. Better not pretend we have secure signatures if we don't.  
  
Anyway, I added a few `sha256sum` calls to release-cmake.yml, so the next release should have associated .txt files.  
  
https://github.com/boostorg/boost/commit/2ff30383d7e8056e5facb5d8020a1ed0a90d10e5

---

## Comment 16

> Username: nigels-com  
> Created at: 2025-09-04 06:00:56 UTC  
> Url: https://github.com/boostorg/boost/issues/838#issuecomment-3252040284  

Anything further needed for this?  
Time to close the ticket?
