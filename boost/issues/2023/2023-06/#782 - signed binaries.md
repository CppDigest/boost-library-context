# #782 - signed binaries [Open]

> Username: laurentcau  
> Created at: 2023-06-09 13:27:09 UTC  
> Updated at: 2023-06-14 21:46:21 UTC  
> Url: https://github.com/boostorg/boost/issues/782  

Hi,  
By chance, do you provide digital signed binaries (dlls) or do you plane to provide them in the futur?

---

## Comment 1

> Username: mclow  
> Created at: 2023-06-10 00:57:29 UTC  
> Url: https://github.com/boostorg/boost/issues/782#issuecomment-1585322277  

I don't believe that we have any plans to provide signed binaries.  
  
What platform(s) are you interested in?

---

## Comment 2

> Username: laurentcau  
> Created at: 2023-06-13 08:05:08 UTC  
> Url: https://github.com/boostorg/boost/issues/782#issuecomment-1588765209  

We're mainly interested for windows dlls but also MacOS and Linux (not sure they support signed library).

---

## Comment 3

> Username: teeks99  
> Created at: 2023-06-14 20:59:05 UTC  
> Url: https://github.com/boostorg/boost/issues/782#issuecomment-1591975874  

I think that would have to be supported by the build system (b2)[https://www.bfgroup.xyz/b2/], on which I'm not an expert, but I don't think is there.  
  
That said, the windows binary installers do get checksums applied to them before they are uploaded to the internet and those checksum files are signed with my GPG key. For example, [this file](https://boostorg.jfrog.io/ui/repos/tree/General/main/release/1.82.0/binaries/SHA256SUMS.asc) for the 1.82 release.

---

## Comment 4

> Username: mclow  
> Created at: 2023-06-14 21:46:21 UTC  
> Url: https://github.com/boostorg/boost/issues/782#issuecomment-1592030961  

We don't currently ship binaries for any platforms other than windows.
