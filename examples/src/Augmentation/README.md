**Attribute augmentation and metadata replacement**

This paper collects everything I've written about wildcards for attribute augmentations, corrects everything I now know to be wrong, and walks through the simplest examples I can construct.  At the end you should understand how (I think!) augmentation should work in NIEM 6, why that is a complete replacement for the metadata mechanism, and what all this means for XSD conformance targets.

**Bottom line up front**

Here is a summary of the important changes from NIEM 5.  The examples will show why these changes will work and are good and/or necessary.

* Subset schemas and message schemas are different.
  * A subset schema is a collection of model components intended for extension and reuse
  * A message schema is for validation and code binding.  It is not a model representation.  It does not conform to the NDR.
* A subset schema has attribute wildcards in *structures.xsd* and *niem-xs.xsd*
  * `<xs:anyAttribute processContents="strict"/>` says that any type may be augmented with an attribute; we promise to define that attribute in the message schema
  * The IC-ISM hack is removed
* Types in a message schema do not inherit from *structures.xsd*
  * Types in the message schema include individual attributes from the structures namespace, as needed. 
* Attribute augmentations are defined in the model
  * By `appinfo:Augmentation` elements in XSD
  * By an `AugmentationRecord` in CMF
* There is a new *reference attribute* concept and a new `Ref` representation term.  An attribute named `fooRef` has a list of references to `Foo` elements.
* `appinfo:relationshipPropertyIndicator` describes a property that modifies the relationship between its parent and grandparent object.  It does the same thing as `structures:relationshipMetadata`, but works for any property, not just metadata elements.
* `structures:appliesToParent` is false for an element that is not a property of its parent.
* There is no longer any need for the metadata mechanism.  Everything that can be done with `@metadata` and `@relationshipMetadata` can also be done with augmentations.

**Examples and what they illustrate**

The examples are message specifications that illustrate augmentations to `nc:EducationType`.  There are four kinds of augmentation in NIEM 6, depending on whether you are augmenting complex content or simple content, and whether your augmentation is an element or attribute.

I put the source code (XSD and CMF) for the examples into the ntac-admin repo.  You will find:

* 02-NoAug – message schema for the base case, no augmentations in this message spec
* 03-CCwE– message schema for complex content augmented with an element
* 04-CCwA – message schema for complex content augmented with an attribute
* 05-SCwA – message schema for simple content augmented with an attribute
* 06-SCwE– message schema for simple content augmented with an element
* 07-ACCwE– augmenting all complex content with an element
* 08-ACCwA– augmenting all complex content with an attribute
* 09-ASCwA – augmenting all simple content with an attribute.  *Doesn't work for XSD types.*
* 10-ASCwE – augmenting all simple content with an element.  *Doesn't work for XSD types.*

**Conclusion**

By putting attribute wildcards into the subset schema, but not into the message schema, we are able to support

* augmenting complex content with an element
* augmenting complex content with an attribute
* augmenting simple content with an element
* augmenting simple content with an attribute

Wildcards in the subset schema allow attribute augmentation while still preserving the subset schema principle.  (Everything valid againt the subset schema document is also valid against the reference or extension schema document.)

No wildcards in the message schema means it precisely defines the mandatory and optional content of the message format.

Augmentation in NIEM 6 an do everything the metadata mechanism does in NIEM 5.  One way to do things is better than two ways.  Fewer complex things to explain is better than more.  We should remove the metadata mechanism from NIEM 6.



Author:  Scott Renner
Revised:  2025-06-26
