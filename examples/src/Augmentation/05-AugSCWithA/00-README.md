## Simple content augmented with an attribute

* Copied the subset XSD from 01-N6-subset

  * *adapters, utility,* and *niem-core.xsd*
  * Not using any of the Justice augmentations in this spec

* Added *message-model.xsd*
  * Has new `privacyText` attribute; we're going to augment `nc:TextType` with it

* Edited *niem-core.xsd* to effect the augmentation

  ```
    <xs:complexType name="TextType">
      <xs:simpleContent>
        <xs:extension base="niem-xs:string">
          <xs:attribute ref="my:privacyText" appinfo:augmentingNamespace="http://example.com/N6AugEx/1.0/"/>
        </xs:extension>
      </xs:simpleContent>
    </xs:complexType>
  ```

  