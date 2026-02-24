# #1189 GPU Batch 8 [Merged]

> Username: mborland  
> Created at: 2024-08-30 17:56:15 UTC  
> Updated at: 2024-09-03 14:38:07 UTC  
> Merged at: 2024-09-03 14:08:22 UTC  
> Closed at: 2024-09-03 14:08:22 UTC  
> Url: https://github.com/boostorg/math/pull/1189  

This PR continues the process of workaround removal to expand functionality. Since all of the gamma functions have been marked up and tested in https://github.com/boostorg/math/pull/1187 we are able to now get all of the beta function family working. This includes: `beta`, `betac`, `ibeta`, `ibetac`, `ibeta_derivative`, `ibeta_inv`, `ibeta_inva`, `ibeta_invb`, `ibetac_inv`, `ibetac_inva`, `ibetac_invb` and lastly the beta distribution.  
  
Something worth noting is that most of the `ibeta` functionality seems to be expensive for NVRTC to parse as seen in the timing here: https://github.com/cppalliance/cuda-math/actions/runs/10635959725/job/29486716049?pr=19#step:9:202 The NVCC timing gap is not nearly as large: https://github.com/cppalliance/cuda-math/actions/runs/10635959725/job/29486715730?pr=19#step:9:208  
  
The on device complete CI runs can be found here: https://github.com/cppalliance/cuda-math/pull/19  
  
CC: @steppi, @dschmitz89, @izaid.

---

## Comment 1

> Username: mborland  
> Created_at: 2024-09-03 14:08:18 UTC  
> Url: https://github.com/boostorg/math/pull/1189#issuecomment-2326624588  

The only CI failure is a pretty typical s390x fail to clone. Should be safe to merge.

---
