## NIEM 5.2 subset schema and model

Generated with SSGT using the wantlist provided.  I ran all the schema documents through "cmftool xcanon" to make them easier to read.  Contains *n5sub.cmf*, which is a NIEM 6 CMF file describing the NIEM 5.2 subset.  Yes, that works, CMFTool understands NIEM 5 (and 4, 3, and 2) just fine.

Note that in CMF, augmentations just appear in the model like any other property; for example, look at the Class object for `nc:EducationType`.  The augmentation properties are marked.  They are also listed in the augmenting Namespace object.