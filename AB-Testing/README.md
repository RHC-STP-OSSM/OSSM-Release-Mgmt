## Learning Objective
Using OSSM to route subset of users to particular version of application

## Prerequisites

- OCP 4.7+ (with tenant admin access)
- OSSM 2.1
- Service Mesh Control Plane

## Use Case Summary

Chrome browser users will see "Hello Red Hat!" version of the application.
Non-Chrome browser users will see "Hello World!" version of the application.

## Start Lab

<p dir="auto">Create a new project called "headers":</p>
<div class="snippet-clipboard-content notranslate position-relative overflow-auto" data-snippet-clipboard-copy-content="oc new-project headers"><pre class="notranslate"><code>oc new-project headers
</code></pre></div>

<p dir="auto">Setup the application CRD's:</p>
<div class="snippet-clipboard-content notranslate position-relative overflow-auto" data-snippet-clipboard-copy-content="oc apply -f application.yaml -n headers"><pre class="notranslate"><code>oc apply -f application.yaml -n headers
</code></pre></div>
