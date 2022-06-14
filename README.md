Objectives

This lab demonstrates release OSSM applications with a safe canary rollout.

Background

Traditional release model requires version rollback when defects are found.  
Canary release model solves this problem using a progressive deployment process where
both versions of the application, the previous and the current, run in parallel until the new version is
completely validated and ready for all users. 

The new version, called the "canary" initially receives only a small amount of all application traffic to minimize impact on limited set of users. 
As confidence level increases, the model progressively route more traffic to the new version.

Following diagram outlines at a very high level the Canary Release Model

![](canary.png)
