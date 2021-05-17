% Documentation Morph and Fix

<img src="img/metafacture.png" alt="Metafacture" style="max-width:100%"/>

# Literal functions 

TODO: General Remark to the difference in MOPRH AND FIX and how to state attribtutes?

## Read - `<data>` / `map`

The simplest way to work with literals is to read them and then pass them on. This is done by the `data`-function.
It always needs a `source` attribute that tells the function which literal should be read.

Note: Wildcards \* and ? are allowed in the `source` attribute. You also can use variants with square brackets `source="bee[rt]"`. Furthermore you can use multiple names as source by separating the names with a pipe `|` e.g. `source="beer|coffee"`

Additionally there are three variables for `source` that have fixed meanings:

- `_id`: reads the ID of the record
- `_else`: passes on all data elements that are not transformed
- `_elseNested`: passes on all data elements that are not transformed in nested structure



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

_________

## Transform

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

TODO: is the example right in FIX?

```
do map ("litA", "gruss")
  trim()
  do whitelist
    entry ("hallo")
    entry ("grüezi")
  end
  case (to: "upper")
end
```

</details>

_________
### compose

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
  compose(prefix: "Hello", postfix: "!")
end
```

__output__

```
{litA: Hallo, Alf!}
```

</details>

_________

### constant

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

TODO: How to do this in FIX? With or without `value:`

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

### timestamp

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

TODO: How to use it in FIX? Attributes with or without names?

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

### substring

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

TODO: How to use it in FIX? Attributes with or without names?

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

### replace/setreplace

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
  replace(pattern:"[ace]", with:"X")
end
```

__output__

```
{litA: XbXdX, litA: fghij}
```

</details>

[setreplace](https://github.com/metafacture/metafacture-core/tree/master/metamorph/src/main/java/org/metafacture/metamorph/functions/SetReplace.java) replaces multiple strings. The mapping from string to replacement is defined in a map, just as done in the lookup function. See also Data Lookup. Setreplace does not support regex if you use in a seperate map.

TODO: `replace` and `regexp` seem to have different ways in using groups with and without bracket.


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

### lookup

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
<data source="kanton" name="main location">
  <lookup default="nicht bekannt">
    <entry name="Wallis" value="Sion"/>
    <entry name="Jura" value="Delémont"/>
    <entry name="Waadt" value="Lausanne"/>
  </lookup>
</data>
```

__output__

```
{main location: Lausanne, main location: Sion, main location: Delémont, main location: nicht bekannt}
```

</details>

<details>
  <summary>Example <b>FIX</b></summary>
__input__

```
{kanton: Waadt, kanton: Wallis, kanton: Jura, kanton: Genf}
```

__FIX-Definition__

TODO: How to use it in FIX? Attributes with or without names? With In we have a leading: `in: `

```
do map("kanton", "main location")
  lookup(in: "...")
end
```

__output__

```
{main location: Lausanne, main location: Sion, main location: Delémont, main location: nicht bekannt}
```

</details>

_________

### case

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

TODO: How to use it in FIX? Attributes with or without names?


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

### trim

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

TODO: How to use it in FIX? Always with emty brackets?


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


### normalize-utf8

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

TODO: How to use it in FIX? Always with empty brackets?

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

### dateformat

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

TODO: How to use it in FIX? Attributes with or without names?


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

### split

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

### isbn

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
TODO: How to use it in FIX? Attributes with or without names?

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

### urlencode

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

TODO: How to use it in FIX? Always empty brackets?

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

### htmlanchor

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

TODO: How to use it in FIX? Attributes with or without names?

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

### switch-name-value

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

TODO: How to use it in FIX? Always empty brackets?
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

### script and java

TODO: fill out this part.

[script](https://github.com/metafacture/metafacture-core/tree/master/metamorph/src/main/java/org/metafacture/metamorph/functions/Script.java) and [java](LINK) process the value with a JavaScript function or a Java class. See Integration of Java and JavaScript.

`script` uses attribute `invoke` for an Javascript-function in an external file (attribute `file`) to pass the values on.

TODO: How to use it in FIX? Attributes with or without names?

`java` TODO: Morph and FIX

_________

### count

[count](https://github.com/metafacture/metafacture-core/tree/master/metamorph/src/main/java/org/metafacture/metamorph/functions/Count.java) counts literals and gives the actual counted number with each literal. To give back only the last counted number see the cookbook. (TODO: cookbook refrence)

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

TODO: How to use it in FIX? Always empty brackets?

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

## Filter

The following passage introduces the different literal functions, which filter literal values based on certain rules.

As all literal function filter functions are inserted in the `<data>`-Element. In the FIX language the literal functions are inserted in an `do map ([source], [name]) ... end`. For more see [here](#transform)

_________

### whitelist

[whitelist](https://github.com/metafacture/metafacture-core/blob/master/metamorph/src/main/java/org/metafacture/metamorph/functions/WhiteList.java) filters based on a listed values that are allowed. Allowed values are stated as following `<entry name=[value]/>`. You can also use an external [Data Lookup](#data-lookup). 


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


```
{litA: allowed, litA: not allowed, litA: not permitted, litA: forbidden, litA: accepted}
```

__FIX-Definition__
TODO: How to use it in FIX? Attributes with or without names? And how to state the `entity`-elements?

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

### blacklist


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


```
{litA: allowed, litA: not allowed, litA: not permitted, litA: forbidden, litA: accepted}
```

__FIX-Definition__

TODO: How to use it in FIX? Attributes with or without names? And how to state the `entity`-elements?

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

### contains / not-contains

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

TODO: How to use it in FIX? Attributes with or without names?

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

TODO: How to use it in FIX? Attributes with or without names?
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
TODO: How to use it in FIX? Attributes with or without names?

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

### occurence

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
TODO: How to use it in FIX? Attributes with or without names?

```
do map("litA")
  occurence(only: "2")
end
```

__output__

```
{litA: butterfly, litA: cat}
```

</details>

 _________

### regexp

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

TODO: `replace` and `regexp` seem to have different ways in using groups with and without bracket. 

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

### unique

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

TODO: How to use it in FIX? Attributes with or without names?

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

## Other

Some function do not fit in the filter/transformation divide.

### buffer

[buffer](https://github.com/metafacture/metafacture-core/blob/master/metamorph/src/main/java/org/metafacture/metamorph/functions/Buffer.java) withholds literal until the `flushWith`-condition is met. For further details concerning `flushWith" [see](#process-controll).

TODO: https://swissbib.gitlab.io/metamorph-doku/literale/filter/ states a bug concerning the buffer. Examples should be added.

TODO: How to use it in FIX? Attributes with or without names?

### add_field (only FIX)

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

## Data lookup

A certain group of functions takes a map/dictionary as argument for filter or transform literals: `<lookup>`, `<whitelist>`, `<blacklist>` etc.
This can be done in three different ways:
- in the function
- refrencing to a map in the morph
- refrencing to a map/database outside the morph  

In this section the usage of such maps will be explained. We start with a simple example of data lookup.

### Local Lookup

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

TODO: How to use it in FIX? Attributes with or without names? How to state `entry`-elements?
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

### map

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

 TODO: How to use it in FIX? Attributes with or without names?

_________

### filemap

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

### javamap

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

### restmap

[restmap](https://github.com/metafacture/metafacture-core/tree/master/metamorph/src/main/java/org/metafacture/metamorph/maps/RestMap.java) requests a REST-API

Attributes:
- `name`  gives the mapping a name (always needed)
- `url` URL of the REST-API (always needed)

TODO: example and FIX

_________

### sqlmap

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

### jndisqlmap

 [jndisqlmap](https://github.com/metafacture/metafacture-core/tree/master/metamorph/src/main/java/org/metafacture/metamorph/maps/JndiSqlMap.java) requests a SQL databse via [JNDI](https://de.wikipedia.org/wiki/Java_Naming_and_Directory_Interface)

Attributes:
- `name`  gives the mapping a name (always needed)
- `datasource` (always needed)
- `query` (always needed)

TODO: example and FIX