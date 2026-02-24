# #194 - Fail to compile on ppc64le [Closed]

> Username: sdebionne  
> Created at: 2024-05-02 12:14:03 UTC  
> Updated at: 2024-05-14 08:32:49 UTC  
> Closed at: 2024-05-14 08:32:49 UTC  
> Url: https://github.com/boostorg/charconv/issues/194  

It looks like some floating point extension are not properly detected on ppc64le (gxx 13.2):  
  
https://dev.azure.com/conda-forge/feedstock-builds/_build/results?buildId=915982&view=logs&j=8c139a40-380b-5518-bf6b-edcb18cbf319&t=e71f8d0d-13be-528e-b5e7-bfc20e0db699&l=18942  
  
cc: https://github.com/conda-forge/boost-feedstock/pull/196

---

## Comment 1

> Username: mborland  
> Created at: 2024-05-03 06:12:21 UTC  
> Url: https://github.com/boostorg/charconv/issues/194#issuecomment-2092369576  

Can you try running from develop? I fixed a few ppc64le related issues since release, and locally everything seems fine with both long double ABIs.

---

## Comment 2

> Username: sdebionne  
> Created at: 2024-05-03 09:06:41 UTC  
> Url: https://github.com/boostorg/charconv/issues/194#issuecomment-2092605660  

Hi Matt, sure I can add patches to the build. Were you referring to https://github.com/boostorg/charconv/commit/100473b1dc3c3dfff0513e584c8f447824c7e34c ?

---

## Comment 3

> Username: mborland  
> Created at: 2024-05-03 10:32:20 UTC  
> Url: https://github.com/boostorg/charconv/issues/194#issuecomment-2092736741  

Yes. #193 will apply as well if you need quadmath support on ppc64le.

---

## Comment 4

> Username: sdebionne  
> Created at: 2024-05-14 08:32:18 UTC  
> Url: https://github.com/boostorg/charconv/issues/194#issuecomment-2109594144  

With https://github.com/boostorg/charconv/commit/100473b1dc3c3dfff0513e584c8f447824c7e34c we manage to build our Conda recipe for all platforms. Thank you!
