---
title: Discovering available brokered services
description: Discover available services supported by Visual Studio and the Visual Studio SDK, including service descriptors for obtaining an interface for each service.
monikerRange: '>= vs-2019'
ms.custom: SEO-VS-2020
ms.date: 01/07/2022
ms.topic: reference
helpviewer_keywords:
- brokered services, Visual Studio
- Visual Studio, brokered services
ms.assetid: 724eb24b-b87c-4971-a2e7-adee7afc03b2
author: aarnott
ms.author: andarno
manager: ansonh
ms.technology: vs-ide-sdk
ms.workload:
- vssdk
---
# Discovering available brokered services

Brokered services can be exposed by [!INCLUDE[vsprvs](../../code-quality/includes/vsprvs_md.md)] or its extensions.
This document describes how to discover brokered services exposed by [!INCLUDE[vsprvs](../../code-quality/includes/vsprvs_md.md)].

Refer to an extension's documentation to discover its own brokered services.

## Visual Studio services class

[!INCLUDE[vsprvs](../../code-quality/includes/vsprvs_md.md)] and the Visual Studio SDK offer many brokered services.
These services are each accessible via an <xref:Microsoft.ServiceHub.Framework.ServiceRpcDescriptor> that can often be obtained from the <xref:Microsoft.VisualStudio.VisualStudioServices> class.

Other brokered services may exist and be documented with specific  [!INCLUDE[vsprvs](../../code-quality/includes/vsprvs_md.md)] features.

The <xref:Microsoft.VisualStudio.VisualStudioServices> class declares a static property for various major or minor releases of [!INCLUDE[vsprvs](../../code-quality/includes/vsprvs_md.md)].
Each of these properties returns an instance of a class that declares properties that expose a <xref:Microsoft.ServiceHub.Framework.ServiceRpcDescriptor> for a brokered service.
In this way, you can readily discover the set of brokered services supported by a particular version of [!INCLUDE[vsprvs](../../code-quality/includes/vsprvs_md.md)].

For example, the brokered solution service was introduced in [!INCLUDE[vsprvs](../../code-quality/includes/vsprvs_md.md)] 2019 Update 5.
The <xref:Microsoft.ServiceHub.Framework.ServiceRpcDescriptor> for this service can be obtained from VisualStudioServices.VS2019_5Services.SolutionService with code like this:

```csharp
ServiceRpcDescriptor descriptor = Microsoft.VisualStudio.VisualStudioServices.VS2019_5.SolutionService;
```

## Version considerations

A [!INCLUDE[vsprvs](../../code-quality/includes/vsprvs_md.md)] extension should consume a brokered service using the latest available VS release property from the <xref:Microsoft.VisualStudio.VisualStudioServices> class, considering the minimum required version of [!INCLUDE[vsprvs](../../code-quality/includes/vsprvs_md.md)] that the extension targets.
Taking the previous solution service example, this would mean that an extension that targets [!INCLUDE[vsprvs](../../code-quality/includes/vsprvs_md.md)] 2019 Update 9 or later should acquire the VisualStudioServices.VS2019_5Services.SolutionService descriptor from the VisualStudioServices.VS2019_9 property.
Let's update the prior example to the following code:

```csharp
ServiceRpcDescriptor descriptor = Microsoft.VisualStudio.VisualStudioServices.VS2019_9.SolutionService;
```

Acquiring the descriptor from the latest versioned property on <xref:Microsoft.VisualStudio.VisualStudioServices> ensures the service RPC will be as efficient as possible and the service itself will provide the latest behavior.
When updating your extension to require a newer version of [!INCLUDE[vsprvs](../../code-quality/includes/vsprvs_md.md)], consider reviewing all use of the <xref:Microsoft.VisualStudio.VisualStudioServices> class and update references to descriptors to use the latest versioned properties to match the [!INCLUDE[vsprvs](../../code-quality/includes/vsprvs_md.md)] version you're targeting.

A brokered service that was acquired using a <xref:Microsoft.ServiceHub.Framework.ServiceRpcDescriptor> from an older versioned property on <xref:Microsoft.VisualStudio.VisualStudioServices> may emulate its behavior as it was in that older version, including possibly reproducing quirky behavior or feature limitations.

A [!INCLUDE[vsprvs](../../code-quality/includes/vsprvs_md.md)] extension that targets and runs on a particular version of [!INCLUDE[vsprvs](../../code-quality/includes/vsprvs_md.md)] may request a brokered service that ends up being activated across a Live Share connection.
In such cases, the remote [!INCLUDE[vsprvs](../../code-quality/includes/vsprvs_md.md)] process may be running a different version from the local [!INCLUDE[vsprvs](../../code-quality/includes/vsprvs_md.md)] process.
A request for a brokered service may fail on a Live Share guest during a Live Share session when the Live Share host does not offer that brokered service of the requested version.

Consider including a fallback plan for when the brokered service isn't available.
This fallback plan may include requesting the brokered service again from an older versioned property on <xref:Microsoft.VisualStudio.VisualStudioServices> as that could succeed if the Live Share host is an older version of [!INCLUDE[vsprvs](../../code-quality/includes/vsprvs_md.md)].
Keep in mind when doing so that some members of the service interface might not be available on the service implementation itself.

A brokered service [may add members](../best-practices-design-brokered-service.md#AddingInterfaceMembers) to its service interface even after shipping.
Pay attention to the IntelliSense documentation on each member of the interface to see if a minimum required service version is mentioned on that member.

An attempt to invoke a member that the remote service doesn't actually implement will throw an <xref:StreamJsonRpc.RemoteMethodNotFoundException>.

## See also

- [Using and Providing Brokered Services](../../extensibility/use-and-provide-brokered-services.md)
