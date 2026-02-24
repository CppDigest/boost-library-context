# #1027 - Assessment of the difficulty in porting CPU architecture for boostorg/math [Closed]

> Username: wangyuliu  
> Created at: 2023-09-06 06:07:39 UTC  
> Updated at: 2023-09-06 06:10:58 UTC  
> Closed at: 2023-09-06 06:10:58 UTC  
> Url: https://github.com/boostorg/math/issues/1027  

Hello everyone! I am working on implementing a tool to assess the complexity of CPU architecture porting. It primarily focuses on RISC-V architecture porting. As part of my dataset, I have collected the boostorg/math project. **I would like to gather community opinions to support my assessment.** I appreciate your help and response! Based on scanning tools, the porting complexity is determined to be simple, with a small amount of code related to the CPU architecture in the project. (referring to the overall workload from adapting the project to a specific architecture to achieving full functionality on that architecture). Is this assessment accurate? I look forward to your help and response.  
各位好！我在实现一个关于CPU架构的移植工作复杂度评估的工具，主要面向riscv架构移植，我收集boostorg/math项目作为我的数据集之一，需要收集社区的意见作为支撑，期待您的帮助和回答！通过扫描工具得出其移植复杂度为简单，工程中与CPU架构相关的代码量较少，（这里指的是从工程尚未适配某架构到实现某架构的较完整功能的总体工作量）这样的判断是否准确？期待您的帮助与回答

---

## Comment 1

> Username: mborland  
> Created at: 2023-09-06 06:10:58 UTC  
> Url: https://github.com/boostorg/math/issues/1027#issuecomment-1707729676  

Yes. The library is supposed to be toolchain and platform agnostic.
