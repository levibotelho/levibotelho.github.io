---
layout: post
title: ! '[XML Schema] Allow elements to appear any number of times, in any order'
category: Miscellaneous
tags: [xml]
comments: true
share: true
---
It’s not every day that I write XML schema files, but when I do I often have to remind myself of how to perform this simple task. For our example we'll write a schema to describe the following document.

{% highlight xml %}
<inventory>
  <Desk></Desk>
  <Chair></Chair>
  <Chair></Chair>
  <!-- and so on… -->
</inventory>
{% endhighlight %}

Our inventory can contain any number of desks and chairs, and there is no restriction on the order of the elements in our document. Now if you're like me and have to write a schema for this, you'll be tempted to use an `all` element like so.

{% highlight xml %}
<xs:element name="Inventory">
  <xs:complexType>
    <xs:all>
      <xs:element name="Desk" />
      <xs:element name="Chair" />
    </xs:all>
  </xs:complexType>
</xs:element>
{% endhighlight %}

The problem with this is that the `all` element requires that each child appear once and only once. Oh, and you can't modify this behaviour by applying a `maxOccurs` attribute to it.

The solution is to instead use a `choice` element, with `maxOccurs` set to `unbounded`.

{% highlight xml %}
<xs:element name="Inventory">
  <xs:complexType>
    <xs:choice maxOccurs="unbounded">
      <xs:element name="Desk" />
      <xs:element name="Chair" />
    </xs:choice>
  </xs:complexType>
</xs:element>
{% endhighlight %}

Here, `maxOccurs` states how many children the `choice` element may have, which in our case is an unlimited number.