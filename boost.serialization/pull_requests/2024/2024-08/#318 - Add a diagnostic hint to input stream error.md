# #318 Add a diagnostic hint to input stream error [Open]

> Username: swebb2066  
> Created at: 2024-08-02 03:05:00 UTC  
> Updated at: 2024-08-02 03:05:00 UTC  
> Url: https://github.com/boostorg/serialization/pull/318  

This PR aims to save users some time when diagnosing an input_stream_error.  
  
For example, a NaN value serialized as `<PlanningCost>-nan(ind)</PlanningCost>` results in an input_stream_error exception when the file is reloaded.

---
