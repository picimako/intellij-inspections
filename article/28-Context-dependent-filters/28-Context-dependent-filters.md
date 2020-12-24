# Context dependent filters

I came across a thing that when I inserted the XML representation of an inspection containing a Count filter for a field type and field name template variables,
the filter for the field type variable wasn't showing in the GUI editor, and I couldn't even add a Count filter for that variable.
I thought it is a bug because it worked with the previous version of the editor.

Then I ran into an other thing when I changed the max value of a certain variable, the filter just disappeared. My guess was that the value I set was the default one.

Then it struck me that the variables are most probably context dependent and Count filters are not allowed where they don't make sense, also that certain variables (rather AST nodes)
have different default counts (or allowed counts) as per Java language constraints.

## Disabled Count filter option
Let's have an instance field of a class with an annotation on the field:

```java
class $Class$ { 
    @$Annotation$ ()
    @Modifier("Instance") $FieldType$ $Field$ = $Init$;
}
```

For instance, you can create an inspection validating fields with a certain annotation applied to them, and there can be any number of such fields.

What you can do is add a Count filter to the `$Field$` variable setting it to *1-Unlimited*. Now it would be straightforward to add a Count filter for `$FieldType$` as well,
however you can't. IntelliJ is clever, and it knows that a field declaration cannot be done without having a field type, thus it prevents us from
setting values for `$FieldType$` and `$Field$` that would interfere with each other.

Considering all of this, the filter values *1-Unlimited* will be applied to both variables together.

### Fallback to default values
Consider the same template as in the last section, we'll look into the `$Init$` variable.

As it turns out IntelliJ is more clever than I thought. In terms of a field the initial value of the field is optional, it can either be present 1 or 0 times,
it determines whether we are talking about declaration or initialization.

The allowed min and max counts are also displayed as a watermark in the field when you clear the field. The field border also turns red, highlighting an error that
the set value is incorrect, e.g. a negative number, or a number greater than the allowed max value.

There are also default values assigned to the variable/node. If the default ones are set the Count filter is removed from the list of filters.

### Count filter defaults
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

## Disabled Type filter option

The definition of the Type filter according to the official documentation:

> The Type filter adds a type of value or expression that is expected for the specified variable.

Java language elements that don't fall into this category are, to name a couple of them:  
- Class
- Annotation
- Field type
- Field name

In case of template variables that reflect these elements/nodes, Type filter is not available, so that users are not mislead about what is a valid construct and what is not.

If you want to specify the reference to the type, you can use Text filter setting the full reference to the class, interface, etc.
