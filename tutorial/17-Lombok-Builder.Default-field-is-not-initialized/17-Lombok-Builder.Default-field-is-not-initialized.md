# Lombok @Builder.Default field is not initialized
Lombok has a [Builder annotation](https://projectlombok.org/features/Builder) with which you can auto-generate a whole builder implementation of a class.
That annotation needs to be applied on class level:

```java
@Builder
public class Unicorn {
    private final String name;
    private final Color color;

    ...
}
```

If you want to provide default values for fields as well, you can do it by adding the `@Builder.Default` annotation to the necessary field(s) and initialize them explicitly:

```java
@Builder
public class Unicorn {
    private final String name;
    @Builder.Default
    private final Color color = Color.PINK;

    ...
}
```

However if the `@Builder.Default` annotation is present on a field, but the field is not initialized, it may mean two things:
- either you don't want to specify a default value and rely on Java's default values, in which case the annotation is unnecessary
- or you just forgot to define the default value

This is the issue I'm going to explain how to create an inspection for.

## Template creation
In this template I combined two predefined ones

- *annotated classes*
    ```java
    @$Annotation$( )
    class $Class$ {}
    ```

- and *instance fields of the class*
    ```java
    class $Class$ {
        @Modifier("Instance") $FieldType$ $Field$ = $Init$;
    }
    ```

I did some simple modifications to them. First I renamed the `$Annotation$` variable to `$BuilderAnnotation$` to make its purpose clearer, then added `$BuilderDefaultAnnotation$` and `$SingularAnnotation$`to the field definition.
I will explain them in a second.

```java
@$BuilderAnnotation$( )
class $Class$ {
    @$BuilderDefaultAnnotation$( )
    @$SingularAnnotation$( )
    @Modifier("Instance") $FieldType$ $Field$ = $Init$;
}
```

![editor](images/17-Lombok-Builder.Default-field-is-not-initialized_Editor.PNG)

## BuilderAnnotation and BuilderDefaultAnnotation variables
We are looking for `@Builder.Default` annotated instance fields in `@Builder` annotated classes, so first let's define the conditions for these two variables.

The only crucial thing in case of both variables is the reference to these annotations which means we need to configure a Text filter for them accordingly.

For `$BuilderAnnotation$` it needs to be defined as

```
lombok\.Builder
```

and for `$BuilderDefaultAnnotation$` it will be

```
lombok\.Builder\.Default
```

Any other configuration can be left at their default states.

![builderannotation](images/17-Lombok-Builder.Default-field-is-not-initialized_BuilderAnnotation.PNG)

![builderdefaultannotation](images/17-Lombok-Builder.Default-field-is-not-initialized_BuilderDefaultAnnotation.PNG)

## SingularAnnotation variable
Lombok has an annotation called [`@Singular`](https://projectlombok.org/features/Builder) as an extra feature for `@Builder`. The official documentation defines its functionality as the following:

> By annotating one of the parameters (if annotating a method or constructor with @Builder) or fields (if annotating a class with @Builder) with the @Singular annotation, lombok will treat that builder node as a collection, and it generates 2 'adder' methods instead of a 'setter' method.

Based on my experience, these collections are initialized as empty collections under the hood, so it doesn't need to be done explicitly.

That being said we need to exclude the `@Singular` annotated fields from the signaled matches, telling IntelliJ to mark a field problematic by this inspection only when the @Singular annotation is not present.
In order to achieve this let's add a Count filter to this variable with the min and max counts set to 0-0.

![singularannotation](images/17-Lombok-Builder.Default-field-is-not-initialized_SingularAnnotation.PNG)

## Class variable
The class name is not important in this template, so no additional configuration is required.

## FieldType and Field variables
These two variables are only important for their number of occurrences. The min and max counts of both (ithin a Count filter) needs to be set to 1-Unlimited,
otherwise if you don't add such a filter (defaulting to 1-1), IntelliJ will look for only the first match, thus highlight only the first match.

Also I marked `$Field$` as the target of the search, thus having only the field name highlighted in case of a match.

![field](images/17-Lombok-Builder.Default-field-is-not-initialized_Field.PNG)

Though the UI doesn't show the filters for `$FieldType$`, the XML representation still contains the filter and works properly.

## Init variable
In this inspection we are looking for fields that are not initialized explicitly, therefore we need to configure this variable as a missing one,
which means its minimum and maximum counts need to be set to 0-0 in a Count filter.

![init](images/17-Lombok-Builder.Default-field-is-not-initialized_Init.PNG)

## Finalization

![highlight](images/17-Lombok-Builder.Default-field-is-not-initialized_Highlight.PNG)

Below you can find the XML representation of the template created, so that you can easily copy and paste it into your template collection.

```xml
<searchConfiguration name="@Builder.Default field is not initialized. Either remove the annotation or initialize the field explicitly." text="@$BuilderAnnotation$( )&#10;class $Class$ {&#10;    @$BuilderDefaultAnnotation$( )&#10;    @$SingularAnnotation$( )&#10;    @Modifier(&quot;Instance&quot;) $FieldType$ $Field$ = $Init$;&#10;}" recursive="true" caseInsensitive="true" type="JAVA">
    <constraint name="BuilderAnnotation" regexp="lombok\.Builder" within="" contains="" />
    <constraint name="Class" within="" contains="" />
    <constraint name="FieldType" maxCount="2147483647" within="" contains="" />
    <constraint name="Field" maxCount="2147483647" target="true" within="" contains="" />
    <constraint name="Init" minCount="0" maxCount="0" within="" contains="" />
    <constraint name="BuilderDefaultAnnotation" regexp="lombok\.Builder\.Default" within="" contains="" />
    <constraint name="__context__" within="" contains="" />
    <constraint name="SingularAnnotation" regexp="lombok\.Singular" minCount="0" maxCount="0" within="" contains="" />
</searchConfiguration>
```
