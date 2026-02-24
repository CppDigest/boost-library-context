# #82 ci: move Ubuntu 20.04 builds to container [Merged]

> Username: striezel  
> Created at: 2025-06-01 01:33:16 UTC  
> Updated at: 2025-06-02 06:39:58 UTC  
> Merged at: 2025-06-02 02:52:50 UTC  
> Closed at: 2025-06-02 02:52:50 UTC  
> Url: https://github.com/boostorg/leaf/pull/82  

The Ubuntu 20.04 image on GitHub Actions has been unavailable since 2025-04-15. See <https://github.com/actions/runner-images/issues/11101> for more information on the deprecation and removal.  
  
Therefore all build jobs that use the Ubuntu 20.04 runner image of GHA have to be moved to Docker containers using Ubuntu 20.04.

---

## Comment 1

> Username: striezel  
> Created_at: 2025-06-01 05:44:21 UTC  
> Url: https://github.com/boostorg/leaf/pull/82#issuecomment-2926583560  

Due to some failures in the first run (see https://github.com/boostorg/leaf/actions/runs/15369556162/job/43248897045 for example) I had to adjust the workflow for Clang 10/11/12 again to make sure the appropriate `clang-10`/`clang-11`/`clang-12` packages get installed. They were pre-installed on the `ubuntu-20.04` runner image, so that was not necessary there. But in the `ubuntu:20.04` container they have to be installed explicitly.  
  
However, there are some sporadic failures to fetch Ubuntu packages, for example in https://github.com/boostorg/leaf/actions/runs/15371382483/job/43251007308:  
```  
Get:106 http://archive.ubuntu.com/ubuntu focal-updates/main amd64 libsasl2-modules amd64 2.1.27+dfsg-2ubuntu0.1 [48.8 kB]  
Get:107 http://archive.ubuntu.com/ubuntu focal/main amd64 manpages-dev all 5.05-1 [2266 kB]  
Get:108 http://archive.ubuntu.com/ubuntu focal/main amd64 patch amd64 2.7.6-6 [105 kB]  
E: Failed to fetch http://archive.ubuntu.com/ubuntu/pool/main/r/rtmpdump/librtmp1_2.4+20151223.gitfa8646d.1-2build1_amd64.deb  403  Forbidden [IP: 185.125.190.83 80]  
Fetched 74.4 MB in 4s (19.9 MB/s)  
E: Failed to fetch http://archive.ubuntu.com/ubuntu/pool/main/g/gcc-defaults/gcc_9.3.0-1ubuntu2_amd64.deb  403  Forbidden [IP: 185.125.190.83 80]  
E: Failed to fetch http://archive.ubuntu.com/ubuntu/pool/main/libx/libxcrypt/libcrypt-dev_4.4.10-10ubuntu4_amd64.deb  403  Forbidden [IP: 185.125.190.83 80]  
E: Failed to fetch http://archive.ubuntu.com/ubuntu/pool/main/g/gcc-defaults/g++_9.3.0-1ubuntu2_amd64.deb  403  Forbidden [IP: 185.125.190.83 80]  
```  
It looks like this is a server-side issue. When I attempt to download one of the packages directly from the shown URL it works. Considering that many of the workflow jobs download Ubuntu packages from the same server at the same or nearly the same time my _guess_(!) is that the server has some kind of rate limit and rejects further downloads after what it sees as too many downloads over a short period with `403  Forbidden`. :thinking: Maybe a retry for only those failed jobs (currently clang++-8 and clang++-11) at some later point will do the trick.

---

## Comment 2

> Username: zajo  
> Created_at: 2025-06-01 20:14:27 UTC  
> Url: https://github.com/boostorg/leaf/pull/82#issuecomment-2927820394  

I tried rerunning the failed jobs and one of them is still going but the other failed, I wonder if we are hitting rate limit of some sort, possibly monthly rate limit? Given it's the end of May.

---

## Comment 3

> Username: striezel  
> Created_at: 2025-06-01 23:59:11 UTC  
> Url: https://github.com/boostorg/leaf/pull/82#issuecomment-2928152500  

@zajo Thank you for re-triggering those jobs and thereby making all those jobs pass. :+1:  
  
I am not really sure whether there is anything that can be done about those rate limits. One could try to switch to mirror servers (there are a few: https://launchpad.net/ubuntu/+archivemirrors) and try a different mirror for each job in the workflow so that no single server gets too many requests and hopefully no rate limit is encountered. However, that would complicate the workflow file quite a bit. I don't think that's worth the effort when one or two re-triggers can solve the issue.

---

## Comment 4

> Username: zajo  
> Created_at: 2025-06-02 02:52:38 UTC  
> Url: https://github.com/boostorg/leaf/pull/82#issuecomment-2928511521  

I say it doesn't matter why they were failing, I'll just merge it. Thank you for the PR.

---
