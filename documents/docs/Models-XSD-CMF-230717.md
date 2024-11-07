### NIEM models in XSD and CMF

We are supporting two modeling formalisms in NIEM 6:  XSD and CMF.  This paper discusses the different kinds of model, the XSD and CMF artifacts comprising those models, the conformance targets for those artifacts, and a little bit about configuration management.  

**1.  A taxonomy of NIEM models and schemas** 

As we write the NDR and training materials, we will need to talk about:

* Authoritative models vs. subsets of authoritative models
* Models that define a message format vs. models intended for reuse
* Extension models and the NIEM model (that is, core plus domains)

I chased my own tail for a long time until I realized I was trying to use the term "reference"  for all three of those.  And then I realized that a reference schema document isn't the same thing as a model.  Neither is an extension schema document.  So I created the following taxonomy, with three kinds of NIEM model, and three sorts of related XSD artifacts.

<img src="C:\Work\IM23\ModelTaxonomy.png" alt="ModelTaxonomy" style="zoom:100%;" />
**1.1  Three kinds of NIEM model**

A *source model* is the authoritative specification of names, definitions, data types, and relations for concepts in one or more *namespaces*. These models are characterized by "optionality and over-inclusiveness". That is, they define more concepts than needed for any particular data exchange specification, typically without cardinalty constraints, making it easy to select the concepts that are needed and omit the others. Source models also typically omit unnecessary range or length constraints on property datatypes.

(Formerly I used "reference model" instead of source model – but since a useful source model in XSD is almost always formed from a combination of reference schema documents and extension schema documents, that didn't work out so well.)

A *subset model* selectss ome components from the corresponding source model and omits others.  This is often for convenience of reuse.  Every instance of a subset model is also an instance of the corresponding source model.  A subset model is usually created through a series of subset-preserving model transformations on the source model.

A *message model* is a subset of a source model with cardinality constraints and datatype restrictions intended to precisely define the content of a particular message format.

**1.2  Three kinds of NIEM XSD artifacts**

The NIEM 5 NDR defines *reference schema document* and *extension schema document*.  Each of these provides the authoritative definition of model components within a single namespace.  The NIEM 5 IEPD specification defines *constraint schema document set*.  The *constrant schema* formed by assembling these schema documents is intended to express business rules for a message format.  

1. A  reference schema document provides definitions that are intended for the widest possible reuse.  The rules in the conformance target for reference schema document provide semantics for XSD constructs.  The rules also promote the widest possible reuse.  Components in the NIEM core and domain are defined in reference schema documents.  

2. An extension schema document provides definitions that are intended for reuse within a more narrow scope than those defined by a reference schema document.  An extension schema document is intended to express the additional vocabulary required for an information exchange, above and beyond the vocabulary available from the NIEM model.  The conformance target for extension schema documents has the same rules for semantics, but relaxed rules for reuse; specifically, an extension schema document; specifically, an extension schema document
   * Can have `xs:restriction`
   * Can have `xs:choice`
   * Can have `xs:any` and `xs:anyAttribute`
   * Can have `@final`, `@fixed`, `@block`, `@blockDefault`, `@finalDefault`
   * Can have element declarations without `nillable="true"`
   * May have external attributes in any type definition (not just in adapter types)
   * Does not require augmentation points
   
3. A constraint schema is not intended to provide definitions for the semantics of the individual components it contains.  A constrant schema is not required to be a subset of any other schema.  It has no conformance target and follows no NDRs.  It is basically anything the message designer needs or wants.  (There is perhaps some expectation that every component in a constraint schema namespace is at least mentioned in the source model, but I don't see that expectation written down anywhere.)

**2.  NIEM models in XSD**

A source model in NIEM XSD is a *source schema* assembled from a set of reference and extension schema documents.  The schema components follow all of the NDRs for semantics and reuse.  The "NIEM model" is the source model formed by assembling all of the reference schema documents governed by the NBAC and domains.

A subset model in NIEM XSD is a *subset schema* assembled from a set of *subset schema documents*, each of which is a subset of a reference or extension schema document.  Everything valid against the subset schema is also valid against the source schema. A subset schema document follows most or all of the NDRs for semantics and reuse.  (If it's "most", then we may need a new SubsetSchemaDocument conformance target.)

A message model in NIEM XSD is a *message schema* assembed from a set of *message schema documents*. Because a message model is a kind of subset model, everything valid agianst the message schema must also be valid against the source schema.  However, we do not need a `MessageSchemaDocument` conformance target.  The message schema documents ***do not have to follow*** all of the NDRs for semantics and reuse, because semantics are available in the source model, and message schemas are not intended for reuse.  This is a big deal; it's new in NIEM 6. and supports:

* For message designers:  more granular constraints on cardinality and datatypes; for example, elements with the same semantic type (i.e. Class) but different validation constraints.  These granular constraints are achieved through local type definitions (forbidden in reference and extension schema documents).
* For developers:  simplified schemas, easier to implement; for example, substitution groups replaced with `xs:choice`, and long type chains (e.g. `PersonNameType` -> `ProperNameType` -> `TextType`) collapsed into a single type definition.

We don't yet know whether a message model in XSD (that is, a message schema) needs to be convertable to the equivalent message model in CMF.  It's definitely possible and desirable to convert CMF to a message schema pile, but that could be a one-way transform.

I think we will insist that a message designer create a source and subset model that contains all of his extension components. This preserves at least the possibility of reusing those components in another message specification.  For a designer working in XSD, that means creating one or more conforming extension schema documents.

A constrant schema isn't necessarily any kind of NIEM model at all, and needn't follow any conformance rules.

**3.  NIEM models in CMF**

A source model, subset model, or message model in NIEM CMF is just a CMF file.  We don't have CMF schemas.  When you're talking about NIEM models and you say "schema", you mean XSD.

The configuration management items in NIEM XSD are reference and extension schema documents.  These are each authoritative for a single namespace.  We will want the same per-namespace configuration item in NIEM CMF – that is, a CMF file that is authoritative for a single namespace.  We will call that a *CMF namespace file* (until we think of a better name.)  This means we need to add:

* A way for a CMF file to specify the namespace for which it is authoritative
* A way for a CMF file to refer to components it does not itself define
* At least one conformance rule for CMF:  a complete model must have definitions for all referenced components

What we do *not* need is the CMF equivalent of `xs:import` and a similar, underspecified assembly process for CMF files.  When you want to assemble a NIEM model from a set of CMF files, just put all the files into the assembler.



Author:  Scott Renner
Date: 2023-07-17
