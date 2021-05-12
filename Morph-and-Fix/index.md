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

Note: Wildcards are allowed in the `source` name. You also can use variants with square brackets `source="bee[rt]"` You can use multiple names as source by separating the names with `|` e.g. `source="beer|coffee"`


<details>
  <summary>Example <b>Morph</b></summary>
__input__

```
{litA: Alf}
```

__Morph-Definition__

```xml
<data source="litA" />
```

__output__

```
{litA: Alf}
```

</details>


<details>
  <summary>Example <b>FIX</b></summary>
__input__

```
{litA: Alf}
```

__FIX-Definition__

```
map ("litA")
```
__output__

```
{litA: Alf}
```

</details>

It also can have a `name` attribute to rename the key.

<details>
  <summary>Example <b>Morph</b></summary>
__input__

```
{litA: Alf}
```

__Morph-Definition__

```xml
<data source="litA" name="Alien" />
```

__output__

```
{Alien: Alf}
```

</details>

<details>
  <summary>Example <b>FIX</b></summary>
__input__

```
{litA: Alf}
```

__FIX-Definition__

```
map ("litA", "Alien")
```

__output__

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
__input__

```
{litA: Alf}
```

__Morph-Definition__

```xml
<data source="litA">
  <compose  prefix="Hello,  "  postfix="!"/>
</data>
```

__output__

```
{litA: Hallo, Alf!}
```

</details>

<details>
  <summary>Example <b>FIX</b></summary>
__input__

```
{litA: Alf}
```

__FIX-Definition__

```
do map("litA")
  compose(prefix: 'Hello' postfix: '!')
end
```

__output__

```
{litA: Hallo, Alf!}
```

</details>

_________

#### constant

[constant](https://github.com/metafacture/metafacture-core/tree/master/metamorph/src/main/java/org/metafacture/metamorph/functions/Constant.java) replaces the value with a constant string. This value is defined in the attribute `value`

<details>
  <summary>Example <b>Morph</b></summary>
__input__

```
{litA: Alf}
```

__Morph-Definition__

```xml
<data source="litA">
  <constant  value="cat"/>
</data>
```

__output__

```
{litA: cat}
```

</details>

<details>
  <summary>Example <b>FIX</b></summary>
__input__

```
{litA: Alf}
```

__FIX-Definition__

```
do map("litA")
  constant("cat")
end
```

__output__

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
__input__

```
{}
```

__Morph-Definition__

```xml
<data source="_id" name "timestamp">
  <timestamp format="yyyy-MM-dd HH:mm:ss" timezone="GMT+02:00" language="de"/>
</data>
```

__output__

```
{zeitstempel: 2018-08-24 17\:59\:18}
```

</details>

<details>
  <summary>Example <b>FIX</b></summary>
__input__

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

__output__

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
__input__

```
{litA: metamorph}
```

__Morph-Definition__

```xml
<data source="litA">
  <substring start="3" end="6"/>
</data>
```

__output__

```
{litA: amor}
```

</details>

<details>
  <summary>Example <b>FIX</b></summary>
__input__

```
{litA: metamorph}
```

__FIX-Definition__

```
do map("litA")
  substring(start: "3", end: "6")
end
```

__output__

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
__input__

```
{litA: abcde, litA: fghij}
```

__Morph-Definition__

```xml
<data source="litA">
  <replace  pattern="[ace]"  with="X"/>
</data>
```

__output__

```
{litA: XbXdX, litA: fghij}
```

</details>

<details>
  <summary>Example <b>FIX</b></summary>
__input__

```
{litA: abcde, litA: fghij}
```

__FIX-Definition__

```
do map("litA")
  replace(pattern:"[ace]", with="X")
end
```

__output__

```
{litA: XbXdX, litA: fghij}
```

</details>

[setreplace](https://github.com/metafacture/metafacture-core/tree/master/metamorph/src/main/java/org/metafacture/metamorph/functions/SetReplace.java) replaces multiple strings. The mapping from string to replacement is defined in a map, just as done in the lookup function. See also Data Lookup. Setreplace does not support regex if you use in a seperate map.

<details>
  <summary>Example <b>Morph</b></summary>
__input__

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

__output__

```
{litA: 1, litA: 2, litA: 3, litA: 4, litA: fünf}
```

</details>

<details>
  <summary>Example <b>FIX</b></summary>
__input__

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

__output__

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
__input__

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

__output__

```
{hauptort: Lausanne, hauptort: Sion, hauptort: Delémont, hauptort: nicht bekannt}
```

</details>

<details>
  <summary>Example <b>FIX</b></summary>
__input__

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

__output__

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
__input__

```
{buch: kitabı}
```

__Morph-Definition__

```xml
<data source="buch">
  <case to="upper" language="tr"/>
</data>
```

__output__

```
{buch: KİTABI}
```

</details>

<details>
  <summary>Example <b>FIX</b></summary>
__input__

```
{buch: kitabı}
```

__FIX-Definition__


```
do map("buch")
  case(to:"upper", language:"tr")
end
```

__output__

```
{buch: KİTABI}
```

</details>

_________

#### trim

[trim](https://github.com/metafacture/metafacture-core/tree/master/metamorph/src/main/java/org/metafacture/metamorph/functions/Trim.java) deletes spaces at the beginning and the end of the received value.

<details>
  <summary>Example <b>Morph</b></summary>
__input__

```
{litA: '  luftig   '}
```

__Morph-Definition__

```xml
<data source="litA">
  <trim />
</data>
```

__output__

```
{litA: luftig}
```

</details>

<details>
  <summary>Example <b>FIX</b></summary>
__input__

```
{litA: '  luftig   '}
```

__FIX-Definition__


```
do map("litA")
  trim()
end
```

__output__

```
{litA: luftig}
```

</details>

_________


#### normalize-utf8

[normalize-utf8](https://github.com/metafacture/metafacture-core/tree/master/metamorph/src/main/java/org/metafacture/metamorph/functions/NormalizeUTF8.java) does UTF-8 normalization. It brings Umlauts into canonical form e.g. *ä* that is build from the diacrit *¨ + a* to ä. For more see [here](http://www.unicode.org/reports/tr15/tr15-23.html).

<details>
  <summary>Example <b>Morph</b></summary>
__input__

```
{litA: ä }
```

__Morph-Definition__

```xml
<data source="litA">
  <normalize-utf8 />
</data>
```

__output__

```
{litA: ä}
```

</details>

<details>
  <summary>Example <b>FIX</b></summary>
__input__

```
{litA: ä }
```

__FIX-Definition__


```
do map("litA")
  normalize-utf8()
end
```

__output__

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
__input__

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

__output__

```
{heute:Sonntag\, 26. August 2018}
```

</details>

<details>
  <summary>Example <b>FIX</b></summary>
__input__

```
{heute: 2018-08-26 }
```

__FIX-Definition__


```
do map("heute")
  dateformat(inputformat:"yyyy-mm-dd", outputformat:"FULL", era:"AD", removeLeadingZeros:"false", language:"de")
end
```

__output__

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
__input__

```
{litA: Oahu,Hawaii,Maui}
```

__Morph-Definition__

```xml
<data source="litA">
  <split delimiter=","/>
</data>
```

__output__

```
{litA: Oahu, litA: Hawauu, litA: Maui}
```

</details>

<details>
  <summary>Example <b>FIX</b></summary>
__input__

```
{litA: Oahu,Hawaii,Maui}
```

__FIX-Definition__


```
do map("litA")
  split(delimiter: ",")
end
```

__output__

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
__input__

```
{isbn: 978-3-89401-810}
```

__Morph-Definition__

```xml
<data source="isbn">
  <isbn to="isbn10" verifyCheckDigit="true" errorString="ungültig"/>
</data>
```

__output__

```
{isbn: ungültig}
```

</details>

<details>
  <summary>Example <b>FIX</b></summary>
__input__

```
{isbn: 978-3-89401-810}
```

__FIX-Definition__

```
do map("isbn")
  isbn(to:"isbn10", verifyCheckDigit:"true", errorString:"ungültig")
end
```

__output__

```
{isbn: ungültig}
```

</details>

_________

#### urlencode

[urlencode](https://github.com/metafacture/metafacture-core/tree/master/metamorph/src/main/java/org/metafacture/metamorph/functions/URLEncode.java) transforms values to URL demands.

<details>
  <summary>Example <b>Morph</b></summary>
__input__

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

__output__

```
{search: https://lobid.org/resources/search?q=Judith+Shklar}
```

</details>

<details>
  <summary>Example <b>FIX</b></summary>
__input__

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

__output__

```
{search: https://lobid.org/resources/search?q=Judith+Shklar}
```

</details>

_________

#### htmlanchor

[htmlanchor](https://github.com/metafacture/metafacture-core/tree/master/metamorph/src/main/java/org/metafacture/metamorph/functions/HtmlAnchor.java) creates an HTML anchor tag. It demands a `prefix` attribute. It also can have a `postfix` and/or a title `attribute`.

<details>
  <summary>Example <b>Morph</b></summary>
__input__

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

__output__

```
{anchor: <a href="https\://example.com/my+special+day">My very very special day</a>}
```

</details>

<details>
  <summary>Example <b>FIX</b></summary>
__input__

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

__output__

```
{anchor: <a href="https\://example.com/my+special+day">My very very special day</a>}
```

</details>

_________

#### switch-name-value

[switch-name-value](https://github.com/metafacture/metafacture-core/tree/master/metamorph/src/main/java/org/metafacture/metamorph/functions/SwitchNameValue.java) exchanges name and value.

<details>
  <summary>Example <b>Morph</b></summary>
__input__

```
{litA: valueB}
```

__Morph-Definition__

```xml
<data source="litA" >
  <switch-name-value/>
</data>
```

__output__

```
{valueB: litA}
```

</details>

<details>
  <summary>Example <b>FIX</b></summary>
__input__

```
{litA: valueB}
```

__FIX-Definition__

```
do map("litA")
  switch-name-value()
end
```

__output__

```
{valueB: litA}
```

</details>

_________

#### script and java

TODO: fill out this part.

[script](https://github.com/metafacture/metafacture-core/tree/master/metamorph/src/main/java/org/metafacture/metamorph/functions/Script.java) and [java](LINK) process the value with a JavaScript function or a Java class. See Integration of Java and JavaScript.

`script` uses attribute `invoke` for an Javascript-function in an external file (attribute `file`) to pass the values on.

`java` TODO..

_________

#### count

[count](https://github.com/metafacture/metafacture-core/tree/master/metamorph/src/main/java/org/metafacture/metamorph/functions/Count.java) counts literals and gives the actual counted number with each literal. To give back only the last counted number see the cookbook. (TODO)

<details>
  <summary>Example <b>Morph</b></summary>
__input__

```
{litA: value1, litA: value2}
```

__Morph-Definition__

```xml
<data source="litA" >
  <count/>
</data>
```

__output__

```
{litA: 1, litA: 2}
```

</details>

<details>
  <summary>Example <b>FIX</b></summary>
__input__

```
{litA: value1, litA: value2}
```

__FIX-Definition__

```
do map("litA")
  count()
end
```

__output__

```
{litA: 1, litA: 2}
```

</details>

_________

### Filter

The following passage introduces the different literal functions, which filter literal values based on certain rules.

 As all literal function filter functions are inserted in the `<data>`-Element. In the FIX language the literal functions are inserted in an `do map ([source], [name]) ... end`. For more see [here](#transform)

 _________

#### whitelist

[whitelist](https://github.com/metafacture/metafacture-core/blob/master/metamorph/src/main/java/org/metafacture/metamorph/functions/WhiteList.java) filters based on a listed values that are allowed. Allowed values are stated as following `<entry name=[value]/>`. You can also use an external [Data Lookup](#data-lookup). 

TODO: How does this work in FIX

<details>
  <summary>Example <b>Morph</b></summary>
__input__

```
{litA: allowed, litA: not allowed, litA: not permitted, litA: forbidden, litA: accepted}
```

__Morph-Definition__

```xml
<data source="litA">
  <whitelist>
    <entry name="allowed"/>
    <entry name="accepted"/>
  </whitelist>
</data>
```

__output__

```
{litA: allowed, litA: accepted}
```

</details>

<details>
  <summary>Example <b>FIX</b></summary>
__input__

TODO: describe example in FIX

```
{litA: allowed, litA: not allowed, litA: not permitted, litA: forbidden, litA: accepted}
```

__FIX-Definition__

```
do map("litA")
  whitelist( ... TODO)
end
```

__output__

```
{litA: allowed, litA: accepted}
```

</details>

 _________

#### blacklist


[blacklist](https://github.com/metafacture/metafacture-core/blob/master/metamorph/src/main/java/org/metafacture/metamorph/functions/BlackList.java) filters based on a listed values that are blocked. Blocked values are stated as following `<entry name=[value]/>`. You can also use an external [Data Lookup](#data-lookup).

<details>
  <summary>Example <b>Morph</b></summary>
__input__

```
{litA: allowed, litA: not allowed, litA: not permitted, litA: forbidden, litA: accepted}
```

__Morph-Definition__

```xml
<data source="litA">
  <blacklist>
    <entry name="not allowed"/>
    <entry name="not permitted"/>
    <entry name="forbidden"/>
  </blacklist>
</data>
```

__output__

```
{litA: allowed, litA: accepted}
```

</details>

<details>
  <summary>Example <b>FIX</b></summary>
__input__

TODO: describe example in FIX

```
{litA: allowed, litA: not allowed, litA: not permitted, litA: forbidden, litA: accepted}
```

__FIX-Definition__

```
do map("litA")
  blacklist( ... TODO)
end
```

__output__

```
{litA: allowed, litA: accepted}
```

</details>

 _________

#### contains / not-contains

[contains](https://github.com/metafacture/metafacture-core/blob/master/metamorph/src/main/java/org/metafacture/metamorph/functions/Contains.java) filters values when they have a certain substring stated in `string`.

TODO: Do they work with regex? Are they case-sensitive?

<details>
  <summary>Example <b>Morph</b></summary>
__input__

```
{litA: hamster in the house, litA: turtle in the house}
```

__Morph-Definition__

```xml
<data source="litA">
  <contains  string="hamster"/>
</data>
```

__output__

```
{litA: hamster in the house}
```

</details>

<details>
  <summary>Example <b>FIX</b></summary>
__input__

```
{litA: hamster in the house, litA: turtle in the house}
```

__FIX-Definition__

```
do map("litA")
  contains(string: "hamster")
end
```

__output__

```
{litA: hamster in the house}
```

</details>

[not-contains](https://github.com/metafacture/metafacture-core/blob/master/metamorph/src/main/java/org/metafacture/metamorph/functions/NotContains.java) blocks values when they have a certain substring stated in `string`.

TODO: Do they work with regex? Are they case-sensitive?

<details>
  <summary>Example <b>Morph</b></summary>
__input__

```
{litA: hamster in the house, litA: turtle in the house}
```

__Morph-Definition__

```xml
<data source="litA">
  <not-contains  string="hamster"/>
</data>
```

__output__

```
{litA: turtle in the house}
```

</details>

<details>
  <summary>Example <b>FIX</b></summary>
__input__

```
{litA: hamster in the house, litA: turtle in the house}
```

__FIX-Definition__

```
do map("litA")
  not-contains(string: "hamster")
end
```

__output__

```
{litA: turtle in the house}
```

</details>

 _________

__equals / not-equals__

[equals](https://github.com/metafacture/metafacture-core/blob/master/metamorph/src/main/java/org/metafacture/metamorph/functions/Equals.java) passes trough values that are equal to the stated (`string`).

TODO: Do they work with regex? Are they case-sensitive?

<details>
  <summary>Example <b>Morph</b></summary>
__input__

```
{litA: hamster in the house, litA: turtle in the house}
```

__Morph-Definition__

```xml
<data source="litA">
  <not-contains  string="hamster"/>
</data>
```

__output__

```
{litA: turtle in the house}
```

</details>

<details>
  <summary>Example <b>FIX</b></summary>
__input__

```
{litA: hamster in the house, litA: turtle in the house}
```

__FIX-Definition__

```
do map("litA")
  not-contains(string: "hamster")
end
```

__output__

```
{litA: turtle in the house}
```

</details>

 _________

#### occurence

[occurence](https://github.com/metafacture/metafacture-core/blob/master/metamorph/src/main/java/org/metafacture/metamorph/functions/Occurrence.java) filters based on how often a literal appears and passes the defined occurences on.

This can be defined in the `only` attributes. Additionally you can use `moreThan [n]` and `lessTan [n]` to specify a specific number of occurences.

- `only="moreThan [n]"` sends literals if they occure n+x times. But it cut the appearences before it reaches n.
- `only="lessThan [n]"` sends literals that appear up to n times.
- `only` without additional filter sends literals if they occure exactly n times and sends the instance at position n.

TODO: Specify if `lessThan` blocks the occurences  \< n literals if they have more than n instances in total.

Tip: You can use two occurence filters after each other to send only a certain part of occurences. First: <occurrence only="moreThan n"/> and then <occurrence only="lessThan m"/>.

<details>
  <summary>Example <b>Morph</b></summary>
__input__

```
{litA: hamster, litA: turtle, litA: butterfly, litA: cat}
```

__Morph-Definition__

```xml
<data source="litA">
  <occurence  only="2"/>
</data>
```

__output__

```
{litA: turtle}
```


__Morph-Definition__

```xml
<data source="litA">
  <occurence  only="moreThan 2"/>
</data>
```

__output__

```
{litA: butterfly, litA: cat}
```

</details>

<details>
  <summary>Example <b>FIX</b></summary>
__input__

```
{litA: hamster, litA: turtle, litA: butterfly, litA: cat}
```

__FIX-Definition__

```
do map("litA")
  occurence(only: "2")
end
```

__output__

```
{litA: turtle}
```The following paragraphs briefly introduce the different collectors available. 

__output__

```
{litA: butterfly, litA: cat}
```

</details>

 _________

#### regexp

[regexp](https://github.com/metafacture/metafacture-core/tree/master/metamorph/src/main/java/org/metafacture/metamorph/functions/Regexp.java) filters values based on matching them with regular expressions specified in `match`. It returns the first occurrence of a pattern. The pattern is a Java regex Pattern (see http://docs.oracle.com/javase/6/docs/api/java/util/regex/Pattern.html). Additinally it can transform the matched pattern defined in `format`. This also supports capture groups which should be state in `format` with ${1} (TODO: also could be or $1)

<details>
  <summary>Example <b>Morph</b></summary>
__input__

```
{litA: hamster, litA: turtle, litA: butterfly, litA: cat}
```

__Morph-Definition__

```xml
<data source="litA">
  <regexp  match="t.r"/>
</data>
```

__output__

```
{litA: hamster, litA: turtle, litA: butterfly}
```

__input__

```
{litA: from  1789  to  1900}
```

__Morph-Definition__

```xml
<data source="litA">
  <regexp  match="(\d\d\d\d)  to  (\d\d\d\d)"  format="${1}-${2}"/>
</data>
```

__output__

```
{litA: 1789-1900}
```

</details>

<details>
  <summary>Example <b>FIX</b></summary>
__input__

```
{litA: hamster, litA: turtle, litA: butterfly, litA: cat}
```

__FIX-Definition__

```
do map("litA")
  regex(match: "t.r")
end
```

__output__

```
{litA: hamster, litA: turtle, litA: butterfly}
```

__input__

```
{litA: from  1789  to  1900}
```

__FIX-Definition__

```
do map("litA")
  regexp(match:"(\d\d\d\d)  to  (\d\d\d\d)", format:"${1}-${2}
end
```

__output__

```
{litA: butterfly, litA: cat}
```

</details>

 _________

#### unique

[unique](https://github.com/metafacture/metafacture-core/blob/master/metamorph/src/main/java/org/metafacture/metamorph/functions/Unique.java) filters out duplicate literals. With following attributes the scope and the apperance of the duplicates can be specified:

- `in` specifies if the duplicate should be detected only in the same entity (`entity`) or in the whole record (`record`, this is by default)
- `part` defines which parts of a literal muss be equal to count as a duplicate: `name`, `value` (default) or `name-value`


<details>
  <summary>Example <b>Morph</b></summary>
__input__

```
{litA: hamster, litA: hamster, litA: hamster}
```

__Morph-Definition__

```xml
<data source="litA">
  <unique />
</data>
```

__output__

```
{litA: hamster}
```

__input__

```
{litA: valueA, litA: valueB, litA: valueA, litB: valueA }
```

__Morph-Definition__

```xml
<data source="litA">
  <unique part="name-value" />
</data>
```

__output__

```
{litA: valueA, litA: valueB, litB: valueA}
```

</details>

<details>
  <summary>Example <b>FIX</b></summary>
__input__

```
{litA: hamster, litA: hamster, litA: hamster}
```

__FIX-Definition__

```
do map("litA")
  unique()
end
```

__output__

```
{litA: hamster}
```

__input__

```
{litA: valueA, litA: valueB, litA: valueA, litB: valueA }
```

__FIX-Definition__

```
do map("litA")
  unique(part: "name-value")
end
```

__output__

```
{litA: valueA, litA: valueB, litB: valueA}
```

</details>

 _________

### Other

Some function do not fit in the filter/transformation divide.

#### buffer

[buffer](https://github.com/metafacture/metafacture-core/blob/master/metamorph/src/main/java/org/metafacture/metamorph/functions/Buffer.java) withholds literal until the `flushWith`-condition is met. For further details concerning `flushWith" [see](#process-controll).

TODO: https://swissbib.gitlab.io/metamorph-doku/literale/filter/ states a bug concerning the buffer. Examples should be added.

#### add_field (only FIX)

[add_field](https://github.com/metafacture/metafacture-fix/blob/13dad3799d1ba91338f27161da2391b209302abb/org.metafacture.fix/src/main/java/org/metafacture/metamorph/FixFunction.java#L42) creates a field without corresponding key in the source data.

<details>
  <summary>Example <b>FIX</b></summary>
__input__

```
{litA: hamster, litA: hamster, litA: hamster}
```

__FIX-Definition__

```
add_field("type", "learningRessource")
```

__output__

```
{type: learningRessource}
```

</details>

 _________

### Data lookup

A certain group of functions takes a map/dictionary as argument for filter or transform literals: `<lookup>`, `<whitelist>`, `<blacklist>` etc.
This can be done in three different ways:
- in the function
- refrencing to a map in the morph
- refrencing to a map/database outside the morph  

In this section the usage of such maps will be explained. We start with a simple example of data lookup.

#### Local Lookup

Take for instance an operation in which you want to replace values according to a lookup table: Value 'A' maps to 'print', 'B' maps to 'audiovisual' an so forth. This is accomplished by the `<lookup>` function. The lookup table is defined inside the `<lookup>` tag. The following code snippet depicts this situation.


<details>
  <summary>Example <b>Morph</b></summary>

```xml
<data source="002@.0" name="dcterms:format">
  <substring start="0" end="1" />
  <lookup>
    <entry name="A" value="print" />
    <entry name="B" value="audiovisual" />
    <entry name="O" value="online" />
  </lookup>
</data>	
```

</details>

<details>
  <summary>Example <b>FIX</b></summary>
```
do map("002@.0", "dcterms:format")
  substring(start:"0", end="1")
  lookup(
    "A": "print",
    "B": "audiovisual",
    "C": "online"
  )
end
```
</details>

_________

#### map

The same lookup tables may used in different places in a Metamorph definition. To enable reuse, a map/dictionary can be defined separately from the respective lookup function. 
In the following listing the `<lookup>` function refers to the table using the name _material_.

<details>
  <summary>Example <b>Morph</b></summary>
```xml
<data source="litA">
  <lookup in="material">
</data>
```
</details>

<details>
  <summary>Example <b>FIX</b></summary>
```
do map ("litA")
  lookup(in:"material")
end
```

</details>


To define a map, there are different options. All maps that should and can be used in the hole morph need to be stored in a separate `maps` bracket.
TODO: How can this be used in FIX?

With the `<map>` tag the contents of the map can be defined right away in the Metamorph definition.

<details>
  <summary>Example <b>Morph</b></summary>

```xml
<maps>
  <map name="material">
    <entry name="A" value="print" />
    <entry name="B" value="audiovisual" />
    <entry name="O" value="online" />
  </map>
</maps>
```
</details>

TODO: How to do this in FIX?

_________

##### filemap

filemap allows to use mappings stored in a separate text file. Each row then represents a key-value-pair.
Attributes:
- `name` gives the mapping a name (always needed)
- `files` states the path to the file, if multiple separated by comma (always needed)
- `separator` states the sign to differentiate between key and value in a text file (standard: tab: \t) 


<details>
  <summary>Example <b>Morph</b></summary>

```xml
<maps>
  <filemap name="map1" files="maps/MARC-country-codes.txt" separator="\t"/>
</maps>
```

</details>

TODO: How to do this in FIX?

_________

##### javamap

TODO: Better description and how to 

javamap uses a java class, which implements *java.util.Map* (link?)

Attributes:
- `name`  gives the mapping a name (always needed)
- `class` class name with full path (always needed)
- all additional attributes are interpreted as parameters for the java class

The situation might arise that the data cannot be hard-coded in xml/text etc; or at least hard-coding it would be very inconvenient. Imagine we want to resolve author ids (> 5 Million) to author names: Putting all the id-name mappings into the Metamorph definition file or text file is certainly _not_ desirable.
To address this issue, Metamorph allows you to load any class which implements the `Map` interface.

<details>
  <summary>Example <b>Morph</b></summary>

```xml
<maps>
  <javamap name="map-name" class="org.mydomain.MyMap" parameter1="xy" />
</maps>
```
 
</details>


`Map`s can also be added to Metamorph programmatically in Java:

```java
//create a Map. Any object implementing Map<String, String> will do
final Map<String, String> map = new HashMap<String, String>();
map.put("one key", "first String");
map.put("another key", "another String");

//tell metamorph to use it during lookup operations
metamorph.putMap("name of map", map);
```

__Important:__ If your `Map` implementation allocates resources which need to be closed eventually (database connections etc.), let it implement the `java.io.Closeable` interface. Metamorph keeps track of all `Closable`s and will call the `close()` method upon `closeStream()`.

_________

#### restmap

[restmap](https://github.com/metafacture/metafacture-core/tree/master/metamorph/src/main/java/org/metafacture/metamorph/maps/RestMap.java) requests a REST-API

Attributes:
- `name`  gives the mapping a name (always needed)
- `url` URL of the REST-API (always needed)

TODO: example and FIX

_________

__sqlmap__

 [sqlmap](https://github.com/metafacture/metafacture-core/tree/master/metamorph/src/main/java/org/metafacture/metamorph/maps/SqlMap.java) queries a SQL database
 
Attributes:
- `name`  gives the mapping a name (always needed)
- `host` host name (default: localhost)
- `login` user name (always needed)
- `password` (always needed)
- `database` name of the database (always nedded)
- `query` (always needed)
- `driver` database driver (default: com.mysql.jdbc.Driver)


TODO: example and FIX

_________

__jndisqlmap__

 [jndisqlmap](https://github.com/metafacture/metafacture-core/tree/master/metamorph/src/main/java/org/metafacture/metamorph/maps/JndiSqlMap.java) requests a SQL databse via [JNDI](https://de.wikipedia.org/wiki/Java_Naming_and_Directory_Interface)

Attributes:
- `name`  gives the mapping a name (always needed)
- `datasource` (always needed)
- `query` (always needed)

TODO: example and FIX

_________

## Collectors

In the case that an output depends on the values from more then one literal, we need to collect literals.

Collectors can aggregate, filter and combine multiple different literals. Collectors are defined under the <rules> tag, just as <data> tags. <data> tags are be put inside the respective collectors to indicate which literals are to be collected.
Allmost all collector can be:
-  controlled concerning the time and way of the emission with [process controll attributes](#process-controll)
-  can be controlled with [if-conditionals](#if-conditionals) (not supported with FIX)
-  can be nested except `entity`
-  to all types of collectors post-processing steps can be added by using the `postprocess` tag (FIX?)

TODO: specify for FIX

<details>
  <summary>Example <b>Morph</b></summary>

__Morph-Definition__

```xml
<combine name="neu" value="${lastName}, ${firstName}">
  <data source="field1" name="firstName"/>
  <data source="field2" name="lastName"/>
  <postprocess>
    <trim/>
    <case to="upper"/>
  </postprocess>
</combine>
```

</details>

<details>
  <summary>Example <b>FIX</b></summary>

__FIX-Definition__

```
do combine ("neu", "${lastName}, ${firstName}")
  map("field1", "firstName")
  map("field2", "lastName")
  do postprocess
    trim()
    case(to: "upper")
  end
end
```

</details>

The following paragraphs briefly introduce the different collectors available. 

### List of collectors

#### combine

 [combine](https://github.com/metafacture/metafacture-core/tree/master/metamorph/src/main/java/org/metafacture/metamorph/collectors/Combine.java) puts together different literals following a certain value template. Following values overwrite previes values of the same field.
 
 Attributes:
- `name` (always needed): name of the new field
- `value` (always needed): Template. Values can be added with ${fieldname} (TODO: FIX, ohne geschwiffene Klammer?)
- `flushWith` (default: if complete)
- `reset`, (default: false)
- `sameEntity`, (default: false) 


There are several important points to note: By default <combine> waits until at least one value from each `<data>` tag is received. If the collection is not complete on record end, no output is generated. After each output, the state of `<combine>` is reset. If one `<data>` tag receives literals repeatedly before the collection is complete only the last value will be retained.

<details>
  <summary>Example <b>Morph</b></summary>
__input__

```
{028A.a: Shklar, 028A.d: Judtih N.}
```

__Morph-Definition__

```xml
<combine name="gnd:variantNameForThePerson" value="${surname}, ${forename}">
  <data source="028A.a" name="surname" />
  <data source="028A.d" name="forename"/>
</combine>
```

__output__

```
{gnd:variantNameForThePerson: Shklar, Judith N.}
```

</details>

<details>
  <summary>Example <b>FIX</b></summary>
__input__

```
{028A.a: Shklar, 028A.d: Judtih N.}
```

__FIX-Definition__

```
do combine("gnd:variantNameForThePerson", "${surname}, ${forename}")
  map("028A.a", "surname")
  map("028A.d", "forename")
end
```

__output__

```
{028A.a: Shklar, 028A.d: Judtih N.}
```

</details>

 _________


#### concat

[concat](https://github.com/metafacture/metafacture-core/tree/master/metamorph/src/main/java/org/metafacture/metamorph/collectors/Concat.java) collects all received values and concatenates them on record end by default.

Attributes:
- `name` (always needed): name of the new field
- `delimiter` sign to separate values (default: "")
- `prefix`
- `postfix`
- `flushWith`  (default: "record")
- `reset` (default: true)
- `sameEntity` (default: false)


Note: The values are concatenated in the order they appear in the input and not in the order of the data sources.

TODO: Do the attributes in FIX need names?

<details>
  <summary>Example <b>Morph</b></summary>
__input__

```
{data1: a, data2: b, data2: c, data1: d}
```

__Morph-Definition__

```xml
<concat delimiter=", " name="concat" prefix="{" postfix="}">
   <data source="data1" />
   <data source="data2" />
</concat>
```

__output__

```
{concat: \{a,b,c,d\}}
```

</details>

<details>
  <summary>Example <b>FIX</b></summary>
__input__

```
{data1: a, data2: b, data2: c, data1: d}
```

__FIX-Definition__

```
do concat(delimiter: ",", name: "concat", prefix: "{", postfix: "}")
   map("data1")
   map("data2")
end
```

__output__

```
{concat: \{a,b,c,d\}}
```

</details>

 _________


#### entity

[entity](https://github.com/metafacture/metafacture-core/tree/master/metamorph/src/main/java/org/metafacture/metamorph/collectors/Entity.java) creates a new entity or subentity with the collected literals.

Attributes:
- `name` (always needed): name of the new entity
- `flushWith`  (default: if complete)
- `reset` (default: false)
- `sameEntity` (default: false)

Note: An `entity` can nest other collectors but an `entity` can only be nested in another `entity`.


<details>
  <summary>Example <b>Morph</b></summary>
__input__

```
{ farbeA: rot, farbeB: grün, farbeC: blau, farbeD: magenta }
```

__Morph-Definition__

```xml
<entity name="rgb">
  <data source="farbeA"/>
  <data source="farbeB"/>
  <data source="farbeC"/>
</entity>
```

__output__

```
{ rgb { farbeA: rot, farbeB: grün, farbeC: blau }}
```

</details>

<details>
  <summary>Example <b>FIX</b></summary>
__input__

```
{ farbeA: rot, farbeB: grün, farbeC: blau, farbeD: magenta }
```

__FIX-Definition__

```
do entity ("rgb")
  map("farbeA")
  map("farbeB")
  map("farbeC")
end
```

__output__

```
{ rgb { farbeA: rot, farbeB: grün, farbeC: blau }}
```

</details>

 _________

#### choose

[choose](https://github.com/metafacture/metafacture-core/tree/master/metamorph/src/main/java/org/metafacture/metamorph/collectors/Choose.java) collects all received values and emits the most preferred one on record end. Preference is assigned according to the order the data sources appear within the choose tag.

Attributes:
- `name` (always needed): name of the new literal
- TODO: `value` ??? value of the new literal, (documentet in swissbib?? is this true?)
- `flushWith`  (default: "record")
- `reset` (default: "true")

<details>
  <summary>Example <b>Morph</b></summary>
__input__

```
{ data1: important, data2: not so important }
```

__Morph-Definition__

```xml
<choose name="status">
   <data source="data1" />
   <data source="data2" />
</choose>
```

__output__

```
{ status: important }
```

</details>

<details>
  <summary>Example <b>FIX</b></summary>
__input__

```
{ data1: important, data2: not so important }
```

__FIX-Definition__

TODO: specify arguments or attributes

```
do choose("data")
  map("data1")
  map("data2")
end
```

__output__

```
{ status: important }
```

</details>

 _________

#### range

[range](https://github.com/metafacture/metafacture-core/tree/master/metamorph/src/main/java/org/metafacture/metamorph/collectors/Range.java) reads two following literals as intigers, which define the start and endpoint of a numerical series.

Attributes:
- `name` (always needed): name of the new literal
- `increment` (default: 1)
- `flushWith`  (default: "record")
- `reset` (default: "false")
- `sameEntity` (default: "false")

<details>
  <summary>Example <b>Morph</b></summary>
__input__

```
{ beginning: 0, end: 10 }
```

__Morph-Definition__

```xml
<range name="even number" increment="2">
  <data source="beginning"/>
  <data source="end"/>
</range>
```

__output__

```
{ even number: 0, even number: 2, even number: 4, even number: 6, even number: 8, even number: 10 }
```

</details>

<details>
  <summary>Example <b>FIX</b></summary>
__input__

```
{ beginning: 0, end: 10 }
```

__FIX-Definition__

TODO: specify arguments or attributes

```
do range("even number", infrement: "2")
  map("beginning")
  map("end")
end
```

__output__

```
{ even number: 0, even number: 2, even number: 4, even number: 6, even number: 8, even number: 10 }
```

</details>

 _________

#### equalsFilter

[equalsFilter](https://github.com/metafacture/metafacture-core/tree/master/metamorph/src/main/java/org/metafacture/metamorph/collectors/EqualsFilter.java) returns a new literal, if all incoming values of the literals are identical.

Attributes:
- `name` (always needed): name of the new literal
- `value` (always needed): value of the new literal
- `flushWith`  (default: if complete)
- `reset` (default: "false")
- `sameEntity` (default: "false")


<details>
  <summary>Example <b>Morph</b></summary>
__input__

```
{ litA: dog, litA: dog, litB: dog }
```

__Morph-Definition__

```xml
<equalsFilter name="all the same" value="true!">
  <data source="litA"/>
  <data source="litB"/>
</equalsFilter>
```

__output__

```
{ all the same: true }
```

</details>

<details>
  <summary>Example <b>FIX</b></summary>
__input__

```
{ litA: dog, litA: dog, litB: dog }
```

__FIX-Definition__


```
do equalsFilter("all the same", "true!")
  map("litA")
  map("litB")
end
```

__output__

```
{ all the same: true }
```

</details>

 _________

#### square

[square](https://github.com/metafacture/metafacture-core/blob/master/metamorph/src/main/java/org/metafacture/metamorph/collectors/Square.java) combines and emits all incoming values to all possible not orderd pairs. The emitted values won't be overwritten.

Attributes:
- `name` (always needed): name of the new literals
- `delimiter` (always needed): string that separates the values in a combination
- `prefix`
- `postfix`
- `flushWith`  (default: "record")
- `reset` (default: "true")

<details>
  <summary>Example <b>Morph</b></summary>
__input__

```
{ litA: valueA1, litA: valueA2, litB: valueB1, litC: valueC1 }
```

__Morph-Definition__

```xml
<square name="pair" delimiter="-" prefix="@" postfix="@">
  <data source="litA"/>
  <data source="litB"/>
  <data source="litC"/>
</square>
```

__output__

```
{ pair: @valueA1-valueC1@, pair: @valueA2-valueC1@, pair: @valueB1-valueC1@, pair: @valueA1-valueB1@, pair: @valueA2-valueB1@, pair: @valueA1-valueA2@ }
```

</details>

<details>
  <summary>Example <b>FIX</b></summary>
__input__

```
{ litA: valueA1, litA: valueA2, litB: valueB1, litC: valueC1 }
```

__FIX-Definition__


```
do square("pair", "-", prefix:"@", postfix:"@")
  map("litA")
  map("litB")
  map("litC")
end
```

__output__

```
{ pair: @valueA1-valueC1@, pair: @valueA2-valueC1@, pair: @valueB1-valueC1@, pair: @valueA1-valueB1@, pair: @valueA2-valueB1@, pair: @valueA1-valueA2@ }
```

</details>

 _________


#### tuples

[tuples](https://github.com/metafacture/metafacture-core/tree/master/metamorph/src/main/java/org/metafacture/metamorph/collectors/Tuples.java) creates all possible combination of the *different* literals. With the attribute `minN` you can set a a minimum of different sources to be received. If less than `minN` are received no tuples are emitted.

Attributes:
- `name` (always needed): name of the new literals
- `separator` (TODO: always needed?): string that separates the values in a combination
- `minN` sets a minimum of different literals
- `flushWith`  (default: "record")



<details>
  <summary>Example <b>Morph</b></summary>
__input__

```
{ litA: valueA1, litA: valueA2, litB: valueB }
```

__Morph-Definition__

```xml
<tuples name="combination" separator="-">
  <data source="litA"/>
  <data source="litB"/>
</tuples>
```

__output__

```
{ combination: valueA1-valueB, combination: valueA2-valueB }
```

</details>

<details>
  <summary>Example <b>FIX</b></summary>
__input__

```
{ litA: valueA1, litA: valueA2, litB: valueB }
```

__FIX-Definition__


```
do tuples("combination", separator:"-")
  map("litA")
  map("litB")
end
```

__output__

```
{ combination: valueA1-valueB, combination: valueA2-valueB }
```

</details>

 _________

#### group

[group](https://github.com/metafacture/metafacture-core/tree/master/metamorph/src/main/java/org/metafacture/metamorph/collectors/Group.java) collects literals and/or collectors, usually to apply the same set of functions on them in a `postprocessing` or to set `name` or/and `value` for all only once.

Attributes:
- `name` new name for all literals (optional)
- `value` new value for all literals (optional)


TODO: how to do this in FIX? especially postprocessing?

<details>
  <summary>Example <b>Morph</b></summary>
__input__

```
{ lowerA: abc, lowerB: def }
```

__Morph-Definition__

```xml
<group name="BIG">
  <data source="lowerA"/>
  <data source="lowerB"/>
  <postprocess>
    <case to="upper"/>
  </postprocess>
</group>
```

__output__

```
{ BIG: ABC, BIG: DEF }
```

</details>

<details>
  <summary>Example <b>FIX</b></summary>
__input__

```
{ lowerA: abc, lowerB: def }
```

__FIX-Definition__


```
do group("BIG")
  map("lowerA")
  map("lowerB")
  do postprocessing
    case(to:"upper")
  end
end
```

__output__

```
{ BIG: ABC, BIG: DEF }
```

</details>

 _________

#### all, any, none

[all](https://github.com/metafacture/metafacture-core/tree/master/metamorph/src/main/java/org/metafacture/metamorph/collectors/All.java), [any](https://github.com/metafacture/metafacture-core/tree/master/metamorph/src/main/java/org/metafacture/metamorph/collectors/Any.java), [none](https://github.com/metafacture/metafacture-core/tree/master/metamorph/src/main/java/org/metafacture/metamorph/collectors/None.java) controll, if literals exist (TODO: or collectors work?). If true, they return by default an unnamed literal with the value `true`. The default name and the default value can be changed. Complex nests that cobine `all`, `any` and `none` are possible.

What do they do:

- `all` controls if all literals exist
- `any` controls if at least one of the literals exists
- `none` controls if the literals does not exist in the record

Attributes:
- `name` new name for the literal (optional)
- `value` new value instead of true (optional)
- `flushWith`  (default: if complete)
- `reset` (default: "false")
- `sameEntity` (default: "false")

<details>
  <summary>Example <b>Morph</b></summary>
__input__

```
{ litB: value }
```

__Morph-Definition__

```xml
<all name="all exist">
  <data source="litA"/>
  <data source="litB"/>
</all>
<any name="some exist">
  <data source="litA"/>
  <data source="litB"/>
</any>
<none name="none exist">
  <data source="litA"/>
  <data source="litB"/>
</none>
```

__output__

```
{ some exist: true }
```

</details>

<details>
  <summary>Example <b>FIX</b></summary>
__input__

```
{ litB: value }
```

__FIX-Definition__


```
do all("all exist")
  map("litA")
  mop("litB")
end
do any("some exist")
  map("litA")
  mop("litB")
end
do none("none exist")
  map("litA")
  mop("litB")
end
```

__output__

```
{ some exist: true }
```

</details>

 _________

### Process controll

When and how *collectors* provide their results, can be defined by three different parameters: `flushWith`, `reset` and `sameEntity`. All of them are stated as attributes to a collector. At the moment process controll attributes are not supported in FIX. This is due to the change that FIX will not process stream based but record based.

TODO: Specify the process controll for subentities since they do not always work as the parententity.

## default settings `flushWith`, `reset` und `sameEntity`

Collector          |  flushWith       |  reset   |  sameEntity
------------------ | ---------------- | -------- | ------------
__`combine`__      | wenn vollständig | `false`  | `false`
__`concat`__       | `record`         | `true`   | `false`
__`entity`__       | wenn vollständig | `false`  | `false`
__`squares`__      | `record`         | `true`   | entfällt
__`tuples`__       | `record`         | entfällt | entfällt
__`group`__        | entfällt         | entfällt | entfällt
__`choose`__       | `record`         | `true`   | `false`
__`range`__        | `record`         | `false`  | `false`
__`equalsFilter`__ | wenn vollständig | `false`  | `false`
__`all`__          | wenn vollständig | `false`  | `false`
__`any`__          | wenn vollständig | `false`  | `false`
__`none`__         | wenn vollständig | `false`  | `false`


 _________
#### flushWith

The attribute `flushWith`  triggers output on the occurrence of any literal or entity with is stated as value: `flushWith="[name]"`. Variables in the output pattern which are not yet bound to a value, are replaced with the empty string. Wildcards can be used. Also multiple fields can be named when seperated by `|`.

Use `flushWith="record"` to set the record end as output trigger.
If flushWith has a certain entity as parameter it outputs at the end of the entity after all literals and subentities that it contains are processed.

Fix does not support `flushWith`.

Note:
- `combine`, `entity`, `equalsFilter` and the Quantors `all`, `any`, `none` flush by default if all demanded literals are processed once. If not, the collector is not triggert at all.
- If the explicit or default `flushWith` definition is not met the collector is not triggered and the collected literals will not be processed further.
- Empty collectors are not processed at all with or without `flushWith`
- Be careful with Wildcards. TODO: see swissbib warning: https://swissbib.gitlab.io/metamorph-doku/literale/datenfelder/

 _________

#### reset

The attritbute `reset` deletes all collected and processed literals after they are flushed. If `reset="false"` the processed literals will be cached and will be processed agan with *every* ne collected literal again.

Note:
 - `entity`, `combine`, `range`, `equalsFilter`, `all`, `any` and `none` are all by default set to `reset="false"`. This can create duplicates especially if you want to create multiple objects as `entity`. Then you have to set `reset="true"` to prevent these duplications of keys.
 - 
 _________

#### sameEntity

The attribute `sameEntity` will reset the collector after each entity end if set to true. Thus **processes only collections from the same source entity**. Note:
- the implenemntation only executes a reset if actually needed and it is independent of the `reset` attribute setting.
- is the source entity is processed but not all source literals and nested collectors are provided, then the saved literals are deleted and eventually overwritten. Then you need an additional `flushWith` attribute.
- 
 _________

### if conditionals

Collectors output can additionally be controlled with `if`-conditionals if they are flushed or not. At the moment if-conditionals are not supported in FIX.
A collector can have a single `if`-conditional.

The syntax looks as follows:


```xml
<combine name="output" value="${a}+${b}">
  <if>
    <data source="fieldA">
      <equals string="valueA"/>
    </data>
  </if>
  <data source="fieldA" name="a"/>
  <data source="fieldB" name="b"/>
</combine>
```

In this scenario only if fieldA has valueA the collector will be processed.

Note:
 - `if`-conditionals support all filter functions (see example above)
 - also all quantifiers (`all`, `any` and `none`)  can be used inside a `if`-conditional. But only one quantifier can be the direct child of a conditional.
  
  
TODO: hint to flushing since expacially with entities these wait for the hole if conditional to be done. Sometime you need to flush the quantifiers and the `if`-conditional earlier. Also sometimes it needs reseting.

e.g. in the following scenario literals are only concated if at least on of each source literal exists:

```xml
<concat name="output">
  <if>
    <all>
      <data source="fieldA"/>
      <data source="fieldB"/>
    </all>
  </if>
  <data source="fieldA" name="a"/>
  <data source="fieldB" name="b"/>
</combine>
```

Also note that you are able to nest the quantifiers so create complex conditionals.

e.g. the following scenarion only combines literals if the fieldA has valueA and fieldC does not exist in the record.

```xml
<concat name="output">
  <if>
    <all>
      <data source="fieldA">
        <equals string="valueA"/>
      </data>
      <none>
        <data source="fieldC"/>
      </none>
    </all>
  </if>
  <data source="fieldA" name="a"/>
  <data source="fieldB" name="b"/>
</combine>
```

## Modularise

__recursion__

__variable__

__makros__

__includes__