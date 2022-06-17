## Learning Objective
Using OSSM to route subset of users to particular version of application

## Prerequisites

- OCP 4.7+ (with tenant admin access)
- OSSM 2.1
- Service Mesh Control Plane
- Kiali

## Use Case Summary

Chrome browser users will see "Hello Red Hat!" version of the application.
Non-Chrome browser users will see "Hello World!" version of the application.

## Start Lab

<p dir="auto">Create a new project called "headers":</p>
<div class="snippet-clipboard-content notranslate position-relative overflow-auto" data-snippet-clipboard-copy-content="oc new-project headers"><pre class="notranslate"><code>oc new-project headers
</code></pre></div>

<p dir="auto">Add project to Service Mesh Member Roll:</p>
<div class="snippet-clipboard-content notranslate position-relative overflow-auto" data-snippet-clipboard-copy-content="oc apply -f ServiceMeshMemberRoll.yaml -n headers"><pre class="notranslate"><code>oc apply -f ServiceMeshMemberRoll.yaml -n headers
</code></pre></div>

<p dir="auto">Setup the application CRD's:</p>
<UL>
<LI>Gateway: Uses istio-ingressgateway
<LI>VirtualService: Will be amended in later steps
<LI>Deployment:<br>
  - Sidecar injection is enabled<br>
  - v1 image: quay.io/redhattraining/ossm-maven-simplest:1.0<br>
  - v2 image: quay.io/redhattraining/ossm-maven-simplest:2.0<br>
</UL>
<div class="snippet-clipboard-content notranslate position-relative overflow-auto" data-snippet-clipboard-copy-content="oc apply -f application.yaml -n headers"><pre class="notranslate"><code>oc apply -f application.yaml -n headers
</code></pre></div>

<p dir="auto">Verify the pods are running.  If you see 2 containers per pod, it means sidecar proxy injection is successful.</p>
<div class="snippet-clipboard-content notranslate position-relative overflow-auto" data-snippet-clipboard-copy-content="oc get pods --watch -n headers"><pre class="notranslate"><code>oc get pods --watch -n headers
</code></pre></div>

![](headers-pods.png)

<p dir="auto">Create Destination Rule.  It defines logical subsets for our v1 and v2 applications which will be used by Virtual Service for routing.</p>
<div class="snippet-clipboard-content notranslate position-relative overflow-auto" data-snippet-clipboard-copy-content="oc apply -f destination-rule.yaml -n headers"><pre class="notranslate"><code>oc get apply -f destination-rule.yaml -n headers
</code></pre></div>

<p dir="auto">Create Virtual Service.</p>
<UL>
<LI>v2 routing logic:<br>
  - We are looking for HTTP Header called "User-Agent" which contains "Chrome" using regex<br>
  - If match, route to v2 subset which we defined earlier in Destination Rule
<LI>v1 routing logic:<br>
  - Default route if match not found
</UL>

![](http-match.png)

<p dir="auto">Note: the match rule is interpreted and executed in sequential order as defined in the yaml</p>
<div class="snippet-clipboard-content notranslate position-relative overflow-auto" data-snippet-clipboard-copy-content="oc apply -f virtual-service-with-header-subsets.yaml -n headers"><pre class="notranslate"><code>oc get apply -f virtual-service-with-header-subsets.yaml -n headers
</code></pre></div>

## Test Execution

- Different browsers send own User-Agent value, for example: 
  - Chrome browser sends User-Agent: Mozilla/5.0 (Macintosh; Intel Mac OS X 10_15_7) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/102.0.0.0 Safari/537.36
  - Firefox sends User-Agent: Mozilla/5.0 (Macintosh; Intel Mac OS X 10.15; rv:91.0) Gecko/20100101 Firefox/91.0
  
- Enter your {istio-ingressgateway.(your cluster domain)}/headers into your browser:
  - Using Chrome: you will see "Hello Red Hat!"
  - Using non-Chrome: you will see "Hello World!"
  
- You can experiment with more elaborate match/route logic by updating the VirtualService CRD
  - For example, you can look for custom HTTP headers with specific values, and route to versioned subsets depending on business needs
  - You can even combine traffic routing weights (as seen in our Canary Deployment lab) with header match logic
  
## Learning Summary

- Deploy versioned subsets of application
- Defined HTTP traffic matching rule and routing logic
- Performed simple test to verify match and routing logic performed as expected
- This powerful OSSM functionality can be expanded into advanced use cases for complex match and routing
  

