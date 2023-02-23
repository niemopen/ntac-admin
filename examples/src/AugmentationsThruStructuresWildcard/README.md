This directory contains example schema documents and messages to demonstrate that augmentation point elements are necessary when augmenting types in a type hierarchy.  

Directory *referenceSchema* is an attempt to implement augmentations through a single wildcard in `structures:ObjecType` and `structures:AssociationType`.

Directory *inheritedAug* is about augmentation and type inheritance.  It contains the message created when the designer says

* augment nc:ItemType with j:ItemMerchandiseIndicator and em:ItemExpirationDate
* augment nc:ConveyanceType with j:ConveyanceJointlyRegisteredIndicator
* augment nc:VehicleType with j:VehicleGarageIndicator

With this approach we can have a message that is valid against the message schema, or against the reference schema, but not both.  This is why augmentation points are necessary.
