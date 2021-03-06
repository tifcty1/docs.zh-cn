---
title: "nonComVisibleBaseClass MDA | Microsoft Docs"
ms.custom: ""
ms.date: "03/30/2017"
ms.prod: ".net-framework"
ms.reviewer: ""
ms.suite: ""
ms.technology: 
  - "dotnet-clr"
ms.tgt_pltfrm: ""
ms.topic: "article"
dev_langs: 
  - "VB"
  - "CSharp"
  - "C++"
  - "jsharp"
helpviewer_keywords: 
  - "visible classes"
  - "managed debugging assistants (MDAs), COM visible classes"
  - "nonComVisibleBaseClass MDA"
  - "COM visible classes"
  - "QueryInterface call failures"
  - "MDAs (managed debugging assistants), COM visible classes"
ms.assetid: 9ec1af27-604b-477e-9ee2-e833eb10d3ce
caps.latest.revision: 9
author: "mairaw"
ms.author: "mairaw"
manager: "wpickett"
caps.handback.revision: 9
---
# nonComVisibleBaseClass MDA
当本机或非托管代码在派生自非 COM 可见基类的 COM 可见托管类的 COM 可调用包装器 \(CCW\) 调用 `QueryInterface` 时，将激活 `nonComVisibleBaseClass` 托管调试助手 \(MDA\)。  仅在调用需要 COM 可见托管类的类接口或默认 `IDispatch` 的情况下，`QueryInterface` 调用才会引起 MDA 激活。  对应用了 <xref:System.Runtime.InteropServices.ClassInterfaceAttribute> 特性的显式接口调用 `QueryInterface`，并且调用由 COM 可见类显式实现时，则不会激活 MDA。  
  
## 症状  
 本机代码发起的`QueryInterface` 调用失败并返回 COR\_E\_INVALIDOPERATION HRESULT。  HRESULT 可能因运行时禁止可能激活此 MDA 的 `QueryInterface` 调用。  
  
## 原因  
 运行时不允许派生自非 COM 可见类的 COM 可见类的类接口或默认 `IDispatch` 接口调用 `QueryInterface`，因为可能引发版本控制问题。  例如，如果向非 COM 可见基类添加任何公共成员，则使用派生类的现有 COM 客户端可能会中止，因为进行此类更改时，包含基类成员的派生类的 vtable 可能被更改。  向 COM 公开的显式接口没有这个问题，因为它们不将接口的基成员包括到 vtable 中。  
  
## 解决方法  
 不要公开类接口。  定义显式接口并向其应用 <xref:System.Runtime.InteropServices.ClassInterfaceAttribute> 特性。  
  
## 对运行时的影响  
 此 MDA 对 CLR 无任何影响。  
  
## 输出  
 下面是有关在派生自非 COM 可见类 `Base` 的 COM 可见类 `Derived` 上调用 `QueryInterface` 的示例消息。  
  
```  
A QueryInterface call was made requesting the class interface of COM   
visible managed class 'Derived'. However since this class derives from   
non COM visible class 'Base', the QueryInterface call will fail. This   
is done to prevent the non COM visible base class from being   
constrained by the COM versioning rules.   
```  
  
## 配置  
  
```  
<mdaConfig>  
  <assistants>  
    <nonComVisibleBaseClass />  
  </assistants>  
</mdaConfig>  
```  
  
## 请参阅  
 <xref:System.Runtime.InteropServices.MarshalAsAttribute>   
 [Diagnosing Errors with Managed Debugging Assistants](../../../docs/framework/debug-trace-profile/diagnosing-errors-with-managed-debugging-assistants.md)   
 [互操作封送处理](../../../docs/framework/interop/interop-marshaling.md)