# Monolith
A self-contained, single-tiered software application; all the different layers of the app - UI, business, data access etc. are in the same codebase.  

Use-cases:
- Requirements are pretty simple 
- Limited traffic
- When it's certain that there won’t be an exponential growth in the user base

**Advantages:** 
- Simple architecture
- No complexity in handling different components

**Disadvantages:**
- **Continuous deployment is hard**: Even a minor code change in a layer needs a re-deployment of the entire application.
- **Regression Testing**: 
  - Layers are tightly coupled with each other; one change might affect all layers. 
  - Thorough regression testing of the entire application is needed after deployment.
- **Many Single Points Of Failure**.
- **Scalability Issues**: Since components cannot be separated, hard to scale horizontally.
- **Cannot Leverage Heterogeneous Technologies**: All components are forced to use a limited set of technologies for compatibility reasons.
- **Not stateless, not Cloud ready**: An app needs to be distributed and stateless to be Cloud ready.

# Microservice
Applies Single Responsibility and Separation of Concerns.  
Different features/tasks are split into separate respective modules/codebases which work in conjunction with each other forming a large service as a whole.  

Use-cases:
- Complex use-cases 
- User count is expected to grow exponentially
- High traffic

**Advantages:**  
- No single points of failure
- Leverage heterogenous technologies
- Independent and continuous deployments

**Disadvantages:**
- Complexities in management: Management and monitoring is complex
- No strong consistency: Hard to guarantee; things are eventually consistent
