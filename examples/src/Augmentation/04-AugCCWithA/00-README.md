## Complex content augmented with an attribute

Copied the subset XSD from 01-N6-subset

* *adapters, utility,* and *niem-core.xsd*
* Not using any of the Justice augmentations in this spec

Added *message-model.xsd*
* Has new `privacyText` attribute; we're going to augment `nc:EducationType` with it
* Observe the `appinfo:AttributeAugmentation` element

```
<xs:annotation>
  <xs:appinfo>
    <appinfo:AttributeAugmentation augmented="nc:EducationType" attribute="my:privacyText"/>
  </xs:appinfo>
</xs:annotation>
```



Edited *niem-core.xsd* to effect the augmentation

```
<xs:complexType name="EducationType">
  <xs:annotation>
    <xs:documentation>A data type for a person's educational background.</xs:documentation>
  </xs:annotation>
  <xs:complexContent>
    <xs:extension base="structures:ObjectType">
      <xs:sequence>
        <xs:element ref="nc:EducationDescriptionText" minOccurs="0" maxOccurs="unbounded"/>
        <xs:element ref="nc:EducationInProgressIndicator" minOccurs="0" maxOccurs="unbounded"/>
        <xs:element ref="nc:EducationAugmentationPoint" minOccurs="0" maxOccurs="unbounded"/>
      </xs:sequence>
      <xs:attribute ref="my:privacyText" appinfo:augmentingNamespace="http://example.com/N6AugEx/1.0/"/>
    </xs:extension>
  </xs:complexContent>
</xs:complexType>
```

