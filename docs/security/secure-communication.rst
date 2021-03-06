.. |date| date::

Secure communication
====================

Last changed: |date|

.. contents::

+-------------------------+---------------------+
| **Impact**              | High                |
+-------------------------+---------------------+
| **Implemented percent** | **33%** (2/6)       |
+-------------------------+---------------------+

.. _OpenStack Security Guide\: Secure communication: http://docs.openstack.org/security-guide/secure-communication.html

From `OpenStack Security Guide\: Secure communication`_:

  *There are a number of situations where there is a security
  requirement to assure the confidentiality or integrity of network
  traffic in an OpenStack deployment. This is generally achieved using
  cryptographic measures, such as the Transport Layer Security (TLS)
  protocol.*

Bottom line is that **all** endpoints, whether they are internal or
external, should be secured with encryption. TLS is strongly
preferred, due to recent published security vulnerabilities in SSL.

There are a number of services that need to be addressed:

* Compute API endpoints
* Identity API endpoints
* Networking API endpoints
* Storage API endpoints
* Messaging server
* Database server
* Dashboard


Certification authorities
-------------------------

The security guide recommends that we use separate PKI deployments for
internal systems and public facing services. In the future, we may
want to use separate PKI deployments for different security domains.

``[PASS]`` **Customer facing interfaces using trusted CA**
  All customer facing interfaces should be provisioned using
  Certificate Authorities that are installed in the operating system
  certificate bundles by default. It should just work without the
  customer having to accept an untrusted CA, or having to install some
  third-party software. We need certificates signed by a widely
  recognized public CA.

  * dashboard.uh-iaas.no
  * api.uh-iaas.no
  * access.uh-iaas-no
  * console.<region>.uh-iaas.no
  * <service>.api.<region>.uh-iaas.no

  * We use Digicert Terena CA on all customer facing interfaces.

``[DEFERRED]`` **Internal endpoints use non-public CA**
  As described above, it is recommended to use a private CA for
  internal endpoints.

  * db connection between regions use non-public CA
  * internal connection within regions use private network

  * FIXME: Identify and list all internal endpoints
  * FIXME: Ensure non-public CA on these endpoints


TLS libraries
-------------

From OpenStack Security Guide:

  *The TLS and HTTP services within OpenStack are typically implemented
  using OpenSSL which has a module that has been validated for FIPS
  140-2.*

We need to make sure that we're using an updated version of OpenSSL.

``[PASS]`` **Ensure updated OpenSSL**
  UH-IaaS is based on CentOS, and uses the OpenSSL library from that
  distro. We need to make sure that OpenSSL is up-to-date.

  * OpenSSL and all other packages are manually updated once a month.

Cryptographic algorithms, cipher modes, and protocols
-----------------------------------------------------

The security guide recommends using **TLS 1.2**, as previous versions
are known to be vulnerable. Furthermore, it is recommended to limit
the cipher suite to **ECDHE-ECDSA-AES256-GCM-SHA384**. It is
acceptable to
accept **HIGH:!aNULL:!eNULL:!DES:!3DES:!SSLv3:!TLSv1:!CAMELLIA** in
cases where we don't control both endpoints.

``[DEFERRED]`` **Ensure TLS 1.2**
  Make sure that only TLS 1.2 is used. Previous versions of TLS, as
  well as SSL, should be disabled completely.

``[DEFERRED]`` **Limit cipher suite on public endpoints**
  Limit the cipher suite on public facing endpoints to the
  general **HIGH:!aNULL:!eNULL:!DES:!3DES:!SSLv3:!TLSv1:!CAMELLIA**.

``[DEFERRED]`` **Limit cipher suite on internal endpoints**
  Limit the cipher suite on public facing endpoints
  to **ECDHE-ECDSA-AES256-GCM-SHA384**.
