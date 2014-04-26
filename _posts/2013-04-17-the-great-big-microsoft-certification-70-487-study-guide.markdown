---
layout: post
title: The Great Big Microsoft Certification 70-487 Study Guide
category: Learning
tags: [iis, entity framework, webapi, xml, certification, 70-487, ado.net, azure, caching, deployment, nuget, odata, transactions, wcf, web services, windows]
comments: true
share: true
---
*Update: I passed the exam, so this guide isn't completely useless :). I've also posted two files that I used to help me better prepare for the exam. [You can find them here](http://www.levibotelho.com/?p=272).*

<hr />
I've been fairly busy lately studying for the Microsoft Certification 70-487 which I plan to pass in the near future (hence the lack of recent blog posts). In the course of studying I have compiled a list of links which provide information on (nearly) all the topics which, [according to the Microsoft Learning website, may appear on the test](http://www.microsoft.com/learning/en/us/exam.aspx?id=70-487#fbid=M8sTcbupBUh).

Before we get to the list, a quick disclaimer: I have not yet taken the test, and as such cannot make any guarantee as to the usefulness of these links. I am deliberately posting this before taking the exam so that in no way this can constitute a violation of Microsoft Learning's NDA.

So, with that taken care of, here are the links!
<a id="more"></a><a id="more-212"></a>

<hr />
# Accessing Data (24%)

## Choose data access technologies

<ul>
<li>Choose a technology (ADO.NET, Entity Framework, WCF Data Services) based on application requirements</li>
</ul>
## Implement caching

<ul>
<li>Cache static data, apply cache policy (including expirations)
<ul>
<li>[http://msdn.microsoft.com/en-us/library/ff477235.aspx](http://msdn.microsoft.com/en-us/library/ff477235.aspx)</li>
</ul>
</li>
<li>Use CacheDependency to refresh cache data
<ul>
<li>[http://www.dotnetcurry.com/ShowArticle.aspx?ID=331](http://www.dotnetcurry.com/ShowArticle.aspx?ID=331)</li>
</ul>
</li>
<li>Query notifications
<ul>
<li>[http://msdn.microsoft.com/en-us/library/t9x04ed2(v=vs.80).aspx](http://msdn.microsoft.com/en-us/library/t9x04ed2(v=vs.80).aspx)</li>
<li>[http://msdn.microsoft.com/en-us/library/system.web.caching.sqlcachedependency.aspx](http://msdn.microsoft.com/en-us/library/system.web.caching.sqlcachedependency.aspx)</li>
<li>[http://msdn.microsoft.com/en-us/library/9dz445ks(v=vs.110).aspx](http://msdn.microsoft.com/en-us/library/9dz445ks(v=vs.110).aspx)</li>
<li>[http://msdn.microsoft.com/en-US/library/wd2x83zk(v=vs.80).aspx](http://msdn.microsoft.com/en-US/library/wd2x83zk(v=vs.80).aspx)</li>
</ul>
</li>
</ul>
## Implement transactions

<ul>
<li>Manage transactions by using the API from System.Transactions namespace
<ul>
<li>[http://msdn.microsoft.com/en-us/library/a90c30fy.aspx](http://msdn.microsoft.com/en-us/library/a90c30fy.aspx)</li>
<li>[http://msdn.microsoft.com/en-us/library/ms172152.aspx](http://msdn.microsoft.com/en-us/library/ms172152.aspx)</li>
</ul>
</li>
<li>Implement distributed transactions
<ul>
<li>[http://www.c-sharpcorner.com/UploadFile/mosessaur/TransactionScope04142006103850AM/TransactionScope.aspx](http://www.c-sharpcorner.com/UploadFile/mosessaur/TransactionScope04142006103850AM/TransactionScope.aspx)</li>
<li>[http://msdn.microsoft.com/en-us/library/system.transactions.transactionscopeoption.aspx](http://msdn.microsoft.com/en-us/library/system.transactions.transactionscopeoption.aspx)</li>
</ul>
</li>
<li>Specify transaction isolation level
<ul>
<li>[http://msdn.microsoft.com/en-us/library/system.transactions.isolationlevel.aspx](http://msdn.microsoft.com/en-us/library/system.transactions.isolationlevel.aspx)</li>
<li>[http://msdn.microsoft.com/en-us/library/ms173763.aspx](http://msdn.microsoft.com/en-us/library/ms173763.aspx)</li>
</ul>
</li>
</ul>
## Implement data storage in Windows Azure

<ul>
<li>Access data storage in Windows Azure
<ul>
<li>[http://www.windowsazure.com/en-us/develop/net/how-to-guides/blob-storage/](http://www.windowsazure.com/en-us/develop/net/how-to-guides/blob-storage/)</li>
<li>[http://www.windowsazure.com/en-us/develop/net/how-to-guides/table-services/](http://www.windowsazure.com/en-us/develop/net/how-to-guides/table-services/)</li>
<li>[http://www.windowsazure.com/en-us/develop/net/how-to-guides/queue-service/](http://www.windowsazure.com/en-us/develop/net/how-to-guides/queue-service/)</li>
<li>[http://www.windowsazure.com/en-us/develop/net/how-to-guides/sql-database/](http://www.windowsazure.com/en-us/develop/net/how-to-guides/sql-database/)</li>
</ul>
</li>
<li>Choose data storage mechanism in Windows Azure (blobs, tables, queues, SQL Database)
<ul>
<li>[http://social.technet.microsoft.com/wiki/contents/articles/1674.data-storage-offerings-on-the-windows-azure-platform.aspx](http://social.technet.microsoft.com/wiki/contents/articles/1674.data-storage-offerings-on-the-windows-azure-platform.aspx)</li>
</ul>
</li>
<li>Distribute data by using the Content delivery network (CDN)
<ul>
<li>[http://www.windowsazure.com/en-us/develop/net/common-tasks/cdn/](http://www.windowsazure.com/en-us/develop/net/common-tasks/cdn/)</li>
<li>[http://msdn.microsoft.com/en-us/library/windowsazure/ff919703.aspx](http://msdn.microsoft.com/en-us/library/windowsazure/ff919703.aspx)</li>
</ul>
</li>
<li>Handle exceptions by using retries (SQL Database)
<ul>
<li>[http://social.technet.microsoft.com/wiki/contents/articles/4235.retry-logic-for-transient-failures-in-windows-azure-sql-database.aspx](http://social.technet.microsoft.com/wiki/contents/articles/4235.retry-logic-for-transient-failures-in-windows-azure-sql-database.aspx)</li>
<li>[http://msdn.microsoft.com/en-us/library/microsoft.practices.transientfaulthandling.retrypolicy_members.aspx](http://msdn.microsoft.com/en-us/library/microsoft.practices.transientfaulthandling.retrypolicy_members.aspx)</li>
<li>[http://msdn.microsoft.com/en-us/library/windowsazure/microsoft.windowsazure.storage.retrypolicies](http://msdn.microsoft.com/en-us/library/windowsazure/microsoft.windowsazure.storage.retrypolicies)</li>
</ul>
</li>
<li>Manage Windows Azure Caching
<ul>
<li>[http://www.windowsazure.com/en-us/develop/net/how-to-guides/cache/](http://www.windowsazure.com/en-us/develop/net/how-to-guides/cache/)</li>
</ul>
</li>
</ul>
## Create and implement a WCF Data Services service

<ul>
<li>[http://msdn.microsoft.com/en-us/data/odata.aspx](http://msdn.microsoft.com/en-us/data/odata.aspx)</li>
<li>[http://msdn.microsoft.com/en-us/library/cc668794.aspx](http://msdn.microsoft.com/en-us/library/cc668794.aspx)</li>
<li>[http://msdn.microsoft.com/en-us/library/cc668792.aspx](http://msdn.microsoft.com/en-us/library/cc668792.aspx)</li>
<li>[http://msdn.microsoft.com/en-us/library/dd728286.aspx](http://msdn.microsoft.com/en-us/library/dd728286.aspx)</li>
<li>Address resources
<ul>
<li>[http://msdn.microsoft.com/en-us/library/dd728283.aspx](http://msdn.microsoft.com/en-us/library/dd728283.aspx)</li>
</ul>
</li>
<li>Implement filtering/Create a query expression
<ul>
<li>[http://msdn.microsoft.com/en-us/library/dd673933.aspx](http://msdn.microsoft.com/en-us/library/dd673933.aspx)</li>
<li>[http://www.odata.org/documentation/uri-conventions](http://www.odata.org/documentation/uri-conventions)</li>
</ul>
</li>
<li>Access payload formats (including JSON)
<ul>
<li>[http://msdn.microsoft.com/en-us/library/dd728282.aspx#sectionSection0](http://msdn.microsoft.com/en-us/library/dd728282.aspx#sectionSection0)</li>
</ul>
</li>
<li>Use data service interceptors and service operators
<ul>
<li>[http://msdn.microsoft.com/en-us/library/dd744842.aspx](http://msdn.microsoft.com/en-us/library/dd744842.aspx)</li>
<li>[http://msdn.microsoft.com/en-us/library/dd744837.aspx](http://msdn.microsoft.com/en-us/library/dd744837.aspx)</li>
<li>[http://msdn.microsoft.com/en-us/library/cc668788.aspx](http://msdn.microsoft.com/en-us/library/cc668788.aspx)</li>
<li>[http://msdn.microsoft.com/en-us/library/dd744841.aspx](http://msdn.microsoft.com/en-us/library/dd744841.aspx)</li>
</ul>
</li>
</ul>
## Manipulate XML data structures

<ul>
<li>Read, filter, create, modify XML data structures
<ul>
<li>[http://msdn.microsoft.com/en-us/library/bb387098.aspx](http://msdn.microsoft.com/en-us/library/bb387098.aspx)</li>
</ul>
</li>
<li>Manipulate XML data by using XMLReader, XMLWriter, XMLDocument, XPath, LINQ to XML
<ul>
<li>[http://www.tizag.com/xmlTutorial/xpathtutorial.php](http://www.tizag.com/xmlTutorial/xpathtutorial.php)</li>
</ul>
</li>
<li>Transform XML by using XSLT transformations
<ul>
<li>[http://www.tizag.com/xmlTutorial/xslttutorial.php](http://www.tizag.com/xmlTutorial/xslttutorial.php)</li>
<li>[http://www.w3schools.com/xsl/xsl_w3celementref.asp](http://www.w3schools.com/xsl/xsl_w3celementref.asp)</li>
</ul>
</li>
</ul>
# Querying and Manipulating Data by Using the Entity Framework (20%)

## Query and manipulate data by using the Entity Framework.

<ul>
<li>Query, update, and delete data by using DbContext
<ul>
<li>[http://msdn.microsoft.com/en-us/data/gg192989.aspx](http://msdn.microsoft.com/en-us/data/gg192989.aspx)</li>
</ul>
</li>
<li>Build a query that uses deferred execution
<ul>
<li>[http://msdn.microsoft.com/en-us/library/bb738633.aspx](http://msdn.microsoft.com/en-us/library/bb738633.aspx)</li>
</ul>
</li>
<li>Implement lazy loading and eager loading
<ul>
<li>[http://www.levibotelho.com/?p=152](http://www.levibotelho.com/?p=152)</li>
<li>[http://msdn.microsoft.com/en-ca/data/jj574232](http://msdn.microsoft.com/en-ca/data/jj574232)</li>
</ul>
</li>
<li>Create and run compiled queries
<ul>
<li>[http://msdn.microsoft.com/en-us/library/bb896297.aspx](http://msdn.microsoft.com/en-us/library/bb896297.aspx)</li>
</ul>
</li>
<li>Query data by using Entity SQL
<ul>
<li>[http://msdn.microsoft.com/en-us/library/bb387145.aspx](http://msdn.microsoft.com/en-us/library/bb387145.aspx)</li>
<li>[http://msdn.microsoft.com/en-us/library/bb738573.aspx](http://msdn.microsoft.com/en-us/library/bb738573.aspx)</li>
</ul>
</li>
</ul>
## Query and manipulate data by using Data Provider for Entity Framework

<ul>
<li>Query and manipulate data by using Connection, DataReader, Command from the System.Data.EntityClient namespace
<ul>
<li>[http://msdn.microsoft.com/en-us/library/bb738533.aspx](http://msdn.microsoft.com/en-us/library/bb738533.aspx)</li>
<li>[http://msdn.microsoft.com/en-us/library/bb738561.aspx](http://msdn.microsoft.com/en-us/library/bb738561.aspx)</li>
</ul>
</li>
<li>Perform synchronous and asynchronous operations
<ul>
<li>[http://msdn.microsoft.com/en-us/library/ca56w9se.aspx](http://msdn.microsoft.com/en-us/library/ca56w9se.aspx)</li>
<li>[http://msdn.microsoft.com/en-us/library/1a674khd.aspx](http://msdn.microsoft.com/en-us/library/1a674khd.aspx)</li>
<li>[http://msdn.microsoft.com/en-us/library/x76a3b72.aspx](http://msdn.microsoft.com/en-us/library/x76a3b72.aspx)</li>
<li>[http://msdn.microsoft.com/en-us/library/system.data.sqlclient.sqlcommand.endexecutenonquery.aspx](http://msdn.microsoft.com/en-us/library/system.data.sqlclient.sqlcommand.endexecutenonquery.aspx)</li>
<li>[http://msdn.microsoft.com/en-us/library/system.data.sqlclient.sqlcommand.endexecutereader.aspx](http://msdn.microsoft.com/en-us/library/system.data.sqlclient.sqlcommand.endexecutereader.aspx)</li>
<li>[http://msdn.microsoft.com/en-us/library/system.data.sqlclient.sqlcommand.endexecutexmlreader.aspx](http://msdn.microsoft.com/en-us/library/system.data.sqlclient.sqlcommand.endexecutexmlreader.aspx)</li>
</ul>
</li>
<li>Manage transactions (API)</li>
</ul>
## Query data by using LINQ to Entities.

<ul>
<li>Query data by using LINQ operators (for example, project, skip, aggregate, filter, and join)
<ul>
<li>[http://msdn.microsoft.com/en-us/library/bb399367.aspx](http://msdn.microsoft.com/en-us/library/bb399367.aspx)</li>
</ul>
</li>
<li>Log queries
<ul>
<li>[http://msdn.microsoft.com/en-us/magazine/gg490349.aspx](http://msdn.microsoft.com/en-us/magazine/gg490349.aspx)</li>
</ul>
</li>
<li>Implement query boundaries (IQueryable vs. IEnumerable)
<ul>
<li>[http://stackoverflow.com/a/2876655/1068266](http://stackoverflow.com/a/2876655/1068266)</li>
</ul>
</li>
</ul>
## Query and manipulate data by using ADO.NET

<ul>
<li>Query and manipulate data by using Connection, DataReader, Command, DataAdapter, DataSet
<ul>
<li>[http://www.codeproject.com/Articles/361579/A-Beginners-Tutorial-for-Understanding-ADO-NET](http://www.codeproject.com/Articles/361579/A-Beginners-Tutorial-for-Understanding-ADO-NET)</li>
<li>[http://www.agilechai.com/content/archive/2007/06/09/how-to-use-sqldatareader-plus-source-code.aspx](http://www.agilechai.com/content/archive/2007/06/09/how-to-use-sqldatareader-plus-source-code.aspx)</li>
</ul>
</li>
<li>Perform synchronous and asynchronous operations
<ul>
<li>[http://msdn.microsoft.com/en-us/library/zw97wx20.aspx](http://msdn.microsoft.com/en-us/library/zw97wx20.aspx)</li>
<li>[http://msdn.microsoft.com/en-us/library/ca56w9se.aspx](http://msdn.microsoft.com/en-us/library/ca56w9se.aspx)</li>
<li>[http://msdn.microsoft.com/en-us/library/1a674khd.aspx](http://msdn.microsoft.com/en-us/library/1a674khd.aspx)</li>
<li>[http://msdn.microsoft.com/en-us/library/x76a3b72.aspx](http://msdn.microsoft.com/en-us/library/x76a3b72.aspx)</li>
</ul>
</li>
<li>Manage transactions (API)</li>
</ul>
## Create an Entity Framework data model

<ul>
<li>Structure the data model using Table per type, table per class, table per hierarchy
<ul>
<li>[http://weblogs.asp.net/manavi/archive/2010/12/24/inheritance-mapping-strategies-with-entity-framework-code-first-ctp5-part-1-table-per-hierarchy-tph.aspx](http://weblogs.asp.net/manavi/archive/2010/12/24/inheritance-mapping-strategies-with-entity-framework-code-first-ctp5-part-1-table-per-hierarchy-tph.aspx)</li>
<li>[http://weblogs.asp.net/manavi/archive/2010/12/28/inheritance-mapping-strategies-with-entity-framework-code-first-ctp5-part-2-table-per-type-tpt.aspx](http://weblogs.asp.net/manavi/archive/2010/12/28/inheritance-mapping-strategies-with-entity-framework-code-first-ctp5-part-2-table-per-type-tpt.aspx)</li>
<li>[http://weblogs.asp.net/manavi/archive/2011/01/03/inheritance-mapping-strategies-with-entity-framework-code-first-ctp5-part-3-table-per-concrete-type-tpc-and-choosing-strategy-guidelines.aspx](http://weblogs.asp.net/manavi/archive/2011/01/03/inheritance-mapping-strategies-with-entity-framework-code-first-ctp5-part-3-table-per-concrete-type-tpc-and-choosing-strategy-guidelines.aspx)</li>
</ul>
</li>
<li>Choose and implement an approach to manage a data model (code first vs. model first vs. database first)
<ul>
<li>[http://msdn.microsoft.com/en-ca/data/jj193542](http://msdn.microsoft.com/en-ca/data/jj193542)</li>
<li>[http://msdn.microsoft.com/en-ca/data/jj200620](http://msdn.microsoft.com/en-ca/data/jj200620)</li>
<li>[http://msdn.microsoft.com/en-ca/data/jj205424](http://msdn.microsoft.com/en-ca/data/jj205424)</li>
<li>[http://msdn.microsoft.com/en-ca/data/jj206878](http://msdn.microsoft.com/en-ca/data/jj206878)</li>
</ul>
</li>
<li>Implement POCO objects</li>
<li>Describe a data model by using conceptual schema definitions, storage schema definition, and mapping language (CSDL, SSDL, MSL)
<ul>
<li>[http://msdn.microsoft.com/en-us/data/jj652004](http://msdn.microsoft.com/en-us/data/jj652004)</li>
<li>[http://msdn.microsoft.com/en-us/data/jj652016](http://msdn.microsoft.com/en-us/data/jj652016)</li>
<li>[http://msdn.microsoft.com/en-us/data/jj652027](http://msdn.microsoft.com/en-us/data/jj652027)</li>
</ul>
</li>
</ul>
# Designing and Implementing WCF Services (19%)

## Create a WCF service

<ul>
<li>[http://msdn.microsoft.com/en-us/magazine/cc163447.aspx](http://msdn.microsoft.com/en-us/magazine/cc163447.aspx)</li>
<li>Create contracts (service, data, message, callback, and fault)
<ul>
<li>[http://msdn.microsoft.com/en-us/library/ms733070.aspx](http://msdn.microsoft.com/en-us/library/ms733070.aspx)</li>
<li>[http://msdn.microsoft.com/en-us/library/ee960161.aspx](http://msdn.microsoft.com/en-us/library/ee960161.aspx)</li>
<li>[http://msdn.microsoft.com/en-us/library/ms733127.aspx](http://msdn.microsoft.com/en-us/library/ms733127.aspx)</li>
<li>[http://stackoverflow.com/questions/4731987/datacontract-and-servicecontract-difference](http://stackoverflow.com/questions/4731987/datacontract-and-servicecontract-difference)</li>
<li>[http://msdn.microsoft.com/en-us/library/ee960168.aspx](http://msdn.microsoft.com/en-us/library/ee960168.aspx)</li>
<li>[http://msdn.microsoft.com/en-us/library/ms730255.aspx](http://msdn.microsoft.com/en-us/library/ms730255.aspx)</li>
<li>[http://msdn.microsoft.com/en-us/library/ms731064.aspx](http://msdn.microsoft.com/en-us/library/ms731064.aspx)</li>
<li>[http://msdn.microsoft.com/en-us/library/ms752208.aspx](http://msdn.microsoft.com/en-us/library/ms752208.aspx)</li>
</ul>
</li>
<li>Implement message inspectors
<ul>
<li>[http://msdn.microsoft.com/en-us/library/aa717047.aspx](http://msdn.microsoft.com/en-us/library/aa717047.aspx)</li>
<li>[http://blogs.msdn.com/b/carlosfigueira/archive/2011/04/19/wcf-extensibility-message-inspectors.aspx](http://blogs.msdn.com/b/carlosfigueira/archive/2011/04/19/wcf-extensibility-message-inspectors.aspx)</li>
</ul>
</li>
<li>Implement asynchronous operations in the service
<ul>
<li>[http://msdn.microsoft.com/en-us/library/ms734701.aspx](http://msdn.microsoft.com/en-us/library/ms734701.aspx)</li>
<li>[http://msdn.microsoft.com/en-us/library/ms731177.aspx](http://sdn.microsoft.com/en-us/library/ms731177.aspx)</li>
<li>[http://msdn.microsoft.com/en-us/library/ms730059.aspx](http://msdn.microsoft.com/en-us/library/ms730059.aspx)</li>
</ul>
</li>
</ul>
## Configure WCF services by using configuration settings

<ul>
<li>Configure service behaviors
<ul>
<li>[http://msdn.microsoft.com/en-us/library/ms730137.aspx](http://msdn.microsoft.com/en-us/library/ms730137.aspx)</li>
<li>[http://msdn.microsoft.com/en-us/magazine/cc163302.aspx](http://msdn.microsoft.com/en-us/magazine/cc163302.aspx)</li>
</ul>
</li>
<li>Configure service endpoints
<ul>
<li>[http://msdn.microsoft.com/en-us/library/ms734786.aspx](http://msdn.microsoft.com/en-us/library/ms734786.aspx)</li>
</ul>
</li>
<li>Configure bindings
<ul>
<li>[http://msdn.microsoft.com/en-us/magazine/cc163394.aspx](http://msdn.microsoft.com/en-us/magazine/cc163394.aspx)</li>
<li>[http://msdn.microsoft.com/en-us/library/ms734662.aspx](http://msdn.microsoft.com/en-us/library/ms734662.aspx)</li>
<li>[http://msdn.microsoft.com/en-us/library/ms733051.aspx](http://msdn.microsoft.com/en-us/library/ms733051.aspx)</li>
<li>[http://msdn.microsoft.com/en-us/library/ms731144.aspx](http://msdn.microsoft.com/en-us/library/ms731144.aspx)</li>
</ul>
</li>
<li>Specify a service contract</li>
<li>Expose service metadata (XSDs, WSDL and metadata exchange endpoint)
<ul>
<li>[http://msdn.microsoft.com/en-us/library/ms734765.aspx](http://msdn.microsoft.com/en-us/library/ms734765.aspx)</li>
</ul>
</li>
</ul>
## Configure WCF services by using the API

<ul>
<li>Configure service behaviors
<ul>
<li>[http://msdn.microsoft.com/en-us/library/aa702788.aspx](http://msdn.microsoft.com/en-us/library/aa702788.aspx)</li>
</ul>
</li>
<li>Configure service endpoints
<ul>
<li>[http://msdn.microsoft.com/en-us/library/ms731080.aspx](http://msdn.microsoft.com/en-us/library/ms731080.aspx)</li>
</ul>
</li>
<li>Configure bindings
<ul>
<li>[http://msdn.microsoft.com/en-us/library/ms731833.aspx](http://msdn.microsoft.com/en-us/library/ms731833.aspx)</li>
<li>[http://msdn.microsoft.com/en-us/library/ms731862.aspx](http://msdn.microsoft.com/en-us/library/ms731862.aspx)</li>
</ul>
</li>
<li>Specify a service contract</li>
<li>Expose service metadata (XSDs, WSDL and metadata exchange endpoint)
<ul>
<li>[http://msdn.microsoft.com/en-us/library/aa738489.aspx](http://msdn.microsoft.com/en-us/library/aa738489.aspx)</li>
</ul>
</li>
<li>WCF routing and discovery features
<ul>
<li>[http://msdn.microsoft.com/en-us/library/ee517422.aspx](http://msdn.microsoft.com/en-us/library/ee517422.aspx)</li>
<li>[http://weblogs.asp.net/gsusx/archive/2009/02/13/using-ws-discovery-in-wcf-4-0.aspx](http://weblogs.asp.net/gsusx/archive/2009/02/13/using-ws-discovery-in-wcf-4-0.aspx)</li>
<li>[http://msdn.microsoft.com/en-us/library/dd456791.aspx](http://msdn.microsoft.com/en-us/library/dd456791.aspx)</li>
</ul>
</li>
</ul>
## Secure a WCF service

<ul>
<li>Implement message level security
<ul>
<li>[http://msdn.microsoft.com/en-us/library/ms733137.aspx](http://msdn.microsoft.com/en-us/library/ms733137.aspx)</li>
<li>[http://msdn.microsoft.com/en-us/library/ff648863.aspx](http://msdn.microsoft.com/en-us/library/ff648863.aspx)</li>
</ul>
</li>
<li>Implement transport level security
<ul>
<li>[http://msdn.microsoft.com/en-us/library/ms729700.aspx](http://msdn.microsoft.com/en-us/library/ms729700.aspx)</li>
</ul>
</li>
<li>Implement certificates
<ul>
<li>[http://msdn.microsoft.com/en-us/library/ms731899.aspx](http://msdn.microsoft.com/en-us/library/ms731899.aspx)</li>
<li>[http://msdn.microsoft.com/en-us/library/ff648360.aspx](http://msdn.microsoft.com/en-us/library/ff648360.aspx)</li>
<li>[http://msdn.microsoft.com/en-us/library/ff650785.aspx](http://msdn.microsoft.com/en-us/library/ff650785.aspx)</li>
</ul>
</li>
</ul>
## Consume WCF services

<ul>
<li>Generate proxies by using SvcUtil;
<ul>
<li>[http://msdn.microsoft.com/en-us/library/aa347733.aspx](http://msdn.microsoft.com/en-us/library/aa347733.aspx)</li>
</ul>
</li>
<li>Generate proxies by creating a service reference;
<ul>
<li>[http://msdn.microsoft.com/en-us/library/cc636424%28v=ax.50%29.aspx](http://msdn.microsoft.com/en-us/library/cc636424%28v=ax.50%29.aspx)</li>
</ul>
</li>
<li>Create and implement channel factories
<ul>
<li>[http://msdn.microsoft.com/en-us/library/ms734681.aspx](http://msdn.microsoft.com/en-us/library/ms734681.aspx)</li>
</ul>
</li>
</ul>
## Version a WCF service

<ul>
<li>This objective may include but is not limited to: Version different types of contracts (message, service, data)
<ul>
<li>[http://stackoverflow.com/a/7560192/1068266](http://stackoverflow.com/a/7560192/1068266)</li>
<li>[http://msdn.microsoft.com/en-us/library/ms731138.aspx](http://msdn.microsoft.com/en-us/library/ms731138.aspx)</li>
<li>[http://msdn.microsoft.com/en-us/library/ee816862.aspx](http://msdn.microsoft.com/en-us/library/ee816862.aspx)</li>
</ul>
</li>
<li>Configure address, binding, and routing service versioning
<ul>
<li>[http://msdn.microsoft.com/en-us/library/ms731060.aspx](http://msdn.microsoft.com/en-us/library/ms731060.aspx)</li>
</ul>
</li>
</ul>
## Create and configure a WCF service on Windows Azure

<ul>
<li>Create and configure bindings for WCF services (Azure SDK-- extensions to WCF)
<ul>
<li>[http://msdn.microsoft.com/en-us/library/windowsazure/hh410102.aspx](http://msdn.microsoft.com/en-us/library/windowsazure/hh410102.aspx)</li>
</ul>
</li>
<li>Relay bindings to Azure using service bus endpoints
<ul>
<li>[http://msdn.microsoft.com/en-us/library/windowsazure/hh966775.aspx](http://msdn.microsoft.com/en-us/library/windowsazure/hh966775.aspx)</li>
</ul>
</li>
<li>Integrate with the Azure service bus relay
<ul>
<li>[http://msdn.microsoft.com/en-us/library/windowsazure/hh367519.aspx](http://msdn.microsoft.com/en-us/library/windowsazure/hh367519.aspx)</li>
<li>[http://msdn.microsoft.com/en-us/library/windowsazure/jj860549.aspx](http://msdn.microsoft.com/en-us/library/windowsazure/jj860549.aspx)</li>
</ul>
</li>
</ul>
## Implement messaging patterns

<ul>
<li>Implement one way, request/reply, streaming, and duplex communication
<ul>
<li>[http://msdn.microsoft.com/en-us/library/ee960162.aspx](http://msdn.microsoft.com/en-us/library/ee960162.aspx)</li>
<li>[http://msdn.microsoft.com/en-us/library/ms733742.aspx](http://msdn.microsoft.com/en-us/library/ms733742.aspx)</li>
<li>[http://msdn.microsoft.com/en-us/library/ms789010.aspx](http://msdn.microsoft.com/en-us/library/ms789010.aspx)</li>
</ul>
</li>
<li>Implement Windows Azure Service Bus and Windows Azure Queues
<ul>
<li>[http://www.windowsazure.com/en-us/manage/services/service-bus/](http://www.windowsazure.com/en-us/manage/services/service-bus/)</li>
<li>[http://www.windowsazure.com/en-us/develop/net/how-to-guides/service-bus-queues/](http://www.windowsazure.com/en-us/develop/net/how-to-guides/service-bus-queues/)</li>
</ul>
</li>
</ul>
## Host and manage services

<ul>
<li>Manage services concurrency (single, multiple, reentrant)
<ul>
<li>[http://www.codeproject.com/Articles/89858/WCF-Concurrency-Single-Multiple-and-Reentrant-and](http://www.codeproject.com/Articles/89858/WCF-Concurrency-Single-Multiple-and-Reentrant-and)</li>
</ul>
</li>
<li>Create service hosts
<ul>
<li>[http://msdn.microsoft.com/en-us/library/ms731758.aspx](http://msdn.microsoft.com/en-us/library/ms731758.aspx)</li>
</ul>
</li>
<li>Choose a hosting mechanism
<ul>
<li>[http://msdn.microsoft.com/en-us/library/ms730158.aspx](http://msdn.microsoft.com/en-us/library/ms730158.aspx)</li>
</ul>
</li>
<li>Choose an instancing mode (per call, per session, singleton)
<ul>
<li>[http://www.codeproject.com/Articles/86007/3-ways-to-do-WCF-instance-management-Per-call-Per](http://www.codeproject.com/Articles/86007/3-ways-to-do-WCF-instance-management-Per-call-Per)</li>
</ul>
</li>
<li>Activate and manage a service by using AppFabric
<ul>
<li>[http://www.dotnetcurry.com/ShowArticle.aspx?ID=771](http://www.dotnetcurry.com/ShowArticle.aspx?ID=771)</li>
</ul>
</li>
<li>Implement transactional services
<ul>
<li>[http://msdn.microsoft.com/en-us/library/ff384250.aspx](http://msdn.microsoft.com/en-us/library/ff384250.aspx)</li>
</ul>
</li>
<li>Host services in an Windows Azure worker role
<ul>
<li>[http://code.msdn.microsoft.com/windowsazure/CSAzureWCFWorkerRole-38b4e51d](http://code.msdn.microsoft.com/windowsazure/CSAzureWCFWorkerRole-38b4e51d)</li>
</ul>
</li>
</ul>
# Creating and Consuming Web API-based services (18%)

## Design a Web API

<ul>
<li>Define HTTP resources with HTTP actions
<ul>
<li>[http://www.codeproject.com/Articles/549152/Introduction-to-ASP-NET-Web-API](http://www.codeproject.com/Articles/549152/Introduction-to-ASP-NET-Web-API)</li>
</ul>
</li>
<li>Plan appropriate URI space, and map URI space using routing
<ul>
<li>[http://www.asp.net/web-api/overview/web-api-routing-and-actions/routing-in-aspnet-web-api](http://www.asp.net/web-api/overview/web-api-routing-and-actions/routing-in-aspnet-web-api)</li>
</ul>
</li>
<li>Choose appropriate HTTP method (get, put, post, delete) to meet requirements
<ul>
<li>[https://en.wikipedia.org/wiki/Representational_state_transfer#RESTful_web_APIs](https://en.wikipedia.org/wiki/Representational_state_transfer#RESTful_web_APIs)</li>
</ul>
</li>
<li>Choose appropriate format (Web API formats) for responses to meet requirements
<ul>
<li>[http://www.asp.net/web-api/overview/formats-and-model-binding/json-and-xml-serialization](http://www.asp.net/web-api/overview/formats-and-model-binding/json-and-xml-serialization)</li>
</ul>
</li>
<li>Plan when to make HTTP actions asynchronous
<ul>
<li>[http://blogs.msdn.com/b/henrikn/archive/2012/02/24/async-actions-in-asp-net-web-api.aspx](http://blogs.msdn.com/b/henrikn/archive/2012/02/24/async-actions-in-asp-net-web-api.aspx)</li>
</ul>
</li>
</ul>
## Implement a Web API

<ul>
<li>Accept data in JSON format (in JavaScript, in an AJAX callback)</li>
<li>Use content negotiation to deliver different data formats to clients
<ul>
<li>[http://www.asp.net/web-api/overview/formats-and-model-binding/content-negotiation](http://www.asp.net/web-api/overview/formats-and-model-binding/content-negotiation)</li>
</ul>
</li>
<li>Define actions and parameters to handle data binding
<ul>
<li>[http://blogs.msdn.com/b/jmstall/archive/2012/04/16/how-webapi-does-parameter-binding.aspx](http://blogs.msdn.com/b/jmstall/archive/2012/04/16/how-webapi-does-parameter-binding.aspx)</li>
</ul>
</li>
<li>Use HttpMessageHandler to process client requests and server responses
<ul>
<li>[http://www.asp.net/web-api/overview/working-with-http/http-message-handlers](http://www.asp.net/web-api/overview/working-with-http/http-message-handlers)</li>
</ul>
</li>
<li>Implement dependency injection, along with the dependency resolver, to create more flexible applications
<ul>
<li>[http://www.asp.net/web-api/overview/extensibility/using-the-web-api-dependency-resolver](http://www.asp.net/web-api/overview/extensibility/using-the-web-api-dependency-resolver)</li>
</ul>
</li>
<li>Implement action filters and exception filters to manage controller execution
<ul>
<li>[http://www.asp.net/web-api/overview/web-api-routing-and-actions/exception-handling](http://www.asp.net/web-api/overview/web-api-routing-and-actions/exception-handling)</li>
</ul>
</li>
<li>Implement asynchronous and synchronous actions
<ul>
<li>[http://www.asp.net/mvc/tutorials/mvc-4/using-asynchronous-methods-in-aspnet-mvc-4](http://www.asp.net/mvc/tutorials/mvc-4/using-asynchronous-methods-in-aspnet-mvc-4)</li>
</ul>
</li>
<li>Implement streaming actions
<ul>
<li>[http://www.strathweb.com/2013/01/asynchronously-streaming-video-with-asp-net-web-api/](http://www.strathweb.com/2013/01/asynchronously-streaming-video-with-asp-net-web-api/)</li>
</ul>
</li>
</ul>
## Secure a Web API

<ul>
<li>Implement HTTPBasic authentication over SSL
<ul>
<li>[http://www.asp.net/web-api/overview/security/basic-authentication](http://www.asp.net/web-api/overview/security/basic-authentication)</li>
<li>[http://www.asp.net/web-api/overview/security/working-with-ssl-in-web-api](http://www.asp.net/web-api/overview/security/working-with-ssl-in-web-api)</li>
</ul>
</li>
<li>Implement Windows Auth
<ul>
<li>[http://www.asp.net/web-api/overview/security/integrated-windows-authentication](http://www.asp.net/web-api/overview/security/integrated-windows-authentication)</li>
</ul>
</li>
<li>Enable cross-domain requests
<ul>
<li>[http://blogs.msdn.com/b/carlosfigueira/archive/2012/02/20/implementing-cors-support-in-asp-net-web-apis.aspx](http://blogs.msdn.com/b/carlosfigueira/archive/2012/02/20/implementing-cors-support-in-asp-net-web-apis.aspx)</li>
</ul>
</li>
<li>Prevent cross-site request forgery (XSRF)
<ul>
<li>[http://www.asp.net/web-api/overview/security/preventing-cross-site-request-forgery-%28csrf%29-attacks](http://www.asp.net/web-api/overview/security/preventing-cross-site-request-forgery-%28csrf%29-attacks)</li>
</ul>
</li>
<li>Implement, and extend, authorization filters to control access to the application
<ul>
<li>[http://www.asp.net/web-api/overview/security/authentication-and-authorization-in-aspnet-web-api](http://www.asp.net/web-api/overview/security/authentication-and-authorization-in-aspnet-web-api)</li>
</ul>
</li>
</ul>
## Host and manage Web API

<ul>
<li>Self-host a Web API in your own process (a Windows service)
<ul>
<li>[http://www.asp.net/web-api/overview/hosting-aspnet-web-api/self-host-a-web-api](http://www.asp.net/web-api/overview/hosting-aspnet-web-api/self-host-a-web-api)</li>
</ul>
</li>
<li>Host Web API in an ASP.NET app</li>
<li>Host services in a Windows Azure worker role
<ul>
<li>[http://www.dotnetglobe.com/2012/04/hosting-aspnet-web-api-on-windows-azure.html](http://www.dotnetglobe.com/2012/04/hosting-aspnet-web-api-on-windows-azure.html)</li>
</ul>
</li>
<li>Restricting message size
<ul>
<li>[http://stackoverflow.com/q/9453738/1068266](http://stackoverflow.com/q/9453738/1068266)</li>
</ul>
</li>
<li>Configure the host server for streaming
<ul>
<li>[http://www.strathweb.com/2012/09/dealing-with-large-files-in-asp-net-web-api/](http://www.strathweb.com/2012/09/dealing-with-large-files-in-asp-net-web-api/)</li>
</ul>
</li>
</ul>
## Consume Web API web services

<ul>
<li>Consume Web API services by using HttpClient synchronously and asynchronously
<ul>
<li>[http://blogs.msdn.com/b/henrikn/archive/2012/02/16/httpclient-is-here.aspx](http://blogs.msdn.com/b/henrikn/archive/2012/02/16/httpclient-is-here.aspx)</li>
</ul>
</li>
<li>Send and receive requests in different formats (JSON/HTML/etc.)
<ul>
<li>[http://www.levibotelho.com/?p=172](http://www.levibotelho.com/?p=172)</li>
<li>[http://stackoverflow.com/q/10679214/1068266](http://stackoverflow.com/q/10679214/1068266)</li>
<li>[http://msdn.microsoft.com/en-us/library/hh944339%28v=vs.108%29.aspx](http://msdn.microsoft.com/en-us/library/hh944339%28v=vs.108%29.aspx)</li>
<li>[http://stackoverflow.com/q/6117101/1068266](http://stackoverflow.com/q/6117101/1068266)</li>
</ul>
</li>
</ul>
# Deploying Web Applications and Services (19%)

## Design a deployment strategy

<ul>
<li>Create an IIS install package
<ul>
<li>[http://weblogs.asp.net/scottgu/archive/2010/09/13/automating-deployment-with-microsoft-web-deploy.aspx](http://weblogs.asp.net/scottgu/archive/2010/09/13/automating-deployment-with-microsoft-web-deploy.aspx)</li>
<li>[http://msdn.microsoft.com/en-us/library/dd465323.aspx](http://msdn.microsoft.com/en-us/library/dd465323.aspx)</li>
</ul>
</li>
<li>Deploy to web farms
<ul>
<li>[http://weblogs.asp.net/scottgu/archive/2010/09/08/introducing-the-microsoft-web-farm-framework.aspx](http://weblogs.asp.net/scottgu/archive/2010/09/08/introducing-the-microsoft-web-farm-framework.aspx)</li>
<li>[http://msdn.microsoft.com/en-us/library/ff731049%28v=azure.10%29.aspx](http://msdn.microsoft.com/en-us/library/ff731049%28v=azure.10%29.aspx)</li>
<li>[http://raquila.com/software/ms-deploy-basics/](http://raquila.com/software/ms-deploy-basics/)</li>
</ul>
</li>
<li>Deploy a web application by using XCopy
<ul>
<li>[http://help.infragistics.com/Help/NetAdvantage/WinForms/2012.2/CLR4.0/html/Win_Using_XCOPY_Deployment.html](http://help.infragistics.com/Help/NetAdvantage/WinForms/2012.2/CLR4.0/html/Win_Using_XCOPY_Deployment.html)</li>
<li>[http://msdn.microsoft.com/en-us/library/aa302347.aspx](http://msdn.microsoft.com/en-us/library/aa302347.aspx)</li>
</ul>
</li>
<li>Automate a deployment from TFS or Build Server
<ul>
<li>[http://codingcraft.wordpress.com/2012/02/18/automated-deployment-with-tfs/](http://codingcraft.wordpress.com/2012/02/18/automated-deployment-with-tfs/)</li>
<li>[http://msdn.microsoft.com/en-us/library/vstudio/ms181709.aspx](http://msdn.microsoft.com/en-us/library/vstudio/ms181709.aspx)</li>
</ul>
</li>
</ul>
## Choose a deployment strategy for a Windows Azure web application

<ul>
<li>Perform an in-place upgrade and VIP swap
<ul>
<li>[http://www.windowsazure.com/en-us/develop/net/common-tasks/staging-deployment/?bcsi-ac-bbaf765720ef3335=20190C6B00000503aHYllMrACWM2RwoWOHeWxZgS+FG7BgAAAwUAAJMKZQAIBwAAAgEAAKXSEgA](http://www.windowsazure.com/en-us/develop/net/common-tasks/staging-deployment/?bcsi-ac-bbaf765720ef3335=20190C6B00000503aHYllMrACWM2RwoWOHeWxZgS+FG7BgAAAwUAAJMKZQAIBwAAAgEAAKXSEgA)</li>
</ul>
</li>
<li>Configure an upgrade domain
<ul>
<li>[http://msdn.microsoft.com/en-us/library/windowsazure/hh472157.aspx](http://msdn.microsoft.com/en-us/library/windowsazure/hh472157.aspx)</li>
<li>[http://msdn.microsoft.com/en-us/library/windowsazure/ee758711.aspx](http://msdn.microsoft.com/en-us/library/windowsazure/ee758711.aspx)</li>
</ul>
</li>
<li>Create and configure input and internal endpoints
<ul>
<li>[http://msdn.microsoft.com/en-us/library/windowsazure/hh180158.aspx](http://msdn.microsoft.com/en-us/library/windowsazure/hh180158.aspx)</li>
<li>[http://msdn.microsoft.com/en-us/library/windowsazure/gg433020.aspx](http://msdn.microsoft.com/en-us/library/windowsazure/gg433020.aspx)</li>
<li>[http://msdn.microsoft.com/en-us/library/windowsazure/gg432980.aspx](http://msdn.microsoft.com/en-us/library/windowsazure/gg432980.aspx)</li>
</ul>
</li>
<li>Specify operating system configuration
<ul>
<li>[http://msdn.microsoft.com/en-us/library/windowsazure/ee758710.aspx](http://msdn.microsoft.com/en-us/library/windowsazure/ee758710.aspx)</li>
<li>[http://www.windowsazure.com/en-us/manage/services/cloud-services/how-to-configure-a-cloud-service/](http://www.windowsazure.com/en-us/manage/services/cloud-services/how-to-configure-a-cloud-service/)</li>
</ul>
</li>
</ul>
## Configure a web application for deployment

<ul>
<li>Switch from production/release mode to debug mode
<ul>
<li>[http://msdn.microsoft.com/en-us/library/e8z01xdh%28v=vs.80%29.aspx](http://msdn.microsoft.com/en-us/library/e8z01xdh%28v=vs.80%29.aspx)</li>
</ul>
</li>
<li>Use SetParameters to set up an IIS app pool, set permissions and passwords
<ul>
<li>[http://msdn.microsoft.com/en-us/library/ff398068.aspx](http://msdn.microsoft.com/en-us/library/ff398068.aspx)</li>
</ul>
</li>
<li>Configure WCF endpoints, bindings, and behaviors</li>
<li>Transform web.config by using XSLT (for example, across development, test, and production/release environments)
<ul>
<li>[http://msdn.microsoft.com/en-us/library/dd465326(v=vs.100).aspx](http://msdn.microsoft.com/en-us/library/dd465326(v=vs.100).aspx)</li>
</ul>
</li>
<li>Configure Azure configuration settings
<ul>
<li>[http://msdn.microsoft.com/en-us/library/windowsazure/ee758710.aspx](http://msdn.microsoft.com/en-us/library/windowsazure/ee758710.aspx)</li>
</ul>
</li>
</ul>
## Manage packages by using NuGet

<ul>
<li>Create and configure a NuGet package
<ul>
<li>[http://www.hanselman.com/blog/CreatingANuGetPackageIn7EasyStepsPlusUsingNuGetToIntegrateASPNETMVC3IntoExistingWebFormsApplications.aspx](http://www.hanselman.com/blog/CreatingANuGetPackageIn7EasyStepsPlusUsingNuGetToIntegrateASPNETMVC3IntoExistingWebFormsApplications.aspx)</li>
</ul>
</li>
<li>Install and update an existing NuGet package
<ul>
<li>[http://docs.nuget.org/docs/start-here/installing-nuget](http://docs.nuget.org/docs/start-here/installing-nuget)</li>
</ul>
</li>
<li>Connect to a local repository cache for NuGet, set up your own package repository
<ul>
<li>[http://docs.nuget.org/docs/creating-packages/hosting-your-own-nuget-feeds](http://docs.nuget.org/docs/creating-packages/hosting-your-own-nuget-feeds)</li>
</ul>
</li>
</ul>
## Create, configure, and publish a web package

<ul>
<li>Create an IIS InstallPackage
<ul>
<li>[http://www.iis.net/learn/publish/using-web-deploy/building-a-web-deploy-package-from-visual-studio-2010](http://www.iis.net/learn/publish/using-web-deploy/building-a-web-deploy-package-from-visual-studio-2010)</li>
</ul>
</li>
<li>Configure the build process to output a web package
<ul>
<li>[http://blogs.msdn.com/b/webdev/archive/2009/02/24/web-packaging-creating-web-packages-using-msbuild.aspx](http://blogs.msdn.com/b/webdev/archive/2009/02/24/web-packaging-creating-web-packages-using-msbuild.aspx)</li>
</ul>
</li>
<li>Apply pre- and post- condition actions to ensure that transformations are correctly applied
<ul>
<li>[http://msdn.microsoft.com/en-us/library/ke5z92ks.aspx](http://msdn.microsoft.com/en-us/library/ke5z92ks.aspx)</li>
</ul>
</li>
<li>Include appropriate assets (web content, certificates)
<ul>
<li>[http://sedodream.com/2010/05/01/WebDeploymentToolMSDeployBuildPackageIncludingExtraFilesOrExcludingSpecificFiles.aspx](http://sedodream.com/2010/05/01/WebDeploymentToolMSDeployBuildPackageIncludingExtraFilesOrExcludingSpecificFiles.aspx)</li>
</ul>
</li>
</ul>
## Share assemblies between multiple applications and servers

<ul>
<li>Prepare the environment for use of assemblies across multiple servers (interning)
<ul>
<li>[http://blogs.technet.com/b/sateesh-arveti/archive/2011/11/30/look-at-sharing-common-assemblies-in-asp-net-4-5.aspx](http://blogs.technet.com/b/sateesh-arveti/archive/2011/11/30/look-at-sharing-common-assemblies-in-asp-net-4-5.aspx)</li>
</ul>
</li>
<li>Sign assemblies by using a strong name
<ul>
<li>[http://msdn.microsoft.com/en-us/library/xc31ft41.aspx](http://msdn.microsoft.com/en-us/library/xc31ft41.aspx)</li>
<li>[http://msdn.microsoft.com/en-us/library/xwb8f617.aspx](http://msdn.microsoft.com/en-us/library/xwb8f617.aspx)</li>
</ul>
</li>
<li>Deploy assemblies to the global assembly cache
<ul>
<li>[http://msdn.microsoft.com/en-us/library/dkkx7f79.aspx](http://msdn.microsoft.com/en-us/library/dkkx7f79.aspx)</li>
<li>[http://msdn.microsoft.com/en-us/library/ex0ss12c.aspx](http://msdn.microsoft.com/en-us/library/ex0ss12c.aspx)</li>
</ul>
</li>
<li>Implement assembly versioning
<ul>
<li>[http://msdn.microsoft.com/en-us/library/51ket42z(v=vs.110).aspx](http://msdn.microsoft.com/en-us/library/51ket42z(v=vs.110).aspx)</li>
</ul>
</li>
<li>Create an assembly manifest
<ul>
<li>[http://msdn.microsoft.com/en-us/library/1w45z383.aspx](http://msdn.microsoft.com/en-us/library/1w45z383.aspx)</li>
<li>[http://msdn.microsoft.com/en-us/library/4w8c1y2s.aspx](http://msdn.microsoft.com/en-us/library/4w8c1y2s.aspx)</li>
</ul>
</li>
<li>Configure assembly binding redirects (for example, from MVC2 to MVC3)
<ul>
<li>[http://msdn.microsoft.com/en-us/library/2fc472t2.aspx](http://msdn.microsoft.com/en-us/library/2fc472t2.aspx)</li>
</ul>
</li>
</ul>
