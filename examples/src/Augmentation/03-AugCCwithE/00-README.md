* Copied the subset XSD from 01-N6-subset
  * *adapters, domain, utility,* and *niem-core.xsd*
  
* Added *message-model.xsd*

  * Made sure it imports *jxdm.xsd*, because this message includes Justice augmentations

* Created *augCCwE.cmf* with `cmftool x2m -o augCCwE.cmf message-model.cmf`

* Created a message schema with `cmftool m2xmsg augCCwE.cmf`

  * Dang!  When I build a schema from *message-model.xsd*, it doesn't include the Justice augmentations

* Created the message schema with `cmftool m2xmsg -c --msgNS http://example.com/N6AugEx/1.0/ augCCwE.cmf`

  * This time we get a catalog file!
  * This time `message-model.xsd` imports `jxdm.xsd`!

