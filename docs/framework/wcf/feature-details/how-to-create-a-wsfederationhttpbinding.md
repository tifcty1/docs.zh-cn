---
title: "如何：创建 WSFederationHttpBinding | Microsoft Docs"
ms.custom: ""
ms.date: "03/30/2017"
ms.prod: ".net-framework-4.6"
ms.reviewer: ""
ms.suite: ""
ms.technology: 
  - "dotnet-clr"
ms.tgt_pltfrm: ""
ms.topic: "article"
helpviewer_keywords: 
  - "联合"
  - "WCF, 联合"
ms.assetid: e54897d7-aa6c-46ec-a278-b2430c8c2e10
caps.latest.revision: 16
author: "Erikre"
ms.author: "erikre"
manager: "erikre"
caps.handback.revision: 16
---
# 如何：创建 WSFederationHttpBinding
在 [!INCLUDE[indigo1](../../../../includes/indigo1-md.md)] 中，<xref:System.ServiceModel.WSFederationHttpBinding> 类（配置中的 [\<wsFederationHttpBinding\>](../../../../docs/framework/configure-apps/file-schema/wcf/wsfederationhttpbinding.md)）提供一种用于公开联合服务的机制。  即，服务要求客户端使用安全令牌服务颁发的安全令牌进行身份验证。  本主题演示如何通过代码和配置来设置 <xref:System.ServiceModel.WSFederationHttpBinding>。  绑定一经创建，您就可以设置一个终结点来使用该绑定。  
  
 基本步骤概述如下：  
  
1.  选择一种安全模式。  <xref:System.ServiceModel.WSFederationHttpBinding> 支持 `Message`，它在消息级提供端对端的安全（即使跨多个跃点也是如此），还支持 `TransportWithMessageCredential`，它在客户端和服务可以通过 HTTPS 直接连接的情况下提供较好的性能。  
  
    > [!NOTE]
    >  <xref:System.ServiceModel.WSFederationHttpBinding> 还支持 `None` 作为安全模式。  此模式不安全，仅用于调试目的。  如果服务终结点是利用其安全模式设置为 `None` 的 <xref:System.ServiceModel.WSFederationHttpBinding> 来部署的，则得到的客户端绑定（由 [ServiceModel 元数据实用工具 \(Svcutil.exe\)](../../../../docs/framework/wcf/servicemodel-metadata-utility-tool-svcutil-exe.md) 生成）为 <xref:System.ServiceModel.WsHttpBinding>，其安全模式为 `None`。  
  
     与其他系统提供的绑定不同，使用 `WSFederationHttpBinding` 时无需选择客户端凭据类型。  这是因为客户端凭据类型始终为已颁发令牌。  [!INCLUDE[indigo2](../../../../includes/indigo2-md.md)] 从指定的颁发者获取一个令牌，并将该令牌提供给服务以对客户端进行身份验证。  
  
2.  在联合客户端上，将 <xref:System.ServiceModel.FederatedMessageSecurityOverHttp.IssuerAddress%2A> 属性设置为安全令牌服务的 URL。  将 <xref:System.ServiceModel.FederatedMessageSecurityOverHttp.IssuerBinding%2A> 设置为要使用的绑定，以便与安全令牌服务进行通信。  
  
3.  可选。  将 <xref:System.ServiceModel.FederatedMessageSecurityOverHttp.IssuedTokenType%2A> 属性设置为令牌类型的统一资源标识符 \(URI\)。  在联合服务上，指定服务期望使用的令牌类型。  在联合客户端上，指定客户端向安全令牌服务请求的令牌类型。  
  
     如果未指定令牌类型，则默认情况下，客户端将在无令牌类型 URI 的情况下生成 WS\-Trust 请求安全令牌 \(RST\)，而服务希望使用安全断言标记语言 \(SAML\) 1.1 令牌进行客户端身份验证。  
  
     SAML 1.1 令牌的 URI 为“http:\/\/docs.oasis\-open.org\/wss\/oasis\-wss\-saml\-token\-profile\-1.1\#SAMLV1.1”。  
  
4.  可选。  在联合服务上，将 <xref:System.ServiceModel.FederatedMessageSecurityOverHttp.IssuerMetadataAddress%2A> 属性设置为安全令牌服务的元数据 URL。  使用元数据终结点，服务的客户端可以选择一个适合的绑定\/终结点对，前提是将服务配置为发布元数据。  [!INCLUDE[crabout](../../../../includes/crabout-md.md)]发布元数据的更多信息，请参见[发布元数据](../../../../docs/framework/wcf/feature-details/publishing-metadata.md)。  
  
 您还可以设置其他属性，包括已颁发令牌中用作校验密钥的密钥类型、在客户端和服务之间使用的算法套件、是协商还是显式指定服务凭据、服务期望已颁发令牌中包含的任何特定声明，以及必须添加到由客户端发送到安全令牌服务的请求的其他 XML 元素。  
  
> [!NOTE]
>  仅当 `SecurityMode` 设置为 `Message` 时，`NegotiateServiceCredential` 属性才是相关的。  如果将 `SecurityMode` 设置为 `TransportWithMessageCredential`，则将忽略 `NegotiateServiceCredential` 属性。  
  
### 以代码方式配置 WSFederationHttpBinding  
  
1.  创建 <xref:System.ServiceModel.WSFederationHttpBinding> 的一个实例。  
  
2.  根据需要将 <xref:System.ServiceModel.WSFederationHttpSecurity.Mode%2A> 属性设置为 <xref:System.ServiceModel.WSFederationHttpSecurityMode> 或 <xref:System.ServiceModel.WSFederationHttpSecurityMode>。  如果需要非 <xref:System.ServiceModel.Security.SecurityAlgorithmSuite.Basic256%2A> 的算法套件，请将 <xref:System.ServiceModel.FederatedMessageSecurityOverHttp.AlgorithmSuite%2A> 属性设置为从 <xref:System.ServiceModel.Security.SecurityAlgorithmSuite> 中获取的值。  
  
3.  根据需要设置 <xref:System.ServiceModel.FederatedMessageSecurityOverHttp.NegotiateServiceCredential%2A> 属性。  
  
4.  根据需要将 <xref:System.ServiceModel.FederatedMessageSecurityOverHttp.IssuedKeyType%2A> 属性设置为 <xref:System.IdentityModel.Tokens.SecurityKeyType> `SymmetricKey` 或 `AsymmetricKey`。  
  
5.  将 <xref:System.ServiceModel.FederatedMessageSecurityOverHttp.IssuedTokenType%2A> 属性设置为适当的值。  如果未设置任何值，[!INCLUDE[indigo2](../../../../includes/indigo2-md.md)] 默认为“http:\/\/docs.oasis\-open.org\/wss\/oasis\-wss\-saml\-token\-profile\-1.1\#SAMLV1.1”，指示 SAML 1.1 令牌。  
  
6.  如果未指定本地颁发者则在客户端上是必需的；在服务上是可选的。  创建一个包含安全令牌服务的地址和标识信息的 <xref:System.ServiceModel.EndpointAddress>，然后将 <xref:System.ServiceModel.EndpointAddress> 实例分配给 <xref:System.ServiceModel.FederatedMessageSecurityOverHttp.IssuerAddress%2A> 属性。  
  
7.  如果未指定本地颁发者则在客户端上是必需的；不能在服务上使用。  为 `SecurityTokenService` 创建一个 <xref:System.ServiceModel.Channels.Binding>，并将 <xref:System.ServiceModel.Channels.Binding> 实例分配给 <xref:System.ServiceModel.FederatedMessageSecurityOverHttp.IssuerBinding%2A> 属性。  
  
8.  不能在客户端上使用；在服务上可选。  为安全令牌服务的元数据创建一个 <xref:System.ServiceModel.EndpointAddress> 实例，然后将其分配给 `IssuerMetadataAddress` 属性。  
  
9. 在客户端和服务上都是可选的。  创建一个或多个 <xref:System.ServiceModel.Security.Tokens.ClaimTypeRequirement> 实例，并将其添加到 <xref:System.ServiceModel.FederatedMessageSecurityOverHttp.ClaimTypeRequirements%2A> 属性返回的集合中。  
  
10. 在客户端和服务上都是可选的。  创建一个或多个 <xref:System.Xml.XmlElement> 实例，并将其添加到 <xref:System.ServiceModel.FederatedMessageSecurityOverHttp.TokenRequestParameters%2A> 属性返回的集合中。  
  
### 在配置中创建联合终结点  
  
1.  创建一个 [\<wsFederationHttpBinding\>](../../../../docs/framework/configure-apps/file-schema/wcf/wsfederationhttpbinding.md)，作为应用程序配置文件中 [\<绑定\>](../../../../docs/framework/configure-apps/file-schema/wcf/bindings.md) 元素的子元素。  
  
2.  创建一个 [\<绑定\>](../../../../docs/framework/misc/binding.md) 元素，作为 [\<wsFederationHttpBinding\>](../../../../docs/framework/configure-apps/file-schema/wcf/wsfederationhttpbinding.md)的子元素，并将 `name` 属性设置为适当的值。  
  
3.  创建一个 `<security>` 元素，作为 [\<绑定\>](../../../../docs/framework/misc/binding.md) 元素的子元素。  
  
4.  根据需要将 `<security>` 元素的 `mode` 属性设置为 `Message` 或 `TransportWithMessageCredential` 的一个值。  
  
5.  创建一个 `<message>` 元素，作为 `<security>` 元素的子元素。  
  
6.  可选。  将 `<message>` 元素的 `algorithmSuite` 属性设置为适当的值。  默认值为 `Basic256`。  
  
7.  可选。  如果需要非对称校验密钥，请将 `<message>` 元素的 `issuedKeyType` 属性设置为 `AsymmetricKey`。  默认值为 `SymmetricKey`。  
  
8.  可选。  设置 `<message>` 元素的 `issuedTokenType` 属性。  
  
9. 如果未指定本地颁发者则在客户端上是必需的；在服务上是可选的。  创建一个 `<issuer>` 元素，作为 `<message>` 元素的子元素。  
  
10. 将 `address` 属性设置为 `<issuer>` 元素，并指定安全令牌服务接受令牌请求的地址。  
  
11. 可选。  添加一个 `<identity>` 子元素，并指定安全令牌服务的标识。  
  
12. [!INCLUDE[crdefault](../../../../includes/crdefault-md.md)] [服务标识和身份验证](../../../../docs/framework/wcf/feature-details/service-identity-and-authentication.md).  
  
13. 如果未指定本地颁发者则在客户端上是必需的；不能在服务上使用。  在可以用于与安全令牌服务进行通信的绑定部分创建一个 [\<绑定\>](../../../../docs/framework/misc/binding.md) 元素。  [!INCLUDE[crabout](../../../../includes/crabout-md.md)]创建绑定的更多信息，请参见[如何：在配置中指定服务绑定](../../../../docs/framework/wcf/how-to-specify-a-service-binding-in-configuration.md)。  
  
14. 通过设置 `<issuer>` 元素的 `binding` 和 `bindingConfiguration` 属性，来指定在上一步创建的绑定。  
  
15. 不能在客户端上使用；在服务上可选。  创建一个 `<issuerMetadata>` 元素，作为 \<`message`\> 元素的子元素。  然后，在 `<issuerMetadata>` 元素的 `address` 属性中，指定安全令牌服务将要发布其元数据的地址。  还可以选择添加一个 `<identity>` 子元素，并指定安全令牌服务的标识。  
  
16. 在客户端和服务上都是可选的。  添加一个 `<claimTypeRequirements>` 元素，将其作为 `<message>` 元素的子元素。  通过将 [\<添加\>](../../../../docs/framework/configure-apps/file-schema/wcf/add-of-claimtyperequirements.md) 元素添加到 `<claimTypeRequirements>` 元素并利用 `claimType` 属性指定声明类型，来指定服务所依赖的必选和可选声明。  通过设置 `isOptional` 属性来指定给定的声明是必选的还是可选的。  
  
## 示例  
 下面的代码示例演示强制设置 `WSFederationHttpBinding` 的代码。  
  
 [!code-csharp[c_FederationBinding#2](../../../../samples/snippets/csharp/VS_Snippets_CFX/c_federationbinding/cs/source.cs#2)]
 <!-- TODO: review snippet reference [!code-vb[c_FederationBinding#2](../../../../samples/snippets/visualbasic/VS_Snippets_CFX/c_federationbinding/vb/source.vb#2)]  -->  
  
## 请参阅  
 [联合](../../../../docs/framework/wcf/feature-details/federation.md)   
 [联合示例](../../../../docs/framework/wcf/samples/federation-sample.md)   
 [如何：在 WSFederationHttpBinding 上禁用安全会话](../../../../docs/framework/wcf/feature-details/how-to-disable-secure-sessions-on-a-wsfederationhttpbinding.md)