# Count filter

The Count filter is one of the 5 filter types that may be added to user defined variables in Structural Search and Replace templates.

According to the [official documentation](https://www.jetbrains.com/help/idea/search-templates.html#count_filter):
> The Count filter specifies a number of occurrences.

NOTE: a part of this article contains some parts of the article called [Context dependent filters](../28-Context-dependent-filters/28-Context-dependent-filters.md)
but that one in itself is still a valid article because it covers variables from a different perspective.

## Summary

A Count filter consists of two fields, a **min** and a **max** field. These two values create a range inclusive
on both sides, and if the number of occurrences of a PSI node falls into this range, then that part of the template is considered
as matching.

## XML representation

There are two attributes of the `<constraint>` tag that is relevant for the Count filter, `minCount` and `maxCount`.

When they are or aren't present in the XML configuration is not really relevant because the XML configuration doesn't
need to be edited manually (maybe apart from its title), it is much more easy to edit it via the GUI, but one thing might
be interesting, that the value `Unlimited` is stored as the following:

```xml
<constraint name="Parameters" minCount="0" maxCount="2147483647" within="" contains="" />
```

## Disabled Count filter option

Let's have an instance field of a class with an annotation on the field:

```java
class $Class$ { 
    @$Annotation$ ()
    @Modifier("Instance") $FieldType$ $Field$ = $Init$;
}
```

For instance, you can create an inspection, validating fields with a certain annotation applied to them, and there can be any number of such fields.

What you can do is add a Count filter to the `$Field$` variable setting it to *1-Unlimited*. Now it would be straightforward to add a Count filter for `$FieldType$` as well,
however you can't. IntelliJ is clever, and it knows that a field declaration cannot be done without having a field type, thus it prevents us from
setting values for `$FieldType$` and `$Field$` that would interfere with each other.

Considering all of this, the filter values *1-Unlimited* will be applied to both variables together.

## Fallback to default values

Consider the same template as in the last section, we'll look into the `$Init$` variable.

As it turns out IntelliJ is more clever than I thought. In terms of a field the initial value of the field is optional, it can either be present 1 or 0 times,
it determines whether we are talking about declaration or initialization.

The allowed min and max counts are also displayed as a watermark in the field when you clear the field. The field border also turns red, highlighting an error that
the set value is incorrect, e.g. a negative number, or a number greater than the allowed max value.

There are also default values assigned to the variable/node. If the default ones are set the Count filter is removed from the list of filters.

## Count filter defaults

I'm going to list the default values and whether it is possible to add a Count filter, for a couple of the probably more commonly used nodes, below.

In general the default min and max values seem to be *1-1* (I didn't try every possible option). The allowed values however can differ.
The list below either states that no Count filter is available, or what the min and max values are.

- **class:** no Count filter
- **extends class list:** 0-1
- **implements class list:** 1-Unlimited
- **class annotation:** 1-Unlimited
- **field type/field:** 1-Unlimited
- **field/variable init value:** 0-1
- **field annotation:** 1-Unlimited
- **static method call:** no Count filter
- **class reference in case of instance method call:** 0-1
- **class reference in case of static method call:** 0-1

## Count filter UI

If you are interested in the Count filter's UI structure you can check out the `com.intellij.structuralsearch.plugin.ui.filters` package,
specifically the class `CountFilter`.
