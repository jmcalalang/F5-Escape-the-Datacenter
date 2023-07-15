Escape the Datacenter
=====================

Inbound connections directly to applications do not need to exist. 

F5XC application delivery network can be the front door to all environments in a public cloud, private cloud, and physical locations. Having a SaaS edge, that can protect, host, and distribute traffic across all environments is a powerful tool that doesnt exist as a full suite except with F5.

The BIG-IP is working as a site services tier resource, only providing services to users in our UDF environment. The F5XC customer edge we deployed at the beginning of the lab will now expose the BIG-IP virtual (OpenShift Router) to the globe via the F5XC AnyCast global network.

Well start with working from low-level XC resources building up to analytics of the exposed services

Return to the F5XC console and login
------------------------------------

|image01|

.. note:: An email from UDF should have arrived for login to the `f5-xc-lab-mcn tenant`_.

Navigate to Health Checks
-------------------------

Navigate to health check resources

|image02|

Create a health check for the BIG-IP virtual
--------------------------------------------

Give our health check a name with our unique namespace appending a resource type for best practice

|image03|

OpenShift routes on the BIG-IP virtual listen for the route hostname, the health check needs to include this and a path that the route can map

Attributes:

- Specify Host Header: ``Host Header Value``
- Host Header Value: ``cafe.example.com``
- Path: ``/coffee``

|image04|

Apply health check, save, and exit

|image05|

Navigate to the origin pool resources
-------------------------------------

|image06|

Create an origin pool of the BIG-IP virtual
-------------------------------------------

From the perspective of F5XC, the BIG-IP virtual would be our Origin Pool existing at our customer edge site.

Give our origin pool a name with our unique namespace appending a resource type for best practice

|image07|

Specify the origin server
-------------------------

Attributes:

- Select Type of Origin Server: ``IP address of Origin Server on given Sites``
- IP: ``10.1.10.12``
- Site: ``The Unique Namespace Site Name``
- Select Network on the Site: ``Outside Network``

|image08|

Add TLS and Health Check for the Origin Pool
--------------------------------------------

The OpenShift router created resources with TLS certificates, since the BIG-IP is our origin server we need to encrypt traffic from the F5XC customer edge to the BIG-IP virtual. Attaching the health check created in this module will verify we should send traffic to the BIG-IP virtual

Attributes:

- TLS: ``Enable``
- SNI Selection: ``No SNI``
- TLS Security Level: ``High``
- Origin Server Verification:  ``Skip Verification``
- MTLS with Origin Servers: ``Disable``

|image09|

Navigate to the HTTP load balancer resources
--------------------------------------------

|image10|

Create the F5XC HTTP load balancer resource
-------------------------------------------

Give the HTTP load balancer a name with our unique namespace appending a resource type for best practice

The HTTP load balancer will listen on single or many Domain names. For the domain name use the unique namespace name with a domain of ``lab-mcn.f5demos.com``

Attributes:

- Domains: ``<unique namespace name>.lab-mcn.f5demos.com``
- HTTPS with Automatic Certificate: All attributes

|image11|

Configure HTTP load balancer routes
-----------------------------------

The route object is used to replace the header of our Host domain with the name the OpenShift router is expecting *cafe.example.com*

Navigate:

|image12|

Create:

|image13|

Attributes:

- Route Type: ``Simple Route``
- HTTP Method: ``ANY``
- Path Match: ``Regex``
- Regex: ``.*``
- Origin Pool: ``<unique namespace name or our origin pool>``
- Host Rewrite Method: ``Host Rewrite Value``
- Host Rewrite Value: ``cafe.example.com``

|image14|
|image15|

HTTP load balancer VIP advertisement
------------------------------------
 
Utilizing the F5XC application delivery network will advertise our service across the globe. The BIG-IP is already supplying a site service level resource, and F5XC will provide the global service resource.

Attributes:

- VIP Advertisment: ``Internet``

|image16|

Dynamic Certificate Creation
----------------------------

F5XC HTTP load balancers with Automatic certificates will create a Lets Encrypt certificate on your behalf if the DNS domain is delegated (like lab-mcn.f5demos.com). If the domain is not delegated you can add the challenge records to pass the validation and certificate creation. Manually uploading certificates is also an option

DnsDomainVerification

|image17|

DomainChallengeStarted

|image18|

DomainChallengePending

|image19|

DomainChallengeVerified

|image20|

CertificateValid

|image21|

Access our OpenShift Application through F5XC
---------------------------------------------

With the certificate created, we can now access the domain created for our OpenShift application. Browse the application a few times, and try different URI paths. Try from any browser, or the ocp-provisioner Firefox.

|image22|

Navigate to the HTTP load balancer analytics
--------------------------------------------

Management of the HTTP load balancers and Analytics of the load balancer are two different locations. This is preferred for RBAC of different roles

|image23|

Deep dive into HTTP load balancer analytics
-------------------------------------------

The dashboard page of the HTTP load balancer will be how the site is performing over the time window selected (default 5 minutes). This will be an average of all requests, and highlight locations of clients, and client types.

|image24|

The Origin Servers tab will show us origin health (based on our health check), and time metrics on the performance

|image25|

The Requests tab will let users dive into each request specifically, with metrics about the client, route trip times, and any security events that might have been triggered

|image25|

Module Complete
---------------

.. sectnum::

.. _`f5-xc-lab-mcn tenant`: https://f5-xc-lab-mcn.console.ves.volterra.io/

.. |image01| image:: images/image01.png
  :width: 50%
  :align: middle

.. |image02| image:: images/image02.png
  :width: 75%
  :align: middle

.. |image03| image:: images/image03.png
  :width: 75%
  :align: middle

.. |image04| image:: images/image04.png
  :width: 75%
  :align: middle

.. |image05| image:: images/image05.png
  :width: 75%
  :align: middle

.. |image06| image:: images/image06.png
  :width: 75%
  :align: middle

.. |image07| image:: images/image07.png
  :width: 75%
  :align: middle

.. |image08| image:: images/image08.png
  :width: 75%
  :align: middle

.. |image09| image:: images/image09.png
  :width: 75%
  :align: middle

.. |image10| image:: images/image10.png
  :width: 75%
  :align: middle

.. |image11| image:: images/image11.png
  :width: 75%
  :align: middle

.. |image12| image:: images/image12.png
  :width: 75%
  :align: middle

.. |image13| image:: images/image13.png
  :width: 75%
  :align: middle

.. |image14| image:: images/image14.png
  :width: 75%
  :align: middle

.. |image15| image:: images/image15.png
  :width: 75%
  :align: middle

.. |image16| image:: images/image16.png
  :width: 75%
  :align: middle

.. |image17| image:: images/image17.png
  :width: 75%
  :align: middle

.. |image18| image:: images/image18.png
  :width: 75%
  :align: middle

.. |image19| image:: images/image19.png
  :width: 75%
  :align: middle

.. |image20| image:: images/image20.png
  :width: 75%
  :align: middle

.. |image21| image:: images/image21.png
  :width: 75%
  :align: middle

.. |image22| image:: images/image22.png
  :width: 75%
  :align: middle

.. |image23| image:: images/image23.png
  :width: 75%
  :align: middle

.. |image24| image:: images/image24.png
  :width: 75%
  :align: middle

.. |image25| image:: images/image25.png
  :width: 75%
  :align: middle

.. |image26| image:: images/image26.png
  :width: 75%
  :align: middle