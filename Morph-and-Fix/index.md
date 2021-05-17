% Documentation Morph and Fix

<img src="img/metafacture.png" alt="Metafacture" style="max-width:100%"/>

# index

- [Literal functions](literalFunctions.md)
- [Collectors](collectors.md)
- [Modularise](modularise.md)

# Overview

Here you will find the different functions and collectors that you can use for transforming data. The functionalities can be differentiated between those that operate straight with single literals ("literal functions" short "functions") and those that collect or aggregate multiple literals ("collectors").

*All* of the following functions and collectors are available for Metamorph (short: Morph) which is written in XML.
*Many but not all* of them are also available for FIX, the experimental succesor which is still being developed and is inspired by Catmandu FIX language.

The examples are displayed in the **Formate**-Format which is the internal Format of metafacture. It builds upon to basic elements: Literals (key-value-pairs) and entities (Objects, that encompase lit). Before metafacture flux passes data to the morph it decode the data into formeta.
E.g.:

```xml
<datafield tag="085" ind1=" " ind2=" ">
  <subfield code="8">1.1</subfield>
  <subfield code="b">346.046</subfield>
  <subfield code="a">346.046</subfield>
  <subfield code="r">333</subfield>
  <subfield code="s">95</subfield>
</datafield>
```
looks in formeta like this:

```
{
	085  {
		8: 1.1
		b: 346.046
		c: 346.046
		r: 333
		s: 95
	}
}
```

`085  ` in this example is an entity, all subfields are literals.

_________
