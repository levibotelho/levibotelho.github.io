---
layout: post
title: StyleCop type members layout for ReSharper 
category: Development
tags: [resharper, stylecop, c#]
comments: true
share: true
---

Resharper's code cleanup (CTRL+E,CTRL+C) has a handy option which reorders your type members for you. However the predefined reordering rules might not suit your needs. If you like StyleCop's stricter ruleset, then this is your lucky day. Go to RESHARPER -> OPTIONS, and then CODE EDITING -> C# -> TYPE MEMBERS LAYOUT. Select the "Custom layout" radio button and paste in the following XML.

{% highlight xml %}
<?xml version="1.0" encoding="utf-8" ?>
<Patterns xmlns="urn:shemas-jetbrains-com:member-reordering-patterns">

  <!--Do not reorder COM interfaces and structs marked by StructLayout attribute-->
  <Pattern>
    <Match>
      <Or Weight="100">
        <And>
          <Kind Is="interface"/>
          <Or>
            <HasAttribute CLRName="System.Runtime.InteropServices.InterfaceTypeAttribute"/>
            <HasAttribute CLRName="System.Runtime.InteropServices.ComImport"/>
          </Or>
        </And>
        <HasAttribute CLRName="System.Runtime.InteropServices.StructLayoutAttribute"/>
      </Or>
    </Match>
  </Pattern>

  <!--Special formatting of NUnit test fixture-->
  <Pattern RemoveAllRegions="true">
    <Match>
      <And Weight="100">
        <Kind Is="class"/>
        <HasAttribute CLRName="NUnit.Framework.TestFixtureAttribute" Inherit="true"/>
      </And>
    </Match>

    <!--Setup/Teardown-->
    <Entry>
      <Match>
        <And>
          <Kind Is="method"/>
          <Or>
            <HasAttribute CLRName="NUnit.Framework.SetUpAttribute" Inherit="true"/>
            <HasAttribute CLRName="NUnit.Framework.TearDownAttribute" Inherit="true"/>
            <HasAttribute CLRName="NUnit.Framework.FixtureSetUpAttribute" Inherit="true"/>
            <HasAttribute CLRName="NUnit.Framework.FixtureTearDownAttribute" Inherit="true"/>
          </Or>
        </And>
      </Match>
    </Entry>

    <!--All other members-->
    <Entry/>

    <!--Test methods-->
    <Entry>
      <Match>
        <And Weight="100">
          <Kind Is="method"/>
          <HasAttribute CLRName="NUnit.Framework.TestAttribute" Inherit="false"/>
        </And>
      </Match>
      <Sort>
        <Name/>
      </Sort>
    </Entry>
  </Pattern>

  <!--Default pattern-->
  <Pattern RemoveAllRegions="true">

    <!-- Constant Fields -->
    <Entry>
      <Match>
        <Kind Is="constant" />
      </Match>
      <Sort>
        <Access />
        <Static />
        <Readonly />
        <Name />
      </Sort>
    </Entry>

    <!-- Fields -->
    <Entry>
      <Match>
        <Kind Is="field" />
      </Match>
      <Sort>
        <Access />
        <Static />
        <Readonly />
        <Name />
      </Sort>
    </Entry>

    <!-- Constructors -->
    <Entry>
      <Match>
        <Kind Is="constructor" />
      </Match>
      <Sort>
        <Access />
        <Static />
        <Readonly />
        <Name />
      </Sort>
    </Entry>

    <!-- Destructors -->
    <Entry>
      <Match>
        <Kind Is="destructor" />
      </Match>
      <Sort>
        <Access />
        <Static />
        <Readonly />
        <Name />
      </Sort>
    </Entry>

    <!-- Delegates -->
    <Entry>
      <Match>
        <Kind Is="delegate" />
      </Match>
      <Sort>
        <Access />
        <Static />
        <Readonly />
        <Name />
      </Sort>
    </Entry>

    <!-- Events -->
    <Entry>
      <Match>
        <Kind Is="event" />
      </Match>
      <Sort>
        <Access />
        <Static />
        <Readonly />
        <Name />
      </Sort>
    </Entry>

    <!-- Enums -->
    <Entry>
      <Match>
        <Kind Is="enum" />
      </Match>
      <Sort>
        <Access />
        <Static />
        <Readonly />
        <Name />
      </Sort>
    </Entry>

    <!-- Interfaces -->
    <Entry>
      <Match>
        <Kind Is="interface" />
      </Match>
      <Sort>
        <Access />
        <Static />
        <Readonly />
        <Name />
      </Sort>
    </Entry>

    <!-- Properties -->
    <Entry>
      <Match>
        <Kind Is="property" />
      </Match>
      <Sort>
        <Access />
        <Static />
        <Readonly />
        <Name />
      </Sort>
    </Entry>

    <!-- Indexers -->
    <Entry>
      <Match>
        <Kind Is="indexer" />
      </Match>
      <Sort>
        <Access />
        <Static />
        <Readonly />
        <Name />
      </Sort>
    </Entry>

    <!-- Operators -->
    <Entry>
      <Match>
        <Kind Is="operator" />
      </Match>
      <Sort>
        <Access />
        <Static />
        <Readonly />
        <Name />
      </Sort>
    </Entry>

    <!-- Methods -->
    <Entry>
      <Match>
        <Kind Is="method" />
      </Match>
      <Sort>
        <Access />
        <Static />
        <Readonly />
        <Name />
      </Sort>
    </Entry>

    <!-- Structs -->
    <Entry>
      <Match>
        <Kind Is="struct" />
      </Match>
      <Sort>
        <Access />
        <Static />
        <Readonly />
        <Name />
      </Sort>
    </Entry>

    <!-- Classes -->
    <Entry>
      <Match>
        <Kind Is="class" />
      </Match>
      <Sort>
        <Access />
        <Static />
        <Readonly />
        <Name />
      </Sort>
    </Entry>

    <!--all other members-->
    <Entry/>

    <!--nested types-->
    <Entry>
      <Match>
        <Kind Is="type"/>
      </Match>
      <Sort>
        <Access />
        <Static />
        <Readonly />
        <Name />
      </Sort>
    </Entry>
  </Pattern>
</Patterns>
{% endhighlight %}

Strictly speaking, there are four differences between this layout definition and StyleCop's.

1. I've kept ReSharper's default [StructLayout] and NUnit test fixtures.
2. Operators are inserted just before Indexers. Operators do not have a predefined position in StyleCop's ruleset.
3. After all StyleCop ordering is complete, equivalent members are ordered alphabetically. If you don't like the alphabetical ordering, simply remove all `<Name />` tags in the `<Sort />` elements.
4. Regions are removed. You can simply remove the `RemoveAllRegions="true"` attribute from the default pattern if you would prefer this not to be the case. 

Enjoy!