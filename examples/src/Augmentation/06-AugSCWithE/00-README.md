## Simple content augmented with an object

* Copied the subset XSD from 01-N6-subset

  * *adapters, utility,* and *niem-core.xsd*
  * Not using any of the Justice augmentations in this spec

* Added *message-model.xsd*
  * Has new `PrivacyAssertion` element; and `privacyAssertionRef` attribute.  We're going to augment `nc:EducationType` with these

* Edited *niem-core.xsd* to effect the augmentation

  ```
    <xs:complexType name="TextType">
      <xs:simpleContent>
        <xs:extension base="xs:string">
          <xs:attribute ref="my:privacyAssertionRef" 
            appinfo:augmentingNamespace="http://example.com/N6AugEx/1.0/"/>
        </xs:extension>
      </xs:simpleContent>
    </xs:complexType>
  ```

  