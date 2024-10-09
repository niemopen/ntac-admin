# Literal properties and atomic classes in NIEM 6

**Rationale:** Literal properties and atomic classes are a part of a technology-neutral NIEM architecture. We support XML and JSON today. I expect at some point to add YAML, Protobuf, Avro, etc. Looking ahead a few years in my crystal ball, I see most developers working with the new serializations, and relatively little XML work. So I want to avoid XML-specific weirdness  in CMF.

Simple content with attributes is weird in every other serialization we are likely to support.  Weird how? For example, XML like this:

```
<my:Thing>
  <my:ThingWeight my:weightUnits="kilo">500</my:ThingWeight>
</my:Thing>
```

turns into JSON like this:

<pre>
"my:Thing": {
  "my:ThingWeight": {
    "my:weightUnits": "kilo",
    "<span class="emp">??what-name-goes-here??</span>": 500
  }
}
</pre>

and RDF like this:

<pre>
_:n0 my:Thing _:n1 .
_:n1 my:ThingWeight _:n2 .
_:n2 my:weightUnits "kilo" .
_:n2 <span class="emp">??what-name-goes-here??</span> 500 .
</pre>

You see the problem, of course. We need a property name for the weight value, and XSD for simple content with attributes does not provide one.

I believe that JSON developers today (and developers with other serializations tomorrow) are entitled to have a meaningful name for that property in their data model and in their messages. It should be the same name you would get if your XML message was modeled without an attribute, like this:

```
<my:Thing>
  <my:ThingWeight>
    <my:WeightUnits>kilo</my:Units>
    <my:WeightValue>500</my:WeightValue>
  </my:ThingWeight>
</my:Thing>
```

Then you would get:

<pre>
"my:Thing": {
  "my:ThingWeight": {
    "my:WeightUnits": "kilo",
    "my:WeightValue": 500
  }
}
</pre>

and:

<pre>
_:n0 my:Thing _:n1 .
_:n1 my:ThingWeight _:n2 .
_:n2 my:WeightUnits "kilo" .
_:n2 my:WeightValue 500 .
</pre>

It's the same information either way. XSD just has one unique weird way of representing it. Somebody has to see some weirdness. I don't want it to be the other developers. I want it to be the XML message designers who put attributes on simple content. I don't mind if they find it a bit awkward because I don't like attributes on simple content anyway.

Now, in CMF, I had to use properties like `my:WeightLiteral` instead of `my:WeightValue`. I don't like that, but the NIEM model already has 600+ property names ending in "Value". There are no property names ending in "Literal". I'd happily use a better suffix if somebody can think of one.

An alternative to literal properties is to use `rdf:value` as the name for every *??what-name-goes-here??* property in every message. As a JSON developer, I say that sucks. 

If there's a third alternative, I haven't found it.

\
Author: Scott Renner\
Last modified: 2024-10-09

<style>
    h1,h2,h3 { font-size:14pt }
    pre { background-color:#f0f0f0; }
    code { background-color:#f0f0f0; }
    span.emp { font-size:10pt; font-family: Arial, sans-serif; font-style:italic; }
</style>