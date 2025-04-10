---
title: Cloud Foundry Services
owner: Core Services
---

import config from '@site/config'

This topic tells you about the services in <AppRuntimeFirst/>.

## <a id='instances'></a> Services and service instances

<AppRuntimeAbbr/> offers a marketplace of services that operators can use to provision reserved resources on demand.
Marketplace services include resources such as databases on a shared or dedicated server, or accounts on a SaaS app.
These resources are known as service instances and the systems that deliver and operate these resources are known as services.

For a service to be available in the Marketplace, an operator must integrate the service with <AppRuntimeAbbr/> using APIs.

{config.guide_name === 'Tile Developer' ? (
  <>
    For more information about provisioning service instances and other life cycle operations, see
    <a href="https://docs.vmware.com/en/VMware-Tanzu-Application-Service/3.0/tas-for-vms/services-managing-services.html">Managing Service Instances</a>.

    <AppRuntimeAbbr/> enables users to use services that are not available
    in the Marketplace using user-provided service instances (UPSI).
    For more information, see <a href="https://docs.vmware.com/en/VMware-Tanzu-Application-Service/3.0/tas-for-vms/services-user-provided.html">User-Provided Service Instances</a>.
  </>
) :
(
  <>
    For more information about provisioning service instances and other life cycle operations, see
    [Managing Service Instances](../devguide/services/managing-services.html).

    <AppRuntimeAbbr/> enables users to use services that are not available
    in the Marketplace using user provided service instances (UPSI).
    For more information, see [User-Provided Service Instances](../devguide/services/user-provided.html).
  </>
)}


## <a id='architecture-terminology'></a> Architecture and terminology

Services are integrated with <AppRuntimeAbbr/> by using a documented API for which the Cloud Controller is the client, called the Service Broker API.
This cannot be confused with the Cloud Controller API (CAPI), often used to refer to the version of Cloud Foundry.
The Service Broker API is versioned independently of CAPI.

The component of the service that uses the Service Broker API is called the _service broker_.
This component was formerly referred to as a service gateway.
However, as traffic between apps and services does not flow through the service broker, the term caused confusion.
Although "gateway" still appears in old code, the term "broker" is used in conversation, in new code, and in documentation.

Service brokers advertise a catalog of service offerings and service plans, as well as interpreting calls for provision, bind, unbind, and deprovision.
What a broker does with each call can vary between services. In general, 'provision' reserves resources on a service and 'bind' delivers information to an app necessary for accessing the resource. The reserved resource is called a _service instance_. What a service instance represents can vary by service. It could be a single database on a multi-tenant server, a dedicated cluster, or even an account on a web app.

![Services interact with the Cloud Foundry. The diagram shows the following components: 'Service Broker', 'cloud controller', 'App environment', and 'service instances.](../images/managed-services.png)

## <a id='credentials'></a> Service Instance credentials

<AppRuntimeAbbr/> enables users to provision credentials needed to interface with a service instance. You can use app binding to deliver these credentials to your <AppRuntimeAbbr/> app. For external and local clients, you can use service keys to generate credentials to communicate directly with a service instance.


### <a id='application-binding'></a> Binding Apps

Service instance credentials can be delivered to apps running on
<AppRuntimeAbbr/> in an environment variable. For more information, see

{config.guide_name === 'Tile Developer' ? (
  <a href="https://docs.vmware.com/en/VMware-Tanzu-Application-Service/3.0/tas-for-vms/services-application-binding.html">Delivering Service Credentials to an App</a>
) :
(<a href="../devguide/services/application-binding.html">Delivering Service Credentials to an App</a>)}

<br/>
<br/>

For information about binding to a specific app development framework, see <BuildpacksLink/>.

### <a id='service-keys'></a> Service keys

Credentials managed manually are known as service keys. Use service keys when you want a set of credentials for use by clients other than the app in the same space. For instance, you can use service keys to connect to a service instance from a local client, or from an app in another space, or even from outside of <AppRuntimeAbbr/>.

{config.guide_name === 'Tile Developer' ? (
  <>
  For more information about creating a user-provided service instance with service keys,
  see <a href="https://docs.vmware.com/en/VMware-Tanzu-Application-Service/3.0/tas-for-vms/services-user-provided.html">User-Provided Service Instances</a>.
  For more information about service keys, see <a href="https://docs.vmware.com/en/VMware-Tanzu-Application-Service/3.0/tas-for-vms/services-service-keys.html">Managing Service Keys</a>.
  </>
) :
(
  <>
    For more information about creating a user-provided service instance with service keys,
    see the <a href="../devguide/services/user-provided.html">User-Provided Service Instances</a> topic.
    For more information about service keys, see the <a href="../devguide/services/service-keys.html">Managing Service Keys</a> topic.
  </>
)}

<p> Not all services support service keys. Some services support credentials through app binding only.</p>


## <a id='implementation-deployment'></a> Implementation and deployment

How a service is implemented is up to the service provider or developer. <AppRuntimeAbbr/> only requires that the service provider implement the Service Broker API. A broker can be implemented as a separate app, or by adding the required HTTP endpoints to an existing service.

Because <AppRuntimeAbbr/> only requires that a service implements the Service Broker API to be available to <AppRuntimeAbbr/> end users, many deployment models are possible. The following are examples of valid deployment models:

* Entire service packaged and deployed by BOSH alongside <AppRuntimeAbbr/>
* Service broker packaged and deployed by BOSH alongside <AppRuntimeAbbr/>, rest of the service deployed and maintained by other means
* Broker (and optionally service) pushed as an app to <AppRuntimeAbbr/> user space
* Entire service, including broker, deployed and maintained outside of <AppRuntimeAbbr/> by other means


## <a id='external-ip'></a> Communication between Apps and Service Instances

To allow an app to communicate with a service external to <AppRuntimeAbbr/>, you might need to configure the service to accept connections from your app based on its outbound IP address.

In your external service configuration, you must do one of the following:

* Add the entire IP address range for the Diego Cell where the app is deployed to your allow list.
* Derive the app IP address from its DNS name using a command-line tool such as `dig`, `host`, or `nslookup`. In your external service configuration, add the IP address or range of the app instance to your allow list.


## <a id='log-management'></a> Streaming App Logs to Log Management Services

{config.guide_name === 'Tile Developer' ? (
  <>
    To learn how your app logs can be streamed to third-party log management services, see <a href="https://docs.vmware.com/en/VMware-Tanzu-Application-Service/3.0/tas-for-vms/services-log-management.html">Streaming App Logs to Log Management Services</a>.
  </>
) :
(
  <>
  To learn how your app logs can be streamed to third-party log management services, see <a href="../devguide/services/log-management.html">Streaming App Logs to Log Management Services</a>.
  </>
)}

User provided service instances can be used to drain app logs to a service not available in the Marketplace.
This is also known as setting up a syslog drain.

{config.guide_name === 'Tile Developer' ? (
  <>
    For guidance on configuring some third party log management services, see <a href="https://docs.vmware.com/en/VMware-Tanzu-Application-Service/3.0/tas-for-vms/services-log-management-thirdparty-svc.html">Service-Specific Instructions for Streaming App Logs</a>.
  </>
) :
(
  <>
    For guidance on configuring some third party log management services, see <a href="../devguide/services/log-management-thirdparty-svc.html">Service-Specific Instructions for Streaming App Logs</a>.
  </>
)}

## <a id='route-services'></a> Managing App Requests with Route Services

{config.guide_name === 'Tile Developer' ? (
  <>
    To learn how Marketplace services (and user-provided service instances) can be
    used to perform preprocessing on app requests, see <a href="https://docs.vmware.com/en/VMware-Tanzu-Application-Service/3.0/tas-for-vms/services-route-binding.html">Managing App Requests with Route Services</a>.
  </>
) :
(
  <>
    To learn how Marketplace services (and user-provided service instances) can be
    used to perform preprocessing on app requests, see <a href="../devguide/services/route-binding.html">Managing App Requests with Route Services</a>.
  </>
)}

## <a id='migrating'></a> Migrating a Database Schema

If your app relies on a relational database, you must apply schema changes periodically. To perform database schema migrations on <AppRuntimeAbbr/>-managed services, run a database migration task with the <AppRuntimeAbbr/> Command Line Interface (cf CLI) tool.

{config.guide_name === 'Tile Developer' ? (
  <>
    For more information about running cf CLI tasks, see <a href="https://docs.vmware.com/en/VMware-Tanzu-Application-Service/3.0/tas-for-vms/using-tasks.html">Running Tasks</a>.
  </>
) :
(
  <>
      For more information about running cf CLI tasks, see <a href="../devguide/using-tasks.html">Running Tasks</a>.
  </>
)}

<p> To run tasks with the cf CLI, you must install cf CLI v6.23.0 or later. For information about downloading, installing, and uninstalling
the cf CLI, see < vars.cli_install_link >.</p>

To run a database schema migration with the cf CLI:

1. Push the app:

    <pre class="terminal">$ cf push APP-NAME</pre>

    Where `APP-NAME` is the name of the app.

    <p> To run a task without starting the app, you can push the app with <code>cf push -i 0</code> and then run the task. You can run the app later
    by scaling up the instance count.</p>

1. Run database schema migration as a task on the app:

    <pre class="terminal">
    $ cf run-task APP-NAME --command "bin/rails db:migrate" --name TASK-NAME
    Creating task for app APP-NAME in org jdoe-org / space development as jdoe@pivotal.io...
    OK
    Task 1 has been submitted and ran successfully.
    </pre>

    Where:
    <ul>
      <li><code>APP-NAME</code> is the name of the app.</li>
      <li><code>TASK-NAME</code> is the name of the task.</li>
    </ul>
