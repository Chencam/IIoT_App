
# Initiate Industrial Internet Applications Using GE’s Predix Services 
## Project Readme File 
Building software as a service in industry is becoming widely popular in digital age. However, growing number of cyber attack in industry shocked industrial companies, customers, and the public. Currently, cyber security is the top issue in this scenario. To develop a secure application from square one or to adopt a security service from an industrial platform, that is a question. Actually, the second option is more reasonable and feasible.

In this project, I want to show how to initiate an industrial internet application with GE’s Predix Security Service. To achieve that, I need to use Predix platform with diverse applications and services.

First of all, I want to give a brief introduction of Industrial Internet. While the “regular” Internet is a “network of networks” that connects people with information, the Industrial Internet (sometimes refered to as Industrial IoT or IIoT) networks machines, systems, people, and physical industries— through the Internet—to collect, organize, and analyze the world’s industrial data, enabling the next generation of data-driven Digital Industrial companies.

Predix is an application platform especially made for building and operating apps for the Industrial Internet. Predix facilitates everything you’d expect from a state-of-the-art Platform-as-a-Service (PaaS), such as rapid deployment and elastic scale of cloud apps. Predix addresses tough challenges as follows:
•	Embedded software for standardized IoT connectivity 
•	Master data management (MDM) and data science tools for analyzing industrial assets and processes
•	Secured cloud environments for compliant management and operation of apps at industrial scale
To gain full access to Predix, I need to create a free trial account. To launch a Predix-enabled development environment to streamline my development effort, I need to be familiar with the different development tools needed for different application types. 

The frequently-used tools and their download links are as follows:
•	Cloud Foundry CLI: https://github.com/cloudfoundry/cli/releases
•	Git: https://git-scm.com/
•	Java SE Development Kit (JDK): https://www.oracle.com/downloads/index.html
•	Node.js: https://nodejs.org/en/download/
•	NPM: https://www.npmjs.com/
•	Maven: http://maven.apache.org/download.cgi
•	Eclipse: https://spring.io/tools/eclipse
•	The Spring Tool Suite (STS) for Eclipse: https://marketplace.eclipse.org/content/spring-tool-suite-sts-eclipse

I can install all the tools needed for cloud development to my laptop with Linux, OS X, or Windows. Or Get a VM Player, such as VirtualBox or VMWare Player, to run the DevBox. saved me some time as I learned Predix. Its download link is as follows: 
•	VirtualBox: https://www.virtualbox.org/
•	VMWare Player: https://my.vmware.com/web/vmware/free#desktop_end_user_computing/vmware_workstation_player/12_0
•	DevBox: https://www.predix.io/services/other-resources/devbox.html
DevBox is designed and tested with default settings to run up to four separate instances on my machine concurrently, depending on the capacity of the system. I have full root access, so I can extend and reconfigure the system as my project evolves, and all utilities available for Linux will work on DevBox.

## Design Documents 
### Security application Design
Cyber threats in industry are on the rise. In the face of this reality, as an industrial internet application developer, cyber security is not optional. We need to understand exactly what the security requirements are. This will depend on the application, but in the IoT world it's safe to say that customers will demand both authentication and authorization capabilities. The reason for this is simple -- customers need to restrict who has access to their devices and what they can do with them.

In the face of many solutions, and we might need to decide what technology to use that provides the required authentication and authorization. There are many open standards, such as OAuth 2.0, SAML, TLS 1.2, that have gained immense traction. We should not have to spend much time considering about the more fundamental aspects of what technology to use. Experts within the security community scrutinize these standards, and for the most part, the consensus is that they are secure. This helps drive adoption which in turn helps reduce the integration costs of building applications compatible with these standards. Reduced cost of integration means more development agility. That sounds great. However, implementing these standards on our own would take a long time through the process of trial and error. We really don’t want to be on our own.

PaaS offerings are well aware of the difficulty of building secure software on open standards, which is why the biggest names in PaaS offer services that provide identity management, authentication, and authorization capabilities using open standards. As an industrial strength PaaS for the Internet of Critical Things, Predix offers the User Account and Authentication (UAA) service for identity management and authentication as well as Access Control Service (ACS) for fine-grained authorization. 

UAA is an open source project that was originally created to provide identity management and authentication for Cloud Foundry. UAA provides: 
•	an OAuth 2.0 authorization server
•	a SCIM-based identity management solution
•	support for authentication based on the SAML Web SSO profile. 

ACS is the Predix solution for applications requiring fine-grained access control. While the tokens issued by UAA can contain information used for access control decisions, that information is tied to the lifetime of the token. In order for user privilege changes to take effect, the user must log out and log back in. Additionally, the privileges themselves are constrained by the maximum size of the token. ACS provides solutions to these shortcomings by providing dynamic attribute-based access control (ABAC). ACS can store user attributes, resource attributes, and access control policies that together define user privileges.

The illustration below shows how attribute-based access control works. At the top we define the basic entity model for ACS. There are subjects and resources and each has a set of attributes. Attributes can be anything that define a subject or resource, and are used in determining access privileges.  In the example below, attributes for the subject (chenye@mit) include group and role.  Attributes for the resource (/assets/02142) include site and group. When another microservice makes an access control request to ACS, it supplies the subject and resource identifier as well as the action the subject is trying to perform on the resource (e.g. GET). When ACS receives the request, it looks up the attributes belonging to the subject and resource, and forwards this information to a policy evaluation engine that decides whether the current set of policies allow the operation. A security policy contains a set of rules that determine the required permissions. In this case the policy contains conditions that compare subject attributes to resource attributes to determine if actions are allowed. Because the subject and resource belong to the same group, access is granted.

![](/Users/chen/Downloads/uaaacs.jpeg '描述')

By offering UAA and ACS, Predix aims to be a platform that solves not just basic security but also the more complex access control use cases that are common in the industry. In the Internet of Critical Things, the price for poorly implemented access control is too high. It is reasonable and feasible to use Predix Security Services when you initiate your industrial internet application. 

Sending Client Tokens to Predix Services is a best-practice because the granularity of access to those services is not sufficient to manage your Application security and Authorization needs. UAA manages my User accounts and application ClientId accounts and that I can plug in an external Identity Provider to UAA. ACS allows me to set up Policies to manage my Users using Attribute Based Access Control. My application should filter requests to it and request that ACS validate those requests by Users to application resources. 

### Setting up Users and Clients for User Token access to Predix Services
As the security administrator for my UAA instance I will create User accounts for my application users and a ClientId account for my application client.
As discussed above, sending User Tokens to the Predix Services is a sub-optimal application security pattern.  We want to show you this in order to draw a distinction of how to set up Users and ClientIds for the sub-optimal and the best-practice application security types.
The diagram below shows the steps for creating an instance of UAA with an admin password, adding a new client id and secret, and adding new user credentials.  
Users are added to UAA "groups" and we assign the clientId "scopes".  When my User logs in, if I pass their User Token to the Predix Service it will allow access if the User is in a UAA Group whose Group name matches the Service Scope name.


### Setting up Users and Clients for Client Token access to Predix Services
Sending a Client Token with a grant_type of "client_credentials" is a best-practice.  The diagram below shows the steps for creating an instance of UAA with an admin password, adding a new client id and secret, and adding in new client credentials. Users do not need to be in Groups.  ClientId gets Authorities instead of Scopes.



### Workflow
Let's take a look at how this all flows at runtime when a User with a Browser arrives at my application leveraging UAA for Login and ACS for Authorization. 




### Industrial Application Design (Interfaces)
#### Login 
https://chenye-predix-nodejs-starter.run.aws-usw02-pr.ice.predix.io/secure
IIoT_app.html
Account: chenyeuser
Password: chenye16user
After configuration process of UAA and ACS, with the user name and password you created, it is easy to start your own applications through login into your own space in Predix. 


#### Human Machine Interface for Compressor 

#### Chart of Temperature and Pressure from Instruments for Compressor
Make data comparisons with charts is a basic and necessary task as per the requirement of plant operation. 


#### Comparison among discharge pressure, discharge flow rate and rotational speed of compressors
The most critical data for a compressor is its discharge pressure, discharge flow rate and rotational speed. These data are closely related to the compressor surge. 

Surge is defined as the operating point at which centrifugal compressor peak head capability and minimum flow limits are reached. Actually, the working principle of a centrifugal compressor is increasing the kinetic energy of the fluid with a rotating impeller. The fluid is then slowed down in a volume called the plenum, where the kinetic energy is converted into potential energy in form of a pressure rise.
To display these data of a compressor in different status and data of different compressors in different plants in one Chart is more likely to study the correlation among diverse data and factors.


#### BOV (Blow Off Valve)
The blow off valve (BOV) plays an important role in the anti-surge control for a compressor. Typically, BOV is controlled through PID algorithm. The minimum difference between feedback and set point value of position makes sure the anti-surge control effect of compressor. This chart clearly shows the performance of BOV control.




#### Data Statistics 
Use javascript to complete data statistical tasks, which is helpful in data management and data analysis tasks later. 



Actually, industrial control systems, such as DeltaV, ControlLogix, PCS, from mainstream industrial automation companies, such as Emerson, Rockwell, Siemens, provide similar tools to realize the functions. However, these standard tools are offered to industry enterprise customers with expensive cost. GE’s Predix provide individual developers a powerful platform with open space, rich resources and low cost for making innovation. With the use experience of other control system, I build my application with similar data visualization and management. For comparison among discharge pressure, discharge flow rate and rotational speed of compressor, I I even create a chart that I have not seen in other software and platforms.


## Technology Demonstrations:
### Operational Technology
Operational Technology (OT) describes a piece of software or hardware that directly interacts with the physical world. It either receives information about the environment outside of itself through sensors; or, using actuators, it can make alterations to that environment. OT components represent the data sources ingested into the Industrial Internet, the points of connection between the physical and the digital. Industrial Control Systems (ICSs) are examples of OT, used to monitor and control the processes and interactions between sensors and actuators. ICSs have existed for as long as industrial processes; but the Industrial Internet—by connecting sensors, actuators, and control systems with cloud-based systems—provides ICSs with new meaning. 
### Industrial Internet
The Industrial Internet is weaving together OT systems with IT (Information Technology) systems, via a concept called OT / IT convergence, now enabling end-to-end automation of complex processes such as asset optimization, monitoring, repair and maintenance. In an effort to create consistency within Industrial Internet systems, the Industrial Internet Consortium (IIC) drafted the Industrial Internet Reference Architecture. The reference architecture has classified Industrial Internet systems into distinct domains, such as control, operations, information, application, and business. The Predix platform is an exemplary implementaton of the Industrial Internet Reference Architecture. 
### Predix
Predix is a comprehensive, modern platform with components that span from the machine to the cloud to enable industrial use cases. The primary components are as follows:
•	Predix Machine: Predix Machine is the software layer responsible for communicating with the industrial asset and the Predix Cloud, as well as running local applications, such as edge analytics. This component can be installed on gateways, industrial controllers and sensors.
•	Predix Connectivity: Predix Connectivity is for scenarios where a direct Internet connection is not readily available. The service enables machines to talk to the Predix Cloud through a virtual network comprised of cellular, fixed line, and satellite technologies.
•	Predix Cloud: Predix Cloud is a global secure cloud infrastructure that is optimized for industrial workloads and meeting regulatory needs.
•	Predix Service: Predix provides industrial services that developers can use to build, test, and run Industrial Internet applications. It also provides a microservices marketplace where developers can publish their own services as well as consume services from third parties.
•	Predix for Developers: Predix provides developers with a framework to communicate with services. Its modular design offers a consistent look and feel, as well as a contextual user experience in both web and mobile applications.

The architecture of Predix is shown in the diagram below.


### Security Service
The Predix platform provides Access Control Services (ACS) for application developers to add granular authorization mechanisms to access web applications and services without having to add complex authorization logic to their code. ACS works in conjunction with the User Account and Authentication (UAA) service in Cloud Foundry. 

User Account and Authentication (UAA) is a web service provided by Cloud Foundry to manage users and OAuth2 clients. Its primary role is as an OAuth2 provider, issuing tokens for client applications to use when they act on behalf of Cloud Foundry users. In collaboration with the login server, it can authenticate users with their Cloud Foundry credentials, and can act as an SSO service using those credentials (or others). The service provides endpoints for managing user accounts and for registering OAuth2 clients.

A combination of User Account and Authentication (UAA) service and Access Control Services (ACS) can provide a complete workflow for authentication and authorization. I can deploy various topologies when using UAA and ACS services. While the mechanism for authorization remains the same in all topologies, the authentication process varies depending on how the users are provisioned. 

### Google Charts
The Google Chart API is a tool that lets us easily create a chart from some data and embed it in a web page. Google creates a PNG image of a chart from data and formatting parameters in an HTTP request. Many types of charts are supported, and by making the request into an image tag, we can simply include the chart in a web page.
Google Charts provides a perfect way to visualize data on our website. From simple line charts to complex hierarchical tree maps, the chart gallery provides a large number of ready-to-use chart types.


## Competitive Analysis 
### Comparison among industrial platforms 
#### Emerson’s PlantWeb
PlantWeb is the proven digital plant architecture that uses the power of predictive intelligence to improve plant performance.

While PlantWeb lowers capital and engineering costs compared to traditional DCS-centered architectures, it provides even greater operational benefits by enabling you to improve throughput, availability, and quality, reduce conversion costs, and sustain the resulting performance gains. Users typically report at least 2% improvements in plant efficiency. 

PlantWeb isn't simply a product or a specific automation control system. It's a proven strategy for building a digital architecture, a blueprint for building solutions that optimize plant performance by leveraging digital intelligence, connecting your plant, controlling your process, and optimizing your assets.

#### Rockwell’s FactoryTalk
FactoryTalk Services Platform is a suite of services including Live Data, Directory, Audit, Security, Activation, and Alarm & Events. It is not a product that you purchase as a standalone software package, but is embedded in most of the products from Rockwell Software. FactoryTalk is the prefix to the majority of the Rockwell Software’s products, although there are some products that are enabled that do not carry the prefix. They are listed in the table as well as the features that are enabled in those products.

Here is each component of the FactoryTalk services platform: Live Data, Directory, Audit, Security, Activation, Alarm & Events.

The comparison among industrial platforms is shown in the table below.

Predix
PlantWeb
FactoryTalk
Company
GE
Emerson
Rockwell
Network Area
Internet (Cloud) 
Intranet 
Intranet 
Attribute of Function
Ecosystem
Platform
Platform
Attribute of System
Convergence of OT and IT System
OT System (DCS/PLC/SCADA)
OT System (DCS/PLC/SCADA)
Development Openness
GE and Non-GE Companies (Startups)
Emerson only
Rockwell only
Provide security service for the public (developer)
Yes
No
No

### Comparison between security standard
#### SAML
Security Assertion Markup Language (SAML) is an XML standard that allows a user to log on once to the log on site for all the trusted websites. SAML is designed for B2B and B2C transactions. SAML has the following components:
•	Assertions: Authentication, attribute, authorization
•	Protocols: HTTP, SMTP, FTP, SOAP
•	Bindings: SAML over SOAP, SAML over HTTP
#### SAML shortcomings
In 2011, a vulnerability termed as “XML signature wrapping vulnerability” was found. This vulnerability can be used to impersonate any user.
#### OAuth
OAuth provides authorization to APIs. I assume you have seen “import contact information” on LinkedIn, Facebook, etc., where the Service Providers ask you to grow your network by importing your contact list from your email providers such as Gmail, Yahoo, etc. With the OAuth 2.0 standard being published in 2012, now various frameworks are available which provide a layer of authentication over authorization too. OAuth has following roles:
•	A resource server called as OAuth Provider –the entity which is hosting the resource.
•	End User that owns the resource that is being requested.
•	Client –OAuth Consumer-the entity requesting for resource.
#### OAuth shortcomings
•	OAuth 1.0 was vulnerable to session fixation attack.
•	OAuth 2.0 does not have native encryption capabilities. It relies on the SSL/TLS protocol to provide encryption of the sensitive data being exchanged between parties to and fro.
#### OpenID
OpenID’s functions resemble that of SAML, but instead of limiting the usage to enterprise users, OpenID was designed for consumer apps and services. With OpenID, the enterprise users are also in scope now. OpenID is being provided by majors like Facebook, Google, Yahoo, etc. Below are the roles that OpenID provides:
•	End user who has OpenID and wants to verify the identity.
•	Resource Party which is the party that wants to verify the identity of end user.
•	OpenID provider which is the party used to verify end user.
#### OpenID shortcomings
•	OpenID has some authentication flaws in which an attacker can impersonate any user if the OpenID provider does not validate the signed email address.
•	OpenID is susceptible to phishing attacks where a rogue Resource Party can forward the user to another rogue OpenID provider and thus can obtain user credentials.
#### Which one to choose?
Following are the points which can be useful to consider which one to use among OpenID, OAuth or SAML or any of their combination.

•	If the use case is to develop SSO where at least one partner is enterprise use SAML, otherwise use OpenID.
•	If the use case involves mobile devices for API authorization then use OAuth.
•	If use case requires a centralized identity provider the use SAML.

In this project, I adopt GE’s UAA, which is based on the Oauth2, allowing applications to act on behalf of their users.  Since my application will be coordinating access to many underlying Predix Catalog Services, Oauth is the natural choice for implementing application security. Also, UAA provides support for authentication based on the SAML Web SSO profile.

#### Comparison between chart API
Google charts is easy to use in Predix web development. I tried other charts APIs, such as highcharts, but they did not work well in Predix.

## Storyboard 


## What I learn from the project
1. Better understand Industrial IoT and Predix platform

2. Better understand cyber security standards

3. Learn how to use Predix’s security service to initiate an application: A combination of User Account and Authentication (UAA) service and Access Control Services (ACS) can provide a complete workflow for authentication and authorization.

4. Try to develop and debug an application in my own space in Predix.

5. Make coding practice with javascript.

6. Convert an excel file into a json file with an add-in tool in excel.

7. Make practice of Google Charts to achieve data visualization.

8. Follow Predix’s tutorials is worth learning, although it was time-consuming. It shows that estimated completion is 15 mins a session, actually, it usually takes me 2~3 times as much time to read it and make a practice.

9. To ensure my development environment is ready to use, I learn to configure it with many tools. 
•	I use Cloud Foundry (CLI) to check apps, services, environment, and use it to log into the system and push my application to Predix cloud. 
•	I use NPM to build node application. 
•	I setup the maven build tool on my machine to be able to build and deploy the Adapter. The Predix Artifactory which holds the Predix Machine jar dependencies is password protected, so I need to add my Predix.io account to my Maven settings.xml. 
•	I use Eclipse to make changes to the REST microservice or to run the microservice locally. 
10. I learn some thing about Time Series Service and I want to have get real time value, but I do not have hardware, such as Raspberry Pi or Intel Edison. I wish I could try them later when I have time.
11. Predix provides hundreds of services, and even more in the near future. I learn something about RMD Reference App, and I know I need more resource for doing this. I hope to go further about it with support from GE.

## Timesheet

Time
Tasks
1st Week (Nov.3-Nov.10)
10 hrs
Design project; go through Predix tutorials
2nd Week (Nov.11-Nov.17)
15 hrs
Go through Predix tutorials; coding practice
3rd Week (Nov.18-Nov.24)
15 hrs
Go through Predix tutorials; coding practice
4th Week (Nov.25-Dec.1)
15 hrs
Go through Predix tutorials; coding practice
5th Week (Dec.2-Dec.8)
15 hrs
Test; Prepare documents, videos, and presentation
6th Week (Dec.9-Dec.15)
20 hrs
Code, Test and make improvement
6 Weeks
90 hrs
In total




## Resources:
https://www.predix.io/resources
http://resources.infosecinstitute.com/saml-oauth-openid/
https://pgjonline.com/2012/04/02/protecting-a-centrifugal-compressor-from-surge/



