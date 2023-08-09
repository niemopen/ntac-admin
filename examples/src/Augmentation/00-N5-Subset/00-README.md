* Subset schema created with SSGT; see *wantlist.xml*
* Schema documents then run through `cmftool xcanon` to make them readable
* *n5sub.cmf* created with `cmftool x2m -o n5sub.cmf niem-core.xsd domains/jxdm.xsd`
  * Justice domain won't be included unless you include *jxdm.xsd* in the command.  Isn't schema assembly wonderful?