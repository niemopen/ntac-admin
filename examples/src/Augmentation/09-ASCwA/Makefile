.PHONY: clean

XSD = model.xsd/msg.xsd \
      model.xsd/niem/niem-core.xsd

model.cmf : $(XSD)
	cmftool x2m -o model.cmf model.xsd/msg.xsd

message.xsd : model.cmf
	cmftool m2xmsg -o message.xsd model.cmf

test : model.cmf message.xsd
	cmftool mval model.cmf
	cmftool m2x -o tmp -r my model.cmf
	diff -r model.xsd tmp
	cmftool xval -s message.xsd/msg.xsd -f msg1valid.xml
	cmftool xval -s message.xsd/msg.xsd -f msg2invalid.xml

clean :
	rm -rf model.cmf message.xsd tmp
