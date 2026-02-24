# #366 Performance reporting for constants [Merged]

> Username: NAThompson  
> Created at: 2020-06-06 01:06:12 UTC  
> Updated at: 2020-06-06 13:11:56 UTC  
> Merged at: 2020-06-06 13:11:53 UTC  
> Closed at: 2020-06-06 13:11:53 UTC  
> Url: https://github.com/boostorg/math/pull/366  



---

## Comment 1

> Username: NAThompson  
> Created_at: 2020-06-06 01:39:41 UTC  
> Updated_at: 2020-06-06 02:45:19 UTC  
> Url: https://github.com/boostorg/math/pull/366#issuecomment-639947123  

These performance numbers are incredibly baffling. For example, here's pi:  
  
```  
Pi/512                 0.125 us        0.125 us      5505655  
Pi/1024                0.188 us        0.188 us      3728422  
Pi/2048                0.211 us        0.211 us      3291036  
Pi/4096                0.139 us        0.139 us      4965032  
Pi/8192                0.158 us        0.158 us      4410894  
Pi/16384               0.209 us        0.209 us      3338357  
Pi/32768               0.344 us        0.344 us      1970632  
Pi/65536                1.36 us         1.35 us       518350  
Pi/131072               3.06 us         3.05 us       229667  
Pi/262144               5.50 us         5.49 us       127616  
Pi/524288               12.3 us         12.3 us        56564  
Pi/1048576           1076651 us      1074717 us            1  
```  
  
It looks like this might be a bug in MPFR. The spikes occur at certain precisions:  
  
```  
Pi/1006592              1096 ms         1087 ms            1  
Pi/1014784              1202 ms         1193 ms            1  
Pi/1022976             0.028 ms        0.028 ms        24577  
Pi/1031168             0.037 ms        0.036 ms        18572  
Pi/1039360             0.028 ms        0.028 ms        24403  
Pi/1047552             0.037 ms        0.037 ms        18753  
Pi/1055744             0.038 ms        0.038 ms        19252  
Pi/1063936             0.029 ms        0.029 ms        23121  
Pi/1072128             0.037 ms        0.037 ms        18908  
Pi/1080320             0.039 ms        0.038 ms        18351  
Pi/1088512             0.030 ms        0.029 ms        23477  
Pi/1096704             0.029 ms        0.029 ms        23553  
Pi/1104896             0.030 ms        0.030 ms        23375  
Pi/1113088              1337 ms         1314 ms            1  
Pi/1121280             0.039 ms        0.039 ms        17818  
Pi/1129472             0.042 ms        0.042 ms        16781  
Pi/1137664             0.031 ms        0.031 ms        22727  
Pi/1145856             0.040 ms        0.039 ms        17729  
Pi/1154048             0.040 ms        0.040 ms        17560  
Pi/1162240             0.041 ms        0.041 ms        17012  
Pi/1170432             0.032 ms        0.032 ms        21854  
Pi/1178624             0.035 ms        0.034 ms        20356  
Pi/1186816             0.041 ms        0.041 ms        17020  
Pi/1195008             0.042 ms        0.042 ms        16965  
Pi/1203200             0.033 ms        0.032 ms        21463  
Pi/1211392             0.043 ms        0.043 ms        16469  
Pi/1219584              1529 ms         1514 ms            1  
Pi/1227776             0.043 ms        0.043 ms        16401  
Pi/1235968             0.034 ms        0.033 ms        20410  
Pi/1244160             0.044 ms        0.044 ms        15989  
Pi/1252352             0.044 ms        0.044 ms        13900  
Pi/1260544             0.034 ms        0.033 ms        20754  
Pi/1268736             0.036 ms        0.036 ms        20403  
Pi/1276928             0.036 ms        0.035 ms        18309  
Pi/1285120             0.036 ms        0.036 ms        19594  
Pi/1293312             0.061 ms        0.056 ms        12831  
Pi/1301504             0.055 ms        0.053 ms        10915  
Pi/1309696             0.041 ms        0.040 ms        13591  
Pi/1317888             0.052 ms        0.050 ms        13093  
Pi/1326080             0.040 ms        0.039 ms        18608  
Pi/1334272             0.052 ms        0.051 ms        13509  
Pi/1342464              1787 ms         1759 ms            1  
```

---

## Comment 2

> Username: NAThompson  
> Created_at: 2020-06-06 01:49:57 UTC  
> Url: https://github.com/boostorg/math/pull/366#issuecomment-639951158  

@jzmaddock : The itrunc I remove was overflowing; I have no clue why it was there. Could you take a quick look?

---

## Comment 3

> Username: jzmaddock  
> Created_at: 2020-06-06 07:57:29 UTC  
> Url: https://github.com/boostorg/math/pull/366#issuecomment-640007997  

That's weird, since the result was never used, yes remove it!

---
