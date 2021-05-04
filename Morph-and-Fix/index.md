% Documentation Morph and Fix

<img src="img/metafacture.png" alt="Metafacture" style="max-width:100%"/>

## Overview

Here you will find the different functions and collectors that you can use for transforming data. All of the following functions and collectors are available for Metamorph (short: Morph) which is written in XML.
Many of them are also available for FIX, the experimental succesor which is still being developed and is inspired by Catmandu FIX language.

The examples are displayed in the Formate-Format which is the internal Format of metafacture. It builds upon to basic elements: Literals (key-value-pairs) and entities (Objects, that encompase lit). Before metafacture flux passes data to the morph it decode the data into formeta.
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

### Functionalities

The functionalities can be differentiated between those that operate straight with single literals ("literal functions" short "functions") and those that collect or aggregate multiple literals ("collectors").

#### Literal functions 

##### Read - `<data>` / `map`

The simplest way to work with literals is to read them and then pass them on. This is done by the `data`-function.
It always needs a `source` attribute that tells the function which literal should be read.


{{% expand "Beispiel" %}}
{{% panel %}}
__Eingabe__

```
{litA: Alf}
```

__Morph-Definition__

```xml
<data source="litA" />
```

__FIX-Definition__

```
map ("litA")
```

__Ausgabe__

```
{litA: Alf}
```

{{% /panel %}}
{{% /expand %}}


It also can have a `name` attribute to rename the key.

{{% expand "Beispiel" %}}
{{% panel %}}
__Eingabe__

```
{litA: Alf}
```

__Morph-Definition__

```xml
<data source="litA" name="Alien" />
```

__FIX-Definition__

```
map ("litA", "Alien")
```

__Ausgabe__

```
{Alien: Alf}
```

{{% /panel %}}
{{% /expand %}}

Wildcards * and ? are compatible with the `source` attribute.

Additionally there are three variables for `source` that have fixed meanings:

- `_id`: reads the ID of the record
- `_else`: passes on all data elements that are not transformed
- `_elseNested`: passes on all data elements that are not transformed in nested structure


##### Transform

__compose__

Wraps the value in a prefix (Attribute: `prefix`) and/or postfix (`postfix`). 

__constant__

Replaces the value with a constant string. This value is defined in the attribute `value=`

__timestamp__

Replaces value with recent timestamp.

__substring__

Extraction of a substring. The extracted part is defined by attributes - `start=` and `end=` - stating the indixnumber of the letter.

__replace/setreplace__

Replaces a pattern with a string. The pattern is a Java regex Pattern (see http://docs. oracle.com/javase/6/docs/api/java/util/regex/Pattern.html).
Replaces multiple strings. The mapping from string to replacement is defined in a map, just as done in the lookup function. See also Data Lookup.

__lookup__

Lookup and replace based on a data table. See also Data Lookup. (Link zu Maps)

__case__

Transforms value in upper or lower values (to="upper" or to"lower") 

__trim__

Trim spaces at the end of the value.

__normalize-utf8__

UTF-8 normalization. Brings Umlauts into canonical form.

__dateformat__

__split__

splitting based on a regexp defined in attribute `delimiter=`

__isbn__

ISBN cleaning, checkdigit verification and transformation between ISBN 10 and ISBN 13. ISBN transformation defined in attribute `to=`. Also possible to clean ISBN from hyphen. `to="clean"`.
It is also possible to control the ISBN checkDigit with the attribute `verifyCeckDigit` and provide a own errorString (`errorString=`).

__urlencode__

transforms values to URL demands

__htmlanchor__

create an HTML anchor tag.

__switch-name-value__

Exchanges name and value.

__script__ and __java__

processing the value with a JavaScript function or a Java class. See Integration of Java and JavaScript.

__count__

Counts occurrences.

##### Filter

__blacklist__

__contains / not-contains__

Filtering based on containing.

__equals / not-equals__

Filtering based on equality.

__occurence__

filtering based on occurrence.

__regexp__

Regexp matching. Returns first occurrence of a pattern. The pattern is a Java regex Pattern (see http://docs.oracle.com/javase/6/docs/api/java/util/regex/Pattern.html).

__unique__

__buffer__

__whitelist__

Filtering based on a whitelist. See also Data Lookup

##### Maps

__maps__

__javamap__

__filemap__

__restmap__

__sqlmap__

__jndisqlmap__

#### Collectors

##### List of collectors

__combine__

__concat__

__entity__

__square__

__tuples__

__group__

__choose__

__range__

__equalsFilter__

__all, any, none__

##### Process controll

__flush__

__reset__

__sameEntity__


##### if conditionals


#### Modularise

__recursion__

__variable__

__makros__

__includes__