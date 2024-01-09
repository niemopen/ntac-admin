> 2024-01-02 draft

# 1. Data modeling in NIEM

NIEM is a framework for developer-level specifications of data.  This specified data is called a *message* in NIEM.  A message is usually  something passed between applications, but NIEM works equally well to specify an information resource published on the web, an input or output for a web service or remote procedure, and so forth.  Data models in NIEM are used to specify *message formats*; they define the mandatory and optional content of messages and the meaning of that content.  Data models in NIEM are also used to capture community agreement on data definitions that are thought to be useful in many data specifications.

A NIEM data model is composed of one or more *namespaces*.  A namespace is a collection of names, definitions, class and data types, and properties for the concepts that are of interest to the namespace author.  Namespace content, once published, cannot be changed; incompatible revisions require a new namespace.  This content stability encourages authors to reuse components from other namespaces.

NIEM models fall into one of three categories:

* A *source model* is the authoritative specification of the components in a namespace. These models are characterized by "optionality and over-inclusiveness". That is, they define more concepts than needed for any particular data specification, typically without cardinality constraints, making it easy to select the concepts that are needed and omit the others. Source models also typically omit unnecessary range or length constraints on property datatypes.  Source models do some of the things an ontology does, by defining terms in a domain of discourse.
* A *subset model* selects some components from one or more source models whle omitting others. This is usually for convenience of reuse. Developers usually create a subset when creating a data specification; they sometimes also create a subset as a starting point for several data pecifications in their organization. Every instance of a subset model is also an instance of the corresponding source model. A subset model is usually created through a series of subset-preserving model transformations on the source model. It typically captures semantics but not cardinality or datatype constraints, and is intended for extension and reuse.
* A *message model* is a subset with cardinality and datatype constraints intended to precisely define the content of a particular message format; that is, a data specification. It is not intended for extension or reuse.

A data model in NIEM is itself data that has a NIEM specification.  The mandatory and optional content of a data model and the meaning of that content is defined by the *NIEM metamodel* – that is, the NIEM data model for data models.  The metamodel is an abstract model.  It has two concrete, developer-level representations:  XML Schema (XSD) and the *Common Model Format (CMF)*.

NIEM's original model representation is XSD.  The NIEM *Naming and Design Rules (NDR)*  defines a profile of XML Schema and assigns semantics to various XSD constructs; for example, `xs:extension` defines a subclass, and element substitution defines a subproperty.  An XML schema conforming to the NDR is a model representation.  Source, subset, and message models in XSD each conform to a different set of rules; these are described in section FIXME.  The XML schema for a message model specifies the content and meaning of a set of messages, and can also be used to validate messages that are serialized in XML.  

CMF is the result of applying the NIEM framework to the specification of NIEM data models.  The CMF specification is just like any other NIEM-based message specification.  A CMF model file is a NIEM message, just like any other NIEM-conforming message.  It is also a model representation, specifying content and meaning of a set of messages.  CMF does not directly support message validation, but it may be converted into artifacts that do (XSD for XML, JSON Schema for JSON, etc.)

# 2. The NIEM Metamodel

The NIEM metamodel is an abstract model defining the information content of a NIEM data model.  It is described by the following UML class diagram and property tables:

![metamodel](metamodel.png)

The table format used to document metamodel classes and properties has the following columns:

* Name
* Definition
* Card – cardinality of the property
* O – true for repeatable properties when the order is significant
* Range – class or datatype of thie property

## 2.1  Model object

A model object represents a complete NIEM model.  It is comprised of a set of namespace and component objects.

## 2.2 Namespace class diagram

<img src="namespace.png" alt="namespace" style="zoom:67%;" />

### 2.2.1 Namespace object 

A namespace object represents a namespace in a model.  Properties marked with a star are only used in the XSD representation of a model.

| Name          | Definition                                                   | Card | O    | Range        |
| ------------- | ------------------------------------------------------------ | ---- | ---- | ------------ |
| aug           | A list of the augmentations made by this namespace           | 0..* |      | Augmentation |
| term          | A list of the local terms defined in this namespace          | 0..* |      | LocalTerm    |
| uri           | The URI of this namespace                                    | 1    |      | xs:anyURI    |
| prefix        | The prefix assigned to this namespace in the model.  For any model there is a one-to-one mapping between namespace prefix and namespace URI. | 1    |      | xs:NCName    |
| documentation | A description of this namespace                              | 0..* | Y    | TextType     |
| lang          | The default language of text strings in the model            | 1    |      | xs:language  |
| version       | The version of this namespace (and the components it comprises) | 0..1 |      | xs:string    |
| kind*         | A kind of namespace in a model (extension, domain, core, etc.) | 0..1 |      | NSKindCode   |
| niemVersion*  | The version of NIEM builtin schema documents referenced in the XML schema document for this namespace (4.0, 5.0, etc.) | 0..1 |      | xs:token     |
| confTargs*    | The URI of a conformance target assertion for the XML schema document for this namespace | 0..* |      | xs:anyURI    |
| docPath*      | The relative path of the XML schema document for this namespace within the directory of the model's schema document pile | 0..1 |      | xs:string    |
| importDoc*    | Documentation used for an`xs:import` element used in the schema document of any other namespace to import this namespace | 0..1 |      | TextType     |

### 2.2.2  Augmentation object

When the author of one namespace adds a property to a class in another namespace, that is an augmentation.  An augmentation object is the record associated with the augmenting namespace.  (The augmentation is also recorded in the augmented namespace; see the HasProperty metamodel class.)  Relationships between the Augmentation, ClassType, and Property classes in the metamodel exist, but are not shown on the UML diagram. 

| Name      | Definition                                                   | Card | Range      |
| --------- | ------------------------------------------------------------ | ---- | ---------- |
| class     | The model class that is augmented by this namespace          | 1    | ClassType  |
| property  | The property added to the augmented class                    | 1    | Property   |
| index     | The position of the property in the XSD augmentation type for this class, if one exists | 0..1 | xs:integer |
| minOccurs |                                                              |      |            |
| maxOccurs |                                                              |      |            |

### 2.2.3 LocalTerm

A namespace may use terms that do not appear in the Oxford English Dictionary.  A LocalTerm object records the definition of such a term.

| Name          | Definition                                                   | Card | O    | Range    |
| ------------- | ------------------------------------------------------------ | ---- | ---- | -------- |
| name          | A name for a local term                                      | 1    |      | xs:token |
| documentation | A definition of a local term.                                | 0..* | Y    | TextType |
| literal       | A meaning of a local term provided as a full, plain-text form | 0..1 |      |          |
| sourceURI     | An identifier or locator for an originating or authoritative document defining a local term | 0..* |      |          |
| citation      | A plain text citation of, reference to, or bibliographic entry for an originating or authoritative document defining a local term | 0..* |      |          |

## 2.3 Component class diagram