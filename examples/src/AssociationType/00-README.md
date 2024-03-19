*Question:* Do we need `structures:AssociationType`?  

*Observation:* A message designer can use it for a global augmentation of every AugmentationType.  Other objects (derived from `structures:ObjectType`) are unaffected.

*However,* we have the same capability in NIEM 6 without relying on `structures:AssociationType`.  However, every AssociationType in the NIEM model is an extension of `nc:AssociationType`.  An augmentation of `nc:AssociationAugmentationPoint` will have the same effect.

*Example with `structures:AssociationType`:*

Given an extension schema like this:

```  xml
<xs:schema
  targetNamespace="https://example.com/AssocType/1.0/"
  xmlns:my="https://example.com/AssocType/1.0/"
  xmlns:structures="https://docs.oasis-open.org/niemopen/ns/model/structures/6.0/" ...
  <xs:element name="AssociationAugmentation" 
    type="my:AssociationAugmentationType" 
    substitutionGroup="structures:AssociationAugmentationPoint"/>
```

We can have runtime data like this:

```xml
<j:ArrestInvolvedWeaponAssociation
 xmlns:my="https://example.com/AssocType/1.0/"
 xmlns:nc="https://docs.oasis-open.org/niemopen/ns/model/niem-core/6.0/"
 xmlns:j="https://docs.oasis-open.org/niemopen/ns/model/domains/justice/6.0/">
  <my:AssociationAugmentation>
    <my:PrivacyText>Secret</my:PrivacyText>
  </my:AssociationAugmentation>
  <nc:AssociationDescriptionText>A sample association</nc:AssociationDescriptionText>
  <nc:Activity>
    <nc:ActivityName>Beating on a printer that doesn't work</nc:ActivityName>
  </nc:Activity>
  <nc:Item>
    <nc:ItemName>Monkey wrench</nc:ItemName>
  </nc:Item>
</j:ArrestInvolvedWeaponAssociation>
```

*Example without `structures:AssociationType`:*

The extension schema contains this instead:

```xml
  <xs:element name="AssociationAugmentation" 
    type="my:AssociationAugmentationType" 
    substitutionGroup="nc:AssociationAugmentationPoint"/>
```

We remove `structures:AssociationType` from *structures.xsd*.
We make `nc:AssociationType` extend `structures:ObjectType`.
Now the runtime data looks like this:

```xml
<j:ArrestInvolvedWeaponAssociation
 xmlns:my="https://example.com/AssocType/1.0/"
 xmlns:nc="https://docs.oasis-open.org/niemopen/ns/model/niem-core/6.0/"
 xmlns:j="https://docs.oasis-open.org/niemopen/ns/model/domains/justice/6.0/">
  <nc:AssociationDescriptionText>A sample association</nc:AssociationDescriptionText>
  <my:AssociationAugmentation>
    <my:PrivacyText>Secret</my:PrivacyText>
  </my:AssociationAugmentation>
  <nc:Activity>
    <nc:ActivityName>Beating on a printer that doesn't work</nc:ActivityName>
  </nc:Activity>
  <nc:Item>
    <nc:ItemName>Monkey wrench</nc:ItemName>
  </nc:Item>
</j:ArrestInvolvedWeaponAssociation>
```

The order of elements is different, but that does not affect the meaning.

*Conclusion:*  A message designer wishing to augment every AssociationType may do so, without `structures:AssociationType`.

*However,* what he *can't* do is augment Objects *without* augmenting Associations.  I suppose there might be message designers with that requirement – and I don't know why both of them couldn't just write a Schematron rule :-).

*But anyway,* today I think I figured out how to support global augmentations of `structures:AssociationType` in CMF without too much violence.  (Having tried all the ways that won't work, the one that's left is…)

*So maybe:*  [Never mind](https://www.youtube.com/watch?v=OjYoNL4g5Vg)