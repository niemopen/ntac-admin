### XSD conformance targets in NIEM 6

I've been thinking about conformance targets, and how these relate to configuration management and workflow.  Some of the terms have seemed a bit confused and/or awkward.  i'm trying to arrange them into something easy to understand.  

**1.  A taxonomy of NIEM models**

Writing this paper drove me crazy until I realized I was trying touse "reference"  for three different things:  a model intended for reuse, a model that is authoritative, and the NIEM model.  Didn't make sense.  So I chose *Reference == Authoritative*, and then made up two new terms – *Reuse model* and *NIEM-Model* – and presto! things made sense.  See what you think.

```
Model --+-- Message Model
        |
        +-- Reuse Model --+-- Subset Model
                          |
                          +-- Reference Model --+-- NIEM-Model
                                                |
                                                -- Extension  Model
```

**1.1  Two kinds of NIEM model**

A *reuse model* provides names and definitions for concepts, organized by *namespace*, and also provides relations among these concepts. These models are characterized by "optionality and over-inclusiveness". That is, they define more concepts than needed for any particular data exchange specification, typically without cardinalty constraints, making it easy to select the concepts that are needed and omit the others. Reuse models also omit unnecessary range or length constraints on property datatypes.  A reuse model provides concept definitions in some domain of discourse; in this way it fulfils some of the roles of an ontology.

A *message model* includes cardinality constraints and datatype restrictions in order to precisely define the content of a particular message format. These models are formed by assembling subsets of the reference schema documents that define the NIEM model, plus one or more extension schema documents that contain any exchange-specific definitions.

A reuse model is intended for reuse in several message models.  A message model is not intended for reuse.

**1.2  Two kinds of reuse model**

A *reference model* is the complete and authoritative definition of all the model components in a set of namespaces.  A *subset model* omits components from a corresponding reference model, including only those components that are not necessary for some purpose.  You want the complete and authoritative definition of a namespace's semantics?  You refer to the reference model.

**1.3  Two kinds of reference model**

A *NIEM-Model* is a reference model defining components in one or more namespaces that are controlled by the NTAC or a NIEM domain.  An *extension* model is a reference model controlled by some other authority.  An extension model is intended to express the additional vocabulary required for an information exchange, above and beyond the vocabulary available from the NIEM model.

**1.4  Discussion**

I think the seven categories in this taxonomy are right.  I'm not dead set on the names for those categories.  I'm not dead set on my choice of *Reference == Authoritative*.  if you get to the end of this paper and think you have better names, I'm all ears.

**2.  Two NIEM modeling formalisms:  XSD and CMF**

A NIEM model expressed in XSD is a *schema*.  When we're talking about NIEM models, and we say schema, we mean XSD.  A schema is an abstract construct formed by assembling a set of *schema documents*.  In XSD, a schema document defines model components in exactly one namespace.  In NIEM schemas we also enforce the inverse – a namespace is defined by exactly one schema document – by forbidding `xs:include`.

We could talk about seven kinds of NIEM schema documents – model, message, reuse, subset, reference, extension, and NIEM-model – but it turns out we only care about four of those.  We will need conformance targets for:

* *message schema document*
* *subset schema document*
* *extension schema document*
* *NIEM-model schema document* (formerly "reference schema document")

A NIEM model may also be expressed as a *CMF file*.  A CMF file defines model components in one or more namespaces.  Kinds of CMF file and corresponding conformance targets are TBD.  There's no such thing as a "CMF schema".

**3.  Existing conformance targets in NIEM 5 XSD**

* Reference schema document:  "A schema document that is intended to provide the authoritative definitions of broadly reusable schema components.  A reference schema document is a schema document that is intended to be the authoritative definition schema for a namespace" [NDR 4.1.1].  The NIEM model is defined by a set of reference schema documents.  (By the way, is there a rule forbidding NBAC and domains to create extension schema documents instead?)
* Extension schema document:  "A schema document that is intended to provide definitions of schema components that are intended for reuse within a more narrow scope than those defined by a reference schema document.  It provides the authoritative definition of business semantics for components in its namespace. It contains components that, when appropriate, use or are derived from the components in reference schema documents." [NDR 4.1.2]  It's like a reference schema document, but not part of the NIEM model, and can have wildcards and be a little less reusable.  Specifically:
  * Can have `xs:restriction`
  * Can have `xs:choice`
  * Can have `xs:any` and `xs:anyAttribute`
  * Can have `@final`, `@fixed`, `@block`, `@blockDefault`, `@finalDefault`
  * Can have element declarations without `nillable="true"`
  * May have external attributes in any type definition (not just in adapter types)
  * Does not require augmentation points
* Conformant schema document set:  "A collection of schema documents that together are capable of validating a conformant instance XML document" [NDR 4.1.3].  The set of schema documents comprise a *schema*.  
* Conformant instance XML document:  "An XML document that is an instance document valid to a conformant schema document set" [NDR 4.1.4].  (This looks like a circular definition, but isn't, quite.)

**4,  Conformance targets for NIEM 6 XSD **

We need one new conformance target for subset schema documents and another for message schema documents.

When you generate a subset of a reference schema document with SSGT (or whatever), the result is *not* intended to be the authoritative definition for its namespace.  The same is true for a subset of an extension schema document.  So I think these schema documents should have a different CTA – perhaps `SubsetSchemaDocument`.  We should reserve `NIEM-ModelSchemaDocument` and `ExtensionSchemaDocument` for the actual authoritative documents.  I suspect we will want to refer to those authoritative documents in some new conformance rules, so it will be handy to have them clearly marked.

A message schema includes cardinality constraints and datatype restrictions to define the content of a particular message format.  We need another conformance target for the message schema documents that comprise a message schema – perhaps `MessageSchemaDocument`.  We need this because we want some constraints and restrictions that don't follow all of the NIEM 5 NDRs.  For instance, we need local type definitions to support constraints which allow reference attributes (`id`,`ref`, and `uri`) on some elements and not on other elements.  In general, the NDR for a message schema document gives away rules supporting semantics and reuse in order to support more precise content constraints.  (The semantics and reuse are still available from the reference schema.)

**5.  Conclusion**

We need four conformance targets for NIEM schema documents.  We need "reference" to mean one thing, not three.  If you don't like my choice of *Reference == Authoritative*, invent your own two new terms and we'll use them instead.



Author: Scott Renner
Date:  2023-07-11