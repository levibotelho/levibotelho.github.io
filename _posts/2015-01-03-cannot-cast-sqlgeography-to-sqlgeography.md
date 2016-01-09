---
layout: post
title: Cannot cast SqlGeography to SqlGeography
category: Development
tags: [c#, sql-server, dapper, gis, assemblies]
description: Explains how to resolve the InvalidCastException "Unable to cast object of type 'Microsoft.SqlServer.Types.SqlGeography' to type 'Microsoft.SqlServer.Types.SqlGeography".
comments: true
share: true
---
I was recently writing some C# code that manipulated location data coming out of SQL Server. Specifically, I was writing a Dapper type handler which transformed an instance of `Microsoft.SqlServer.Types.SqlGeography` to a struct which held latitude and longitude values. The method that was causing me problems looked like this.

    public override GeoCoordinate Parse(object value)
    {
        var location = (SqlGeography)value;
        return new GeoCoordinate(location.Lat.Value, location.Long.Value);
    }

The problem that I was having was that even though `value` was an instance of `SqlGeography`, I was consistently encountering the following exception.

> System.InvalidCastException: Unable to cast object of type 'Microsoft.SqlServer.Types.SqlGeography' to type 'Microsoft.SqlServer.Types.SqlGeography'.

After a considerable amount of frustration, I figured out what the problem was. It turns out that `SqlGeography` instance that was being passed to the method by Dapper was coming from version 10.0.0.0 of the `Microsoft.SqlServer.Types` assembly, pulled straight from my machine's GAC, whereas the `SqlGeography` type that I was trying to convert to was coming from version 11.0.0.0 of the assembly that I had referenced in my project via the `Microsoft.SqlServer.Types` NuGet package.

This issue is actually mentioned on MSDN in the article "[Breaking Changes to Database Engine Features in SQL Server 2012](http://msdn.microsoft.com/en-us/library/ms143179(v=sql.110).aspx)", about a third of the way down the page. The solution is to add the following assembly binding redirect to your application's config file.

    <dependentAssembly>
      <assemblyIdentity name="Microsoft.SqlServer.Types" publicKeyToken="89845dcd8080cc91" culture="neutral" />
      <bindingRedirect oldVersion="10.0.0.0" newVersion="11.0.0.0" />
    </dependentAssembly>

With that in place, the CLR correctly uses version 11.0.0.0 of the assembly across the entire application, and everything works as expected.