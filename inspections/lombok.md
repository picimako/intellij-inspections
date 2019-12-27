# Lombok

If you would like to use more Lombok specific inspection, it is already built in in the Lombok IntelliJ plugin [JetBrains page](https://plugins.jetbrains.com/plugin/6317-lombok)/[GitHub project](https://github.com/mplushnikov/lombok-intellij-plugin)
which is already an essential part of working with Lombok in IntelliJ.

- Lombok website: https://projectlombok.org
- Lombok on GitHub: https://github.com/rzwitserloot/lombok
- Lombok IntelliJ plugin on GitHub: https://github.com/mplushnikov/lombok-intellij-plugin

## Request for addition to Lombok
There is already a GitHub issue created for Lombok to have these inspections reviewed and added in some form: [Lombok IntelliJ plugin/594](https://github.com/mplushnikov/lombok-intellij-plugin/issues/594)

## Slf4j Logger is defined explicitly. Use Lombok @Slf4j annotation instead

There are couple of Lombok annotation for different logger APIs. This one checks for explicit Slf4j logger field definitions:

```java
Logger LOG = LoggerFactory.getLogger(CurrentClass.class)
```

Neither the field type (instance or static) nor the name of the field name is restricted to anything, making it more flexible. 

```xml
<searchConfiguration name="Slf4j Logger is defined explicitly. Use Lombok @Slf4j annotation instead." text="$Logger$ $LOG$ = $LoggerFactory$.getLogger($Class$.class);&#10;" recursive="false" caseInsensitive="true" type="JAVA">
    <constraint name="__context__" within="" contains="" />
    <constraint name="Logger" regexp="org\.slf4j\.Logger" within="" contains="" />
    <constraint name="LoggerFactory" regexp="org\.slf4j\.LoggerFactory" within="" contains="" />
    <constraint name="LOG" target="true" within="" contains="" />
    <constraint name="Class" within="" contains="" />
</searchConfiguration>
```

## @NonNull annotation is applied to a @Singular field. @Singular fields have no way to be set as null

According to Lombok's [official documentation](https://projectlombok.org/features/Builder):
> lombok will treat that builder node as a collection, and it generates 2 'adder' methods instead of a 'setter' method.
> One which adds a single element to the collection, and one which adds all elements of another collection to the collection. No setter to just set the collection (replacing whatever was already added) will be generated.

Therefore there is now way a @Singular collection could get `null` value, thus having the `@NonNull` annotation on such fields is superfluous.

This inspection only checks classes that annotated as `@lombok.Builder`, since `@Singular` field are allowed only is such classes. 

Example of incorrect usage:

```java
@NonNull
@Singular
private List<Cake> cakes;
```

**Template:**

```xml
<searchConfiguration name="@NonNull annotation is applied to a @Singular field. @Singular fields have no way to be set as null." text="@$BuilderAnnotation$( )&#10;class $Class$ {&#10;    @$SingularAnnotation$( )&#10;    @$NonNullAnnotation$( )&#10;    @Modifier(&quot;Instance&quot;) $FieldType$ $Field$ = $Init$;&#10;}" recursive="false" caseInsensitive="true" type="JAVA">
    <constraint name="Class" within="" contains="" />
    <constraint name="BuilderAnnotation" regexp="lombok\.Builder" within="" contains="" />
    <constraint name="FieldType" maxCount="2147483647" within="" contains="" />
    <constraint name="Field" maxCount="2147483647" target="true" within="" contains="" />
    <constraint name="Init" minCount="0" within="" contains="" />
    <constraint name="SingularAnnotation" regexp="lombok\.Singular" within="" contains="" />
    <constraint name="NonNullAnnotation" regexp="lombok\.NonNull" within="" contains="" />
</searchConfiguration>
```

## @Builder.Default field is not initialized. Either remove the annotation or initialize the field explicitly

`@Builder.Default` annotated fields are designed to provide a default/fallback value for that field in case it is not defined explicitly.

Thus having the this annotation on a field but missing the field initialization can mean two things:
- it lacks initialization intentionally in which case the annotation may be removed
- it lacks initialization accidentally in which case it needs to be added

An example of incorrect usage:

```java
@Builder.Default
private final City city;
```

**Template:**

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

## Lombok @NonNull is used on a method parameter. You may replace it with an explicit check e.g. java.util.Objects.requireNonNull

This inspection is based on the fact that including Lombok annotations in your code makes it a little bit more difficult to debug it, at least in IntelliJ.
Though this inspection might not be considered good practice, it might at least be an example how one can create such inspection with any desired annotation.

This inspection (for now) only works for methods whose parameters are all annotated as Lombok.@NonNull.

This inspection would signal code snippets like the following, as incorrect:

```java
public void someMethod(@NonNull String parameter, @NonNull Integer parameter) {
}
```

**Template:**

```xml
<searchConfiguration name="Lombok @NonNull is used on a method parameter. You may replace it with an explicit check e.g. java.util.Objects.requireNonNull." text="$ReturnType$ $Method$(@$NonNullAnnotation$ () $ParameterType$ $Parameter$);" recursive="false" caseInsensitive="true" type="JAVA">
    <constraint name="ReturnType" within="" contains="" />
    <constraint name="Method" within="" contains="" />
    <constraint name="ParameterType" maxCount="2147483647" within="" contains="" />
    <constraint name="Parameter" maxCount="2147483647" target="true" within="" contains="" />
    <constraint name="NonNullAnnotation" regexp="lombok\.NonNull" maxCount="2147483647" within="" contains="" />
</searchConfiguration>
```

## Primitive type field, method parameter or local variable is annotated as Lombok @NonNull

Since primitive types in Java cannot have `null` value, it doesn't make sense to annotate primitive type fields and variables as `@NonNull`,
though doing so does not result in any error, it's just unnecessary.

This inspection would signal code snippets like the following, as incorrect:

```java
@NonNull
private int anInteger;
```

**Template:**

```xml
<searchConfiguration name="Primitive type field, method parameter or local variable is annotated as Lombok @NonNull." text="@$NonNullAnnotation$ ( )&#10;$Type$ $Name$ = $Init$;" recursive="false" caseInsensitive="true" type="JAVA">
    <constraint name="NonNullAnnotation" regexp="lombok\.NonNull" within="" contains="" />
    <constraint name="Init" minCount="0" within="" contains="" />
    <constraint name="Type" regexp="byte|short|int|long|float|double|boolean|char" within="" contains="" />
    <constraint name="Name" target="true" within="" contains="" />
</searchConfiguration>
```

## Variable is annotated as Lombok @Cleanup. Use native Java try-with-resources statement instead

According to the [official documentation](https://projectlombok.org/features/Cleanup):
> You can use @Cleanup to ensure a given resource is automatically cleaned up before the code execution path exits your current scope.

But such logic has been introduced in Java 7 with the try-with-resources statement, so it's usage is preferred.

This inspection would signal code snippets like the following, as incorrect:

```java
@Cleanup
private InputStream inputStream;
```

**Template:**

```xml
<searchConfiguration name="Variable is annotated as Lombok @Cleanup. Use native Java try-with-resources statement instead." text="@$CleanupAnnotation$ ()&#10;$Type$ $Inst$ = $Expr$;" recursive="false" caseInsensitive="true" type="JAVA">
    <constraint name="Type" within="" contains="" />
    <constraint name="Inst" target="true" within="" contains="" />
    <constraint name="Expr" minCount="0" maxCount="0" within="" contains="" />
    <constraint name="CleanupAnnotation" regexp="lombok\.Cleanup" within="" contains="" />
    <constraint name="__context__" within="" contains="" />
</searchConfiguration>
```

## Static field in a @Builder class is annotated as @Builder.Default, which has no effect. It can be removed

Static fields in Builder classes are not meant to be a part of the built object and have builder methods, and Lombok's `@Builder` annotation
won't create any builder method for static fields, thus annotating those fields as `@Builder.Default` is superfluous because it has no effect.

This inspection would signal code snippets like the following, as incorrect:

```java
@Builder
public class SomeBuilder {
    
    @Builder.Default
    private static final int anInteger = 5;
}
```

**Template:**

```xml
<searchConfiguration name="Static field in a @Builder class is annotated as @Builder.Default, which has no effect. It can be removed." text="@$BuilderAnnotation$( )&#10;class $Class$ {&#10;    @$BuilderDefaultAnnotation$( )&#10;    static $FieldType$ $Field$ = $Init$;&#10;}" recursive="false" caseInsensitive="true" type="JAVA">
    <constraint name="BuilderAnnotation" regexp="lombok\.Builder" within="" contains="" />
    <constraint name="Class" within="" contains="" />
    <constraint name="BuilderDefaultAnnotation" regexp="lombok\.Builder\.Default" within="" contains="" />
    <constraint name="FieldType" maxCount="2147483647" within="" contains="" />
    <constraint name="Field" maxCount="2147483647" target="true" within="" contains="" />
    <constraint name="Init" minCount="0" within="" contains="" />
    <constraint name="__context__" within="" contains="" />
</searchConfiguration>
```

## Field in a non-@Builder class is annotated as @Builder.Default. It has no effect, thus can be removed

The `@Builder.Default` annotation is designed to work in combination with the `@Builder` annotation, so in classes where the class is not
annotated as `@Builder` it has no effect. But it may signal two things:
- it is the `@Builder` annotation that is missing from the class level, and it should be added
- or the class is indeed not a builder, so the `@Builder.Default` annotation may be removed

This inspection would signal code snippets like the following, as incorrect:

```java
public class SomeBuilder {
    
    @Builder.Default
    private final Integer anInteger = 5;
}
```

```xml
<searchConfiguration name="Field in a non-@Builder class is annotated as @Builder.Default. It has no effect, thus can be removed." text="@$BuilderAnnotation$( )&#10;class $Class$ {&#10;    @$BuilderDefaultAnnotation$( )&#10;    @Modifier(&quot;Instance&quot;) $FieldType$ $Field$ = $Init$;&#10;}" recursive="false" caseInsensitive="true" type="JAVA">
    <constraint name="BuilderAnnotation" regexp="lombok\.Builder" minCount="0" maxCount="0" within="" contains="" />
    <constraint name="Class" within="" contains="" />
    <constraint name="BuilderDefaultAnnotation" regexp="lombok\.Builder\.Default" within="" contains="" />
    <constraint name="FieldType" maxCount="2147483647" within="" contains="" />
    <constraint name="Field" maxCount="2147483647" target="true" within="" contains="" />
    <constraint name="Init" minCount="0" within="" contains="" />
</searchConfiguration>
```

## Explicit @Singular field initialization as empty collection can be removed, @Singular itself initializes it as an empty collection

When a collection type field in a `@Builder` annotated class is annotated as `@Singular` without a having defined a default value,
it is by default initialized as an empty collection, thus initializing it explicitly as an empty collection is superfluous, and can be removed.

This inspection would signal code snippets like the following, as incorrect:

```java
public class SomeBuilder {
    
    @Singular
    private final List<String> elements = new ArrayList<>;
}
```

Supported collection types:
- Java: java.util: List, ArrayList, Iterable, Collection, SortedSet, SortedMap, NavigableSet, NavigableMap, HashSet, HashMap, TreeSet, TreeMap
- Guava: com.google.common.collect.Immutable...: Collection, List, Set, SortedSet, Map, BiMap, SortedMap, Table

Supported initialization calls:
- Java: `new ArrayList<>()`, `new HashSet<>()`, `new TreeSet<>()`, `new HashMap<>()`, `new TreeMap<>()`, `List.of()`, `Map.of()`, `Set.of()`
- Guava: `ImmutableList.of()`, `ImmutableMap.of()`, `ImmutableBiMap.of()`, `ImmutableSortedMap.of()`, `ImmutableSet.of()`, `ImmutableSortedSet.of()`, `ImmutableTable.of()`

**Template:**

```xml
<searchConfiguration name="Explicit @Singular field initialization as empty collection can be removed, @Singular itself initializes it as an empty collection." text="@$BuilderAnnotation$( )&#10;class $Class$ {&#10;    @$SingularAnnotation$( )&#10;    @$BuilderDefaultAnnotation$( )&#10;    @Modifier(&quot;Instance&quot;) $FieldType$ $Field$ = $Init$;&#10;}" recursive="true" caseInsensitive="true" type="JAVA">
    <constraint name="__context__" within="" contains="" />
    <constraint name="BuilderAnnotation" regexp="lombok\.Builder" within="" contains="" />
    <constraint name="Class" within="" contains="" />
    <constraint name="SingularAnnotation" regexp="lombok\.Singular" within="" contains="" />
    <constraint name="BuilderDefaultAnnotation" regexp="lombok\.Builder\.Default" within="" contains="" />
    <constraint name="FieldType" regexp="(java\.util\.(List|ArrayList|Iterable|Collection|((Sorted|Navigable|Hash|Tree|)(Set|Map))))|(com\.google\.common\.collect\.Immutable(Collection|List|Set|SortedSet|Map|BiMap|SortedMap|Table))" within="" contains="" />
    <constraint name="Field" maxCount="2147483647" target="true" within="" contains="" />
    <constraint name="Init" regexp="(new (ArrayList|HashSet|TreeSet|HashMap|TreeMap)&lt;.*&gt;\(\))|(Immutable(List|Map|BiMap|SortedMap|Set|SortedSet|Table)\.of\(\))|((List|Map|Set)\.of\(\))" within="" contains="" />
</searchConfiguration>
```
