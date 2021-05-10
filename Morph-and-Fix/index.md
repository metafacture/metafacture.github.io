% Documentation Morph and Fix

<img src="img/metafacture.png" alt="Metafacture" style="max-width:100%"/>

# Overview

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

_________

# Functionalities

The functionalities can be differentiated between those that operate straight with single literals ("literal functions" short "functions") and those that collect or aggregate multiple literals ("collectors").

## Literal functions 

### Read - `<data>` / `map`

The simplest way to work with literals is to read them and then pass them on. This is done by the `data`-function.
It always needs a `source` attribute that tells the function which literal should be read.


<details>
  <summary>Example <b>Morph</b></summary>
__Eingabe__

```
{litA: Alf}
```

__Morph-Definition__

```xml
<data source="litA" />
```

__Ausgabe__

```
{litA: Alf}
```

</details>


<details>
  <summary>Example <b>FIX</b></summary>
__Eingabe__

```
{litA: Alf}
```

__FIX-Definition__

```
map ("litA")
```
__Ausgabe__

```
{litA: Alf}
```

</details>

It also can have a `name` attribute to rename the key.

<details>
  <summary>Example <b>Morph</b></summary>
__Eingabe__

```
{litA: Alf}
```

__Morph-Definition__

```xml
<data source="litA" name="Alien" />
```

__Ausgabe__

```
{Alien: Alf}
```

</details>

<details>
  <summary>Example <b>FIX</b></summary>
__Eingabe__

```
{litA: Alf}
```

__FIX-Definition__

```
map ("litA", "Alien")
```

__Ausgabe__

```
{Alien: Alf}
```

</details>

Wildcards * and ? are compatible with the `source` attribute.

Additionally there are three variables for `source` that have fixed meanings:

- `_id`: reads the ID of the record
- `_else`: passes on all data elements that are not transformed
- `_elseNested`: passes on all data elements that are not transformed in nested structure

---
### Transform

The following passage introduces the different literal functions, which transform the values. As the filter functions in the subsequent passage all literal function are inserted in the `<data>`-Element. In the FIX language the literal functions are inserted in an `do map ([source], [name]) ... end`.

<details>
  <summary>Example <b>Morph</b></summary>

__Morph-Definition__

```xml
<data source="litA" name="gruss">
  <trim/>
  <whitelist>
    <entry name="hallo"/>
    <entry name="grüezi"/>
  </whitelist>
  <case to="upper"/>
</data>
```

</details>

<details>
  <summary>Example <b>FIX</b></summary>

__FIX-Definition__

```
do map ("litA", "gruss")
  trim()
  do whitelist
    entry ("hallo")
    entry ("grüezi")
  end
  case (to="upper")
end
```

</details>

_________
#### compose

[compose](https://github.com/metafacture/metafacture-core/tree/master/metamorph/src/main/java/org/metafacture/metamorph/functions/Compose.java) wraps the value in a prefix (Attribute: `prefix`) and/or postfix (`postfix`).

<details>
  <summary>Example <b>Morph</b></summary>
__Eingabe__

```
{litA: Alf}
```

__Morph-Definition__

```xml
<data source="litA">
  <compose  prefix="Hello,  "  postfix="!"/>
</data>
```

__Ausgabe__

```
{litA: Hallo, Alf!}
```

</details>

<details>
  <summary>Example <b>FIX</b></summary>
__Eingabe__

```
{litA: Alf}
```

__FIX-Definition__

```
do map("litA")
  compose(prefix: 'Hello' postfix: '!')
end
```

__Ausgabe__

```
{litA: Hallo, Alf!}
```

</details>

_________

#### constant

[constant](https://github.com/metafacture/metafacture-core/tree/master/metamorph/src/main/java/org/metafacture/metamorph/functions/Constant.java) replaces the value with a constant string. This value is defined in the attribute `value`

<details>
  <summary>Example <b>Morph</b></summary>
__Eingabe__

```
{litA: Alf}
```

__Morph-Definition__

```xml
<data source="litA">
  <constant  value="cat"/>
</data>
```

__Ausgabe__

```
{litA: cat}
```

</details>

<details>
  <summary>Example <b>FIX</b></summary>
__Eingabe__

```
{litA: Alf}
```

__FIX-Definition__

```
do map("litA")
  constant("cat")
end
```

__Ausgabe__

```
{litA: cat}
```

</details>

_________

#### timestamp

[timestamp](https://github.com/metafacture/metafacture-core/tree/master/metamorph/src/main/java/org/metafacture/metamorph/functions/Timestamp.java) replaces value with recent timestamp. Without attributes `timestamp` creates a default unix timestamp.
With attributes `format`, `timezone` and `language` you can create individual formatted timestamps.

- `format`: format of the timestamp as specified in [java.text.SimpleDateFormat](https://docs.oracle.com/javase/8/docs/api/java/text/SimpleDateFormat.html)
- `timezone`: timezone as specified  in [java.util.TimeZone](https://docs.oracle.com/javase/8/docs/api/java/util/TimeZone.html)  (default: UTC)
- `language`: area conventions specified in [java.util.Locale](https://docs.oracle.com/javase/8/docs/api/java/util/Locale.html)

<details>
  <summary>Example <b>Morph</b></summary>
__Eingabe__

```
{}
```

__Morph-Definition__

```xml
<data source="_id" name "timestamp">
  <timestamp format="yyyy-MM-dd HH:mm:ss" timezone="GMT+02:00" language="de"/>
</data>
```

__Ausgabe__

```
{zeitstempel: 2018-08-24 17\:59\:18}
```

</details>

<details>
  <summary>Example <b>FIX</b></summary>
__Eingabe__

```
{}
```

__FIX-Definition__

TODO: How to use it in FIX?

```
do map("_id_")
  timestamp()
end
```

__Ausgabe__

```
{zeitstempel: 2018-08-24 17\:59\:18}
```

</details>

_________

#### substring

[substring](https://github.com/metafacture/metafacture-core/tree/master/metamorph/src/main/java/org/metafacture/metamorph/functions/Timestamp.java) extraction of a substring. The extracted part is defined by attributes - `start` and `end` - stating the indixnumber of the letter.
If you set the `end="0"` the remaining string will be provided.

<details>
  <summary>Example <b>Morph</b></summary>
__Eingabe__

```
{litA: metamorph}
```

__Morph-Definition__

```xml
<data source="litA">
  <substring start="3" end="6"/>
</data>
```

__Ausgabe__

```
{litA: amor}
```

</details>

<details>
  <summary>Example <b>FIX</b></summary>
__Eingabe__

```
{litA: metamorph}
```

__FIX-Definition__

```
do map("litA")
  substring(start: "3", end: "6")
end
```

__Ausgabe__

```
{litA: amor}
```

</details>

_________

#### replace/setreplace

[replace](https://github.com/metafacture/metafacture-core/tree/master/metamorph/src/main/java/org/metafacture/metamorph/functions/Replace.java) overwrites a pattern (`pattern`) with a string (`with`). You can use [Java regex Pattern](http://docs. oracle.com/javase/6/docs/api/java/util/regex/Pattern.html) when defining the pattern attribute.
Not matching values will be passed on without changes.

<details>
  <summary>Example <b>Morph</b></summary>
__Eingabe__

```
{litA: abcde, litA: fghij}
```

__Morph-Definition__

```xml
<data source="litA">
  <replace  pattern="[ace]"  with="X"/>
</data>
```

__Ausgabe__

```
{litA: XbXdX, litA: fghij}
```

</details>

<details>
  <summary>Example <b>FIX</b></summary>
__Eingabe__

```
{litA: abcde, litA: fghij}
```

__FIX-Definition__

```
do map("litA")
  replace(pattern:"[ace]", with="X")
end
```

__Ausgabe__

```
{litA: XbXdX, litA: fghij}
```

</details>

[setreplace](https://github.com/metafacture/metafacture-core/tree/master/metamorph/src/main/java/org/metafacture/metamorph/functions/SetReplace.java) replaces multiple strings. The mapping from string to replacement is defined in a map, just as done in the lookup function. See also Data Lookup. Setreplace does not support regex if you use in a seperate map.

<details>
  <summary>Example <b>Morph</b></summary>
__Eingabe__

```
{litA: eins, litA: zwei, litA: drei, litA: vier, litA: fünf}
```

__Morph-Definition__

```xml
<data source="litA" name="zahl">
  <setreplace>
    <entry name="eins" value="1"/>
    <entry name="zwei" value="2"/>
    <entry name="drei" value="3"/>
    <entry name="vier" value="4"/>
  </setreplace>
</data>
```

__Ausgabe__

```
{litA: 1, litA: 2, litA: 3, litA: 4, litA: fünf}
```

</details>

<details>
  <summary>Example <b>FIX</b></summary>
__Eingabe__

```
{litA: eins, litA: zwei, litA: drei, litA: vier, litA: fünf}
```

__FIX-Definition__

TODO: show fix form

```
do map("litA")
  setreplace(...)
end
```

__Ausgabe__

```
{litA: 1, litA: 2, litA: 3, litA: 4, litA: fünf}
```

</details>

_________

#### lookup

[lookup](https://github.com/metafacture/metafacture-core/tree/master/metamorph/src/main/java/org/metafacture/metamorph/functions/Lookup.java) maps and replaces based on a data table. With the attribute `in` you can define a separate concordance map. In contrast to [setreplace](#replacesetreplace) it only takes static values and does not allow regex. For that see also [Data Lookup](#data-lookup). With the attribute `default` you can define a string to replace values that can't be mapped.

TODO: Does `lookup` pass trough values that do not map even without `default`?

<details>
  <summary>Example <b>Morph</b></summary>
__Eingabe__

```
{kanton: Waadt, kanton: Wallis, kanton: Jura, kanton: Genf}
```

__Morph-Definition__

```xml
<data source="kanton" name="hauptort">
  <lookup default="nicht bekannt">
    <entry name="Wallis" value="Sion"/>
    <entry name="Jura" value="Delémont"/>
    <entry name="Waadt" value="Lausanne"/>
  </lookup>
</data>
```

__Ausgabe__

```
{hauptort: Lausanne, hauptort: Sion, hauptort: Delémont, hauptort: nicht bekannt}
```

</details>

<details>
  <summary>Example <b>FIX</b></summary>
__Eingabe__

```
{kanton: Waadt, kanton: Wallis, kanton: Jura, kanton: Genf}
```

__FIX-Definition__

TODO: show fix form

```
do map("kanton", "hauptort")
  lookup(...)
end
```

__Ausgabe__

```
{hauptort: Lausanne, hauptort: Sion, hauptort: Delémont, hauptort: nicht bekannt}
```

</details>

_________

#### case

[case](https://github.com/metafacture/metafacture-core/tree/master/metamorph/src/main/java/org/metafacture/metamorph/functions/Case.java) transforms value in all upper or lower case (with attribute `to="upper"` or `to"lower"`).

TODO: What is the `language` attribute doing?

<details>
  <summary>Example <b>Morph</b></summary>
__Eingabe__

```
{buch: kitabı}
```

__Morph-Definition__

```xml
<data source="buch">
  <case to="upper" language="tr"/>
</data>
```

__Ausgabe__

```
{buch: KİTABI}
```

</details>

<details>
  <summary>Example <b>FIX</b></summary>
__Eingabe__

```
{buch: kitabı}
```

__FIX-Definition__


```
do map("buch")
  case(to:"upper", language:"tr")
end
```

__Ausgabe__

```
{buch: KİTABI}
```

</details>

_________

#### trim

[trim](https://github.com/metafacture/metafacture-core/tree/master/metamorph/src/main/java/org/metafacture/metamorph/functions/Trim.java) deletes spaces at the beginning and the end of the received value.

<details>
  <summary>Example <b>Morph</b></summary>
__Eingabe__

```
{litA: '  luftig   '}
```

__Morph-Definition__

```xml
<data source="litA">
  <trim />
</data>
```

__Ausgabe__

```
{litA: luftig}
```

</details>

<details>
  <summary>Example <b>FIX</b></summary>
__Eingabe__

```
{litA: '  luftig   '}
```

__FIX-Definition__


```
do map("litA")
  trim()
end
```

__Ausgabe__

```
{litA: luftig}
```

</details>

_________


#### normalize-utf8

[normalize-utf8](https://github.com/metafacture/metafacture-core/tree/master/metamorph/src/main/java/org/metafacture/metamorph/functions/NormalizeUTF8.java) does UTF-8 normalization. It brings Umlauts into canonical form e.g. *ä* that is build from the diacrit *¨ + a* to ä. For more see [here](http://www.unicode.org/reports/tr15/tr15-23.html).

<details>
  <summary>Example <b>Morph</b></summary>
__Eingabe__

```
{litA: ä }
```

__Morph-Definition__

```xml
<data source="litA">
  <normalize-utf8 />
</data>
```

__Ausgabe__

```
{litA: ä}
```

</details>

<details>
  <summary>Example <b>FIX</b></summary>
__Eingabe__

```
{litA: ä }
```

__FIX-Definition__


```
do map("litA")
  normalize-utf8()
end
```

__Ausgabe__

```
{litA: ä}
```

</details>

_________

#### dateformat

[dateformat](https://github.com/metafacture/metafacture-core/tree/master/metamorph/src/main/java/org/metafacture/metamorph/functions/DateFormat.java) models dates with following attributes:

- `inputformat` sets the format of the input date as specified in [java.text.SimpleDateFormat](https://docs.oracle.com/javase/8/docs/api/java/text/SimpleDateFormat.html)
- `outputformat` sets the format of the output date as `"FULL"`, `"LONG"` (default), `"MEDIUM"` oder `"SHORT"` (see [java.text.DateFormat](https://docs.oracle.com/javase/8/docs/api/java/text/DateFormat.html))
- `era`: `"AUTO"` (default), `"AD"` oder `"BC"`
- ``removeLeadingZeros` (default: `false`)
- `language`: area conventions specified in [java.util.Locale](https://docs.oracle.com/javase/8/docs/api/java/util/Locale.html)

TODO: swissbib report a bug: removeLeadingZeros="true" entfernte in unseren Tests fälschlicherweise auch Nulle in vierstelligen Jahreszahlen, das Resultat war also bspw. 218 anstelle von 2018!  https://swissbib.gitlab.io/metamorph-doku/literale/transformationen/

<details>
  <summary>Example <b>Morph</b></summary>
__Eingabe__

```
{heute: 2018-08-26 }
```

__Morph-Definition__

```xml
<data source="heute">
  <dateformat inputformat="yyyy-MM-dd" outputformat="FULL" era="AD" removeLeadingZeros="false" language="de"/>
</data>
```
TODO: is the upper case with MM intentional?

__Ausgabe__

```
{heute:Sonntag\, 26. August 2018}
```

</details>

<details>
  <summary>Example <b>FIX</b></summary>
__Eingabe__

```
{heute: 2018-08-26 }
```

__FIX-Definition__


```
do map("heute")
  dateformat(inputformat:"yyyy-mm-dd", outputformat:"FULL", era:"AD", removeLeadingZeros:"false", language:"de")
end
```

__Ausgabe__

```
{heute:Sonntag\, 26. August 2018}
```

</details>

_________

#### split

[split](https://github.com/metafacture/metafacture-core/tree/master/metamorph/src/main/java/org/metafacture/metamorph/functions/Split.java) separates values in substrings based on a regexp defined in attribute `delimiter`

TODO: Are values that are not split passed on?

<details>
  <summary>Example <b>Morph</b></summary>
__Eingabe__

```
{litA: Oahu,Hawaii,Maui}
```

__Morph-Definition__

```xml
<data source="litA">
  <split delimiter=","/>
</data>
```

__Ausgabe__

```
{litA: Oahu, litA: Hawauu, litA: Maui}
```

</details>

<details>
  <summary>Example <b>FIX</b></summary>
__Eingabe__

```
{litA: Oahu,Hawaii,Maui}
```

__FIX-Definition__


```
do map("litA")
  split(delimiter: ",")
end
```

__Ausgabe__

```
{litA: Oahu, litA: Hawauu, litA: Maui}
```

</details>

_________

#### isbn

[isbn](https://github.com/metafacture/metafacture-core/tree/master/metamorph/src/main/java/org/metafacture/metamorph/functions/ISBN.java) cleaning, checkdigit verification and transformation between ISBN 10 and ISBN 13. ISBN transformation defined in attribute `to`. Also possible to clean ISBN from hyphen. `to="clean"`.
It is also possible to control the ISBN checkDigit with the attribute `verifyCeckDigit` and provide a own errorString (`errorString`).

<details>
  <summary>Example <b>Morph</b></summary>
__Eingabe__

```
{isbn: 978-3-89401-810}
```

__Morph-Definition__

```xml
<data source="isbn">
  <isbn to="isbn10" verifyCheckDigit="true" errorString="ungültig"/>
</data>
```

__Ausgabe__

```
{isbn: ungültig}
```

</details>

<details>
  <summary>Example <b>FIX</b></summary>
__Eingabe__

```
{isbn: 978-3-89401-810}
```

__FIX-Definition__

```
do map("isbn")
  isbn(to:"isbn10", verifyCheckDigit:"true", errorString:"ungültig")
end
```

__Ausgabe__

```
{isbn: ungültig}
```

</details>

_________

#### urlencode

[urlencode](https://github.com/metafacture/metafacture-core/tree/master/metamorph/src/main/java/org/metafacture/metamorph/functions/URLEncode.java) transforms values to URL demands.

<details>
  <summary>Example <b>Morph</b></summary>
__Eingabe__

```
{author: Judith Shklar}
```

__Morph-Definition__

```xml
<data source="author" name="search">
  <urlencode />
  <compose prefix="https://lobid.org/resources/search?q="/>
</data>
```

__Ausgabe__

```
{search: https://lobid.org/resources/search?q=Judith+Shklar}
```

</details>

<details>
  <summary>Example <b>FIX</b></summary>
__Eingabe__

```
{author: Judith Shklar}
```

__FIX-Definition__

```
do map("author", "search")
  urlcode()
  compose(prefix:"https://lobid.org/resources/search?q=")
end
```

__Ausgabe__

```
{search: https://lobid.org/resources/search?q=Judith+Shklar}
```

</details>

_________

#### htmlanchor

[htmlanchor](https://github.com/metafacture/metafacture-core/tree/master/metamorph/src/main/java/org/metafacture/metamorph/functions/HtmlAnchor.java) creates an HTML anchor tag. It demands a `prefix` attribute. It also can have a `postfix` and/or a title `attribute`.

<details>
  <summary>Example <b>Morph</b></summary>
__Eingabe__

```
{article: my special day}
```

__Morph-Definition__

```xml
<data source="article" name="anchor">
  <urlencode />
  <htmlanchor prefix="https://example.com/" title="My very very special day"/>
</data>
```

__Ausgabe__

```
{anchor: <a href="https\://example.com/my+special+day">My very very special day</a>}
```

</details>

<details>
  <summary>Example <b>FIX</b></summary>
__Eingabe__

```
{article: my special day}
```

__FIX-Definition__

```
do map("article", "anchor")
  urlcode()
  htmlanchor(prefix:"https://example.com/", title: "My very very special day")
end
```

__Ausgabe__

```
{anchor: <a href="https\://example.com/my+special+day">My very very special day</a>}
```

</details>

_________

#### switch-name-value

[switch-name-value](https://github.com/metafacture/metafacture-core/tree/master/metamorph/src/main/java/org/metafacture/metamorph/functions/SwitchNameValue.java) exchanges name and value.

<details>
  <summary>Example <b>Morph</b></summary>
__Eingabe__

```
{litA: wertB}
```

__Morph-Definition__

```xml
<data source="litA" >
  <switch-name-value/>
</data>
```

__Ausgabe__

```
{wertB: litA}
```

</details>

<details>
  <summary>Example <b>FIX</b></summary>
__Eingabe__

```
{litA: wertB}
```

__FIX-Definition__

```
do map("litA")
  switch-name-value()
end
```

__Ausgabe__

```
{wertB: litA}
```

</details>

_________

#### script and java

TODO: fill out this part.

[script](https://github.com/metafacture/metafacture-core/tree/master/metamorph/src/main/java/org/metafacture/metamorph/functions/Script.java) and [java](LINK) process the value with a JavaScript function or a Java class. See Integration of Java and JavaScript.

`script` uses attribute `invoke` for an Javascript-function in an external file (attribute `file`) to pass the values on.

`java` TODO..

#### count

[count](https://github.com/metafacture/metafacture-core/tree/master/metamorph/src/main/java/org/metafacture/metamorph/functions/Count.java) counts literals and gives the actual counted number with each literal. To give back only the last counted number see the cookbook. (TODO)

<details>
  <summary>Example <b>Morph</b></summary>
__Eingabe__

```
{litA: wert1, litA: wert2}
```

__Morph-Definition__

```xml
<data source="litA" >
  <count/>
</data>
```

__Ausgabe__

```
{litA: 1, litA: 2}
```

</details>

<details>
  <summary>Example <b>FIX</b></summary>
__Eingabe__

```
{litA: wert1, litA: wert2}
```

__FIX-Definition__

```
do map("litA")
  count()
end
```

__Ausgabe__

```
{litA: 1, litA: 2}
```

</details>

_________

### Filter

The following passage introduces the different literal functions, which filter literal values based on certain rules.

 As all literal function filter functions are inserted in the `<data>`-Element. In the FIX language the literal functions are inserted in an `do map ([source], [name]) ... end`.

__whitelist__

Filtering based on a whitelist. Allowed values are stated as following `<entry name=[value]/>`. You can also use an external [Data Lookup](#data-lookup). 



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



### Data lookup

__maps__

__javamap__

__filemap__

__restmap__

__sqlmap__

__jndisqlmap__

## Collectors

### List of collectors

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

### Process controll

__flush__

__reset__

__sameEntity__


### if conditionals


## Modularise

__recursion__

__variable__

__makros__

__includes__