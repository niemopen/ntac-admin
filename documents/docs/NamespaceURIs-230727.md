BLUF:  I did more research, and now I believe best practice has NIEM namespace URIs ending in "/", not "#".

A NIEM model namespace:

1. Has exactly one namespace URI, which is shared with no other namespace
2. Has a preferred namespace prefix
3. Has a governing authority
4. Defines a set of model components (classes and properties, also known as types, elements, and attributes)
5. Has an authoritative definition (XML Schema document and CMF file – these are equivalent and convertable)

A NIEM model namespace doesn't have a whole lot to do with the XML Namespace specification, especially in the post-XML-only world of NIEM 6.  But a NIEM model namespace is a pretty good match to a RDF vocabulary.  So we should look at best practices for those.  Which brings us to the W3C's [HashVsSlash](https://www.w3.org/wiki/HashVsSlash) wiki page from November 2022:

> There's a long-standing issue in the RDF community about whether to use **slash URIs** like —
>
> ```
> http://purl.org/dc/elements/1.1/title
> ```
>
> — or **hash URIs** like —
>
> ```
> http://www.w3.org/1999/02/22-rdf-syntax-ns#type
> ```
>
> That is, should the character before the last word be a hash ("#") or a slash ("/")?
>
> Short answer: **slash URIs are generally preferred** because they give greater flexibility, but read on for a more detailed explanation and comparative advantages of each approach.

Webb chose hash URIs back in 2014 when he wrote [5.6.1. Resource IRIs for XML Schema components and information items](https://reference.niem.gov/niem/specification/naming-and-design-rules/3.0/niem-ndr-3.0.html#section_5.6.1) in NDR 3.0.  

> Certain components defined by NIEM schemas and instances have corresponding resource IRIs. Each IRI is taken from a qualified name, as follows:
>
> - If namespace name ends with #: concatenate(namespace name, local part)
> - Otherwise: concatenate(namespace name, #, local part)

Before that, NIEM model components did not have URIs.  After that, NIEM model components had hash URIs.  Probably a good choice at the time.  But it led to some weirdness with NIEM JSON.  As you will recall, we chose JSON-LD as the basis for NIEM JSON.  JSON-LD is a RDF syntax; therefore, NIEM JSON is RDF.  The NDR defines a RDF mapping for NIEM XML; therefore, NIEM XML is also RDF.  ***That has to be the exact same RDF***, or we go to Jail, do not pass GO or collect $200. So for NIEM 5 JSON we have to write `@context` pairs like this:

```
"@context": {
  "nc": "http://release.niem.gov/niem/niem-core/5.0/#"  
}
```

See that trailing # character?  That has to be there, or else the NIEM JSON and NIEM XML won't be the same RDF.  Which means that in NIEM 5, the URI value in a NIEM JSON `@context` is *not the namespace URI*.  I am just sure that will bite us on the ass at some awkward moment.  Which is why until now I've said that NIEM 6 namespace URIs must end in a #.

HOWEVER, we could switch to slash URIs by rewriting that part of the NDR for NIEM 6, like this:

> Certain components defined by NIEM schemas and instances have corresponding resource IRIs. Each IRI is taken from a qualified name, as follows:
>
> - If namespace name ends with /: concatenate(namespace name, local part)
> - Otherwise: concatenate(namespace name, /, local part)

And then our NIEM JSON `@context` pairs will look like this:

```
"@context": {
  "nc": "http://docs.oasis-open.org/niemopen/ns/niem-core/6.0/"
}
```

The only users who will notice are those who are currently working with NIEM JSON and concerned about matching the RDF from NIEM XML.  I don't think there are any such users.

Finally, here is a cool example of what can happen when you try to resolve slash URIs.  We could eventually work our way up to this.

* a QUDT component:  http://qudt.org/schema/qudt/CurrencyUnit
* the QUDT namespace: http://qudt.org/schema/qudt

Resolving a NIEM namespace URI might return different formats based on content negotiation.  You could ask for *text/html* and get something readable in a browser.  You could ask for *application/niem+xsd* or *application/niem+cmf* and get the authoritative namespace definition in the desired formalism.  Someday.