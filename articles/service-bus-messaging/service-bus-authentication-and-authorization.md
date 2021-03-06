---
title: Azure Service Bus authentication and authorization | Microsoft Docs
description: Authenticate apps to Service Bus with Shared Access Signature (SAS) authentication.
services: service-bus-messaging
documentationcenter: na
author: axisc
editor: spelluru

ms.assetid: 18bad0ed-1cee-4a5c-a377-facc4785c8c9
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/22/2019
ms.author: aschhab

---
# Service Bus authentication and authorization

Applications gain access to Azure Service Bus resources using Shared Access Signature (SAS) token authentication. With SAS, applications present a token to Service Bus that has been signed with a symmetric key known both to the token issuer and Service Bus (hence "shared") and that key is directly associated with a rule granting specific access rights, like the permission to receive/listen or send messages. SAS rules are either configured on the namespace, or directly on entities such as a queue or topic, allowing for fine grained access control.

SAS tokens can either be generated by a Service Bus client directly, or they can be generated by some intermediate token issuing endpoint with which the client interacts. For example, a system may require the client to call an Active Directory authorization protected web service endpoint to prove its identity and system access rights, and the web service then returns the appropriate Service Bus token. This SAS token can be easily generated using the Service Bus token provider included in the Azure SDK. 

> [!IMPORTANT]
> If you are using Azure Active Directory Access Control (also known as Access Control Service or ACS) with Service Bus, note that the support for this method is now limited and you should migrate your application to use SAS. For more information, see [this blog post](https://blogs.msdn.microsoft.com/servicebus/2017/06/01/upcoming-changes-to-acs-enabled-namespaces/) and [this article](service-bus-migrate-acs-sas.md).

## Azure Active Directory
Azure Active Directory (Azure AD) integration for Service Bus resources provides role-based access control (RBAC) for fine-grained control over a client’s access to resources. You can use role-based access control (RBAC) to grant permissions to security principal, which may be a user, a group, or an application service principal. The security principal is authenticated by Azure AD to return an OAuth 2.0 token. The token can be used to authorize a request to access an Service Bus resource (queue, topic, etc.).

For more information about authenticating with Azure AD, see the following articles:

- [Authenticate with managed identities](service-bus-managed-service-identity.md)
- [Authenticate from an application](authenticate-application.md)

> [!IMPORTANT]
> Authorizing users or applications using OAuth 2.0 token returned by Azure AD provides superior security and ease of use over shared access signatures (SAS). With Azure AD, there is no need to store the tokens in your code and risk potential security vulnerabilities. We recommend that you use using Azure AD with your Azure Service Bus applications when possible. 


## Shared access signature
[SAS authentication](service-bus-sas.md) enables you to grant a user access to Service Bus resources, with specific rights. SAS authentication in Service Bus involves the configuration of a cryptographic key with associated rights on a Service Bus resource. Clients can then gain access to that resource by presenting a SAS token, which consists of the resource URI being accessed and an expiry signed with the configured key.

You can configure keys for SAS on a Service Bus namespace. The key applies to all messaging entities within that namespace. You can also configure keys on Service Bus queues and topics. SAS is also supported on [Azure Relay](../service-bus-relay/relay-authentication-and-authorization.md).

To use SAS, you can configure a [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) object on a namespace, queue, or topic. This rule consists of the following elements:

* *KeyName*: identifies the rule.
* *PrimaryKey*: a cryptographic key used to sign/validate SAS tokens.
* *SecondaryKey*: a cryptographic key used to sign/validate SAS tokens.
* *Rights*: represents the collection of **Listen**, **Send**, or **Manage** rights granted.

Authorization rules configured at the namespace level can grant access to all entities in a namespace for clients with tokens signed using the corresponding key. You can configure up to 12 such authorization rules on a Service Bus namespace, queue, or topic. By default, a [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) with all rights is configured for every namespace when it is first provisioned.

To access an entity, the client requires a SAS token generated using a specific [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule). The SAS token is generated using the HMAC-SHA256 of a resource string that consists of the resource URI to which access is claimed, and an expiry with a cryptographic key associated with the authorization rule.

SAS authentication support for Service Bus is included in the Azure .NET SDK versions 2.0 and later. SAS includes support for a [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule). All APIs that accept a connection string as a parameter include support for SAS connection strings.

## Next steps

- Continue reading [Service Bus authentication with Shared Access Signatures](service-bus-sas.md) for more details about SAS.
- How to [migrate from Azure Active Directory Access Control (ACS) to Shared Access Signature authorization](service-bus-migrate-acs-sas.md).
- [Changes To ACS Enabled namespaces](https://blogs.msdn.microsoft.com/servicebus/2017/06/01/upcoming-changes-to-acs-enabled-namespaces/).
- For corresponding information about Azure Relay authentication and authorization, see [Azure Relay authentication and authorization](../service-bus-relay/relay-authentication-and-authorization.md). 

