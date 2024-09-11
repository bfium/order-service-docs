---
date:
  created: 2024-08-11
  updated: 2024-08-16
#draft: true
readtime: 10
pin: true
links:
  - Homepage: index.md
  - Blog index: blog/index.md
    
authors:
  - bfium

slug: requirements  

tags:
  - IT
---


# Requirements
about the requirements of the Online shopping 
<!-- more -->

■ Loose coupling. 
Services should be relatively independent of each other. Thus, a service should hold a minimum amount of information about other services and ideally should not depend on other services.

■ Service contract. 
A service provides a contract, which a SOA application can rely on. The contract is typically defined in the service interface in the form of a set of operations. Each operation usually has input and output parameters, but it can also include quality of service parameters such as response time and availabil- ity. This principle builds on the object-oriented design concept of separating the interface and the implementation, and establishing the interface as the contract between the provider of the service and the user of the service.

■ Autonomy. 
Each service is self-contained, such that it can operate independently without the need of other services. This concept can be achieved by separating services from coordination, so that services do not directly communicate with each other.

■ Abstraction. 
As with object-oriented design, the details of a service are hidden, A service only reveals its interface in terms of the operations it provides, and for each operation, the inputs it needs, and the outputs it returns.

■ Reusability. 
A key goal of SOA is to design services that are reusable. The pre- ceding design goals of services are intended to facilitate reuse.

■ Composability. 
Services are designed to be capable of being assembled into larger composite services. In some cases, a composite service also needs to pro- vide coordination of the individual services.

■ Statelessness. 
Where possible, services maintain little or no information about specific client activities.

■ Discoverability. 
A service provides an external description to help allow it to be discovered by a discovery mechanism.

# Protocols
## Web services protocols
Application clients and services need to have a communication protocol for inter- component communication. Extensible Markup Language (XML) is a technology that allows different systems to interoperate through exchange of data and text. The Simple Object Access Protocol (SOAP), which is a lightweight protocol developed by the World Wide Web Consortium (W3C), builds on XML and HTTP to per- mit exchange of information in a distributed environment. SOAP defines a unified approach for sending XML-encoded data and consists of three parts: an envelope that defines a framework for describing what is in a message and how to process it, a set of encoding rules for expressing instances of application-defined data types, and a convention for representing remote procedure calls and responses.

## Web Services
Applications provide services for clients. One example of application services is Web services, which use the World Wide Web for application-to-application com- munication. From a software perspective, Web services are the application program- ming interfaces (APIs) that provide a standard means of communication among dif- ferent software applications on the World Wide Web. From a business application perspective, a Web service is business functionality provided by a company in the form of an explicit service over the Internet for other companies or programs to use. A Web service is provided by a service provider and may be composed of other ser- vices to form new services and applications. An example of a Web client invoking a Web service is given in Figure 16.5.

