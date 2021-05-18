% Documentation Morph and Fix

<img src="img/metafacture.png" alt="Metafacture" style="max-width:100%"/>

# Collectors

In the case that an output depends on the values from more then one literal, we need to collect literals.

Collectors can aggregate, filter and combine multiple different literals. Collectors are defined under the <rules> tag, just as <data> tags. <data> tags are be put inside the respective collectors to indicate which literals are to be collected.
Allmost all collector can be:
-  controlled concerning the time and way of the emission with [process controll attributes](#process-controll)
-  can be controlled with [if-conditionals](#if-conditionals) (not supported with FIX)
-  can be nested except `entity`
-  to all types of collectors post-processing steps can be added by using the `postprocess` tag (FIX?)

TODO: specify for FIX, do all collectors in FIX start with do? attributes always with name? How to `postprocess` in FIX

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

TODO: example in FIX right?

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

## List of collectors

### combine

[combine](https://github.com/metafacture/metafacture-core/tree/master/metamorph/src/main/java/org/metafacture/metamorph/collectors/Combine.java) puts together different literals following a certain value template. Following values overwrite previes values of the same field.

Attributes:

- `name` (always needed): name of the new field
- `value` (always needed): Template. Values can be added with ${fieldname} (TODO: FIX, ohne geschwiffene Klammer?)
- `flushWith` (default: if complete)
- `reset`, (default: false)
- `sameEntity`, (default: false)
- `flushIncomplete` (default: "true")

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

TODO: How to use it in FIX? Attributes with or without names?

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


### concat

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
TODO: How to use it in FIX? Attributes with or without names?

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


### entity

[entity](https://github.com/metafacture/metafacture-core/tree/master/metamorph/src/main/java/org/metafacture/metamorph/collectors/Entity.java) creates a new entity or subentity with the collected literals.

Attributes:

- `name` (always needed): name of the new entity
- `flushWith`  (default: if complete)
- `reset` (default: false)
- `sameEntity` (default: false)
- `flushIncomplete` (default: "true")

Note: An `entity` can nest other collectors but an `entity` can only be nested in another `entity`

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

### choose

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

### range

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

TODO: How to use it in FIX? Attributes with or without names?

```
do range("even number", increment: "2")
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

### equalsFilter

[equalsFilter](https://github.com/metafacture/metafacture-core/tree/master/metamorph/src/main/java/org/metafacture/metamorph/collectors/EqualsFilter.java) returns a new literal, if all incoming values of the literals are identical.

Attributes:

- `name` (always needed): name of the new literal
- `value` (always needed): value of the new literal
- `flushWith`  (default: if complete)
- `reset` (default: "false")
- `sameEntity` (default: "false")
- `flushIncomplete` (default: "true")


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
TODO: How to use it in FIX? Attributes with or without names?

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

### square

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
TODO: How to use it in FIX? Attributes with or without names?

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


### tuples

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
TODO: How to use it in FIX? Attributes with or without names?


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

### group

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
<group name="BIG">NOTE: This option is only applicable in combination with flushWith.

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
TODO: How to use it in FIX? Attributes with or without names? Postprocessing?

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

### all, any, none

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
TODO: How to use it in FIX? Attributes with or without names? Does they exist in FIX?

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

## Process controll

When and how *collectors* provide their results, can be defined by three different parameters: `flushWith`, `reset`, `sameEntity` and `flushIncomplete`. All of them are stated as attributes to a collector. At the moment process controll attributes are not supported in FIX. This is due to the change that FIX will not process stream based but record based.

TODO: Specify the process controll for subentities since they do not always work as the parententity.

## default settings `flushWith`, `reset` und `sameEntity`

Collector          |  `flushWith`     |  `reset`        |  `sameEntity`  |  `flushIncomplete` |
------------------ | ---------------- | --------        | ------------   |  ------------ 
__`combine`__      | if complete      | `false`         | `false`        |  `true` |
__`concat`__       | `record`         | `true`          | `false`        |  not applicable |
__`entity`__       | if complete      | `false`         | `false`        |  `true` |
__`squares`__      | `record`         | `true`          | not applicable |  not applicable |
__`tuples`__       | `record`         | not applicable  | not applicable |  not applicable |
__`group`__        | not applicable   | not applicable  | not applicable |  not applicable |
__`choose`__       | `record`         | `true`          | `false`        |  not applicable |
__`range`__        | `record`         | `false`         | `false`        |  not applicable |
__`equalsFilter`__ | if complete      | `false`         | `false`        |  `true` |
__`all`__          | if complete      | `false`         | `false`        |  not applicable |
__`any`__          | if complete      | `false`         | `false`        |  not applicable |
__`none`__         | if complete      | `false`         | `false`        |  not applicable |

TODO: are the default settings the same in FIX? I think this is important.
 _________
### flushWith

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

### reset

The attritbute `reset` deletes all collected and processed literals after they are flushed. If `reset="false"` the processed literals will be cached and will be processed agan with *every* ne collected literal again.

Note:

 - `entity`, `combine`, `range`, `equalsFilter`, `all`, `any` and `none` are all by default set to `reset="false"`. This can create duplicates especially if you want to create multiple objects as `entity`. Then you have to set `reset="true"` to prevent these duplications of keys.

_________

### sameEntity

The attribute `sameEntity` will reset the collector after each entity end if set to true. Thus **processes only collections from the same source entity**. Note:

- the implenemntation only executes a reset if actually needed and it is independent of the `reset` attribute setting.
- is the source entity is processed but not all source literals and nested collectors are provided, then the saved literals are deleted and eventually overwritten. Then you need an additional `flushWith` attribute.


### flushIncomplete

The attribute `flushIncomplete` controlls if a collector is complete before flushing. Without this option, a flushing collector (e.g. `EqualsFilter`) always emits its value when being flushed, regardless of whether it's already complete.

Only applies to a subset of all flushing collectors since most of them are never "complete"; `All`, on the other hand, has this condition already built-in.

NOTE: This option is only applicable in combination with `flushWith`.

TODO: Is this supported or planned for FIX?
_________

## if conditionals

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
