## Configuration management in NIEM 6:  CMF and XSD files

The main target of configuration management in NIEM version 1 through 5 is the XML schema document.  A NIEM domain publishes its model as one or more reference schema documents, each providing declarations and definitions of the components in its target namespace.  The NBAC does the same for the NIEM core model.  A message designer likewise publishes extension schema documents as part of his message specification.  These schema documents are the authoritative source for their corresponding namespaces.

(It bothers me a little that there is nothing in the schema document to *say* it is the authoritative source.  Conformance target assertions are not quite the same thing.  Oh well, if it matters people can write something in the schema `xs:documentation`, I guess.)

CMF and XSD are interchangeable In NIEM 6, and so we need a CMF equivalent to the authoritative XSD document.  You won't get that by dropping, say, *domains/cyber.xsd* into the current CMFTool. That tool today produces *complete model* CMF file.  What we need is a *namespace* CMF file — one that defines components in just one namespace, and merely references the components in all the others.  And *that* means a namespace CMF file can't use REFs for components in the other namespaces.  So instead of the `@ref` in line 7 below:

```
01 <Class structures:id="cyber.CompromisedCommunicationType">
02   <Name>CompromisedCommunicationType</Name>
03   <Namespace structures:ref="cyber" xsi:nil="true"/>
04   <DefinitionText>A data type for a compromised communication between systems</DefinitionText>
05   <AugmentableIndicator>true</AugmentableIndicator>
06   <HasProperty>
07     <Property structures:ref="nc.StartDate" xsi:nil="true"/>
08     <MinOccursQuantity>0</MinOccursQuantity>
09     <MaxOccursQuantity>unbounded</MaxOccursQuantity>
10   </HasProperty>
```

The authoritative CMF file for the cyber domain (*cyber.cmf*) must have the `@uri` in line 8 below:

```
06   <HasProperty>
07     <Property 
08       structures:uri="https://docs.oasis-open.org/niemopen/ns/model/niem-core/6.0/StartDate" 
09       xsi:nil="true"/>
```

The *cyber.cmf* file won't contain objects for any of the components in other namespaces.  It will just point to them with `uri` attributes.

All this requires some new capabilities in CMFTool:

1. Write a single namespace CMF file from a model
2. Write a complete set of namespace CMF files for a model
3. Combine a set of CMF files into a model, checking for consistency and completeness

