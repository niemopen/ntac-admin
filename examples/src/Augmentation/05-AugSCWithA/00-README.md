* Copied the subset XSD from 01-N6-subset
  * *adapters, utility,* and *niem-core.xsd*
  * Not using any of the Justice augmentations in this spec
  
* Added *message-model.xsd*
  * Has new `privacyText` attribute; we're going to augment `nc:EducationType` with it
  
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
  
* Created `augSCwA.cmf` with `cmftool x2m -o augCCwA.cmf messageModel.xsd`

* Created the message schema with `cmftool m2xmsg augSCwA.cmf`

* Recreated `augCCwA.cmf` with `cmftool x2m -o augSCwA.cmf messageModel.xsd`