This directory contains example schema documents and messages to demonstrate augmentation of types in a type hierarchy.  

Directory *referenceSchema* shows the XSD for the NIEM reference model.  It shows what the reference schema documents would look like in the proposed NIEM 6.  The core and domain reference schema documents would actually have definitions for the full core and domain models.  You should pretend the missing definitions are there, looking like the examples you see.  The *messageModel.xsd* document isn't part of the NIEM reference model, of course; it's here for validation convenience.

Directory *inheritedAug* contains the message schema created when the designer says

* augment nc:ItemType with j:ItemMerchandiseIndicator and em:ItemExpirationDate
* augment nc:ConveyanceType with j:ConveyanceJointlyRegisteredIndicator
* augment nc:VehicleType with j:VehicleGarageIndicator

