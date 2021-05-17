% Documentation Morph and Fix

<img src="img/metafacture.png" alt="Metafacture" style="max-width:100%"/>

# Modularise

The following chapter introduces how literals and morph definitions can be reused in a morph process.

## recursion

TODO: Does this work with FIX?

Literas usually are passed on to the next command of flushed but not stored. There is a special syntax - a leading `@` in the name - for recursion variables that can be defiend in a morph. This allows to store values until the end of a whole prosessing workflow.

```xml
<data source="input" name="@loop"/>
<data source="@loop" name="output"/>
```

When working with recursions mind the following:

  - **rename**: recursed literals in a cache need to be given a new name if used as source. If you don't rename you trigger a endless loop (stackOverflowError) the following example is NOT possible:

```xml
<data source="eingabename" name="@loop"/>
<data source="@loop"/> <!-- name-Attribut is missing! -->
```
  
  - you can have literals as first sign if you escape it with `\`. It won't appear when transformed. 
  - you can't create recursions in a collector since they are process as recursion and as a literal. therefor you also do not need to escape an leading `@` if you want to name a literal in a collector
  
Use cases for recusrions:
 - you only process a literal once and use it in different collectors or functions in the same process workflow
 - you can reuse the result of a collector, that needs to be flushed before the records end, in another collector

Limitation:
 - The cached literl: TODO: see https://swissbib.gitlab.io/metamorph-doku/modularisierung/rekursion/

<details>
  <summary>Example <b>Morph</b></summary>
__input__

```
{ litA: valueAA, litA: valueAB, litB: valueBA, litB: valueBB }
```

__Morph-Definition__

```xml
<rules>
    <data source="litA" name="@loop"/>
    <entity name="entAB" flushWith="litB" reset="true">
        <data source="@loop" name="litA"/>
        <data source="litB" name="litB"/>
    </entity>
</rules>
```

__output__

```
{ entAB { litA: valueAA, litA: valueAB, litB: valueBA }, entAB { litB: valueBB } }
```

</details>

<details>
  <summary>Example <b>FIX</b></summary>
__input__

```
{ litA: valueAA, litA: valueAB, litB: valueBA, litB: valueBB }
```

__FIX-Definition__


```
TODO !!
```

__output__

```
{ entAB { litA: valueAA, litA: valueAB, litB: valueBA }, entAB { litB: valueBB } }
```

</details>

 _________

### variable

TODO: at a later stage

works in FIX

### makros

You can define macros outside of the <rules>-element that let you reuse functions and collector combinations.

```xml
<macros>
  <macro name="eindeutiger_name">
  <!-- Makro -->
  </macro>
  <!-- additional Makros -->
</macros>
<!-- <rules> ... -->
```

TODO: How does this work in fix? And better description.

Every macro needs to have a `name`-attribute so that it can be called in the morph process.
Macros can have multiple functions and collectos. The parameters need to written in square bracket with a leading dollar sign in front of the brakcets.

e.g.

```xml
<macro name="makro1">
  <concat delimiter=" " name="$[ccName]" flushWith="$[abschickenMit]">
    <data source="$[dsSource]" >
      <case to="upper"/>
    </data>
  </concat>
</macro>
```

In the morph-file macros can be called with the element `call-macro`. As attribute the name of the macro has to be stated in `name`.
Additional attributes will be used as arguments for the macro. The macro above will be call as follow:

```xml
<combine name="name" value="${vorname} ${nachname}">
  <call-macro name="makro1" ccName="vorname" dsName="feldA" abschickenMit="nachname" />
  <data source="feldB" name="nachname"/>
</combine>
```

__includes__

TODO: at a later stage

works in FIX