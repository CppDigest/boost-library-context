# #52 - Proposal for OpenHarmony Adaptation of [ variant2 ] [Open]

> Username: techvision0628  
> Created at: 2025-05-09 07:19:31 UTC  
> Updated at: 2025-05-09 07:19:31 UTC  
> Url: https://github.com/boostorg/variant2/issues/52  

## Background  
OpenHarmony is an open-source operating system and community that is widely used in the field of intelligent terminals.  
  
Variant2 library implements a type-safe discriminated/tagged union type. In order to enable variant2 to support the OpenHarmony system, we have modified some of the code of variant2. We plan to submit the modified code to the variant2 community. The purpose of creating this Issue is to initiate discussions with the community. We sincerely welcome you to put forward your valuable suggestions.    
  
## Adaptation Proposal  
We have completed the adaptation of variant2 on OpenHarmony. The specific content is as follows:  
  
- The construction/packaging of variant2 was realized based on the OpenHarmony environment and development tools.  
- Provide sample examples of using variant2 in OpenHarmony application development.  
  
We'll submit the adaptation code through a Pull Request (PR) promptly.  
  
## Testing Results  
Based on the OpenHarmony system, we conducted unit tests on variant2 using the original library test cases and passed the tests in the following versions:  
  
DevEco Studio 5.0.3 Release-5.0.9.221, SDK: API15(OpenHarmony 5.0.3.135)    
DevEco Studio 5.0.4 Beta1-5.0.9.320, SDK: API16 (OpenHarmony 5.0.4.150)    
DevEco Studio 5.0.3 Release-5.0.9.300, SDK: API15(OH_SDK 5.0.3.135(SP1))    
DevEco Studio 5.0.4 Beta1 5.0.9.320, SDK: API16(OH SDK 5.0.4.150)  
  
If necessary, we can provide detailed test reports at any time.  
  
## Labels  
enhancement, compatibility, cross-platform
