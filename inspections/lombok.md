# Lombok

If you would like to use more Lombok specific inspection, it is already built into the Lombok IntelliJ plugin [JetBrains page](https://plugins.jetbrains.com/plugin/6317-lombok)/[GitHub project](https://github.com/mplushnikov/lombok-intellij-plugin)
which is already an essential part of working with Lombok in IntelliJ.

- Lombok website: https://projectlombok.org
- Lombok on GitHub: https://github.com/rzwitserloot/lombok
- Lombok IntelliJ plugin on GitHub: https://github.com/mplushnikov/lombok-intellij-plugin

## Table of contents

#### Logging
- [Slf4j Logger is defined explicitly. Use Lombok @Slf4j annotation instead](#slf4j-logger-is-defined-explicitly-use-lombok-slf4j-annotation-instead)

#### @Builder
- [@NonNull annotation is applied to a @Singular field. @Singular fields have no way to be set as null](#nonnull-annotation-is-applied-to-a-singular-field-singular-fields-have-no-way-to-be-set-as-null)
- [@Builder.Default field is not initialized. Either remove the annotation or initialize the field explicitly](#builderdefault-field-is-not-initialized-either-remove-the-annotation-or-initialize-the-field-explicitly)
- [Static field in a @Builder class is annotated as @Builder.Default, which has no effect. It can be removed](#static-field-in-a-builder-class-is-annotated-as-builderdefault-which-has-no-effect-it-can-be-removed)
- [Field in a non-@Builder class is annotated as @Builder.Default. It has no effect, thus can be removed](#field-in-a-non-builder-class-is-annotated-as-builderdefault-it-has-no-effect-thus-can-be-removed)
- [Explicit @Singular field initialization as empty collection can be removed, @Singular itself initializes it as an empty collection](#explicit-singular-field-initialization-as-empty-collection-can-be-removed-singular-itself-initializes-it-as-an-empty-collection)

#### @NonNull
- [Lombok @NonNull is used on a method parameter. You may replace it with an explicit check e.g. java.util.Objects.requireNonNull](#lombok-nonnull-is-used-on-a-method-parameter-you-may-replace-it-with-an-explicit-check-eg-javautilobjectsrequirenonnull)
- [Primitive type field, method parameter or local variable is annotated as Lombok @NonNull](#primitive-type-field-method-parameter-or-local-variable-is-annotated-as-lombok-nonnull)

#### @Cleanup
- [Variable is annotated as Lombok @Cleanup. Use native Java try-with-resources statement instead](#variable-is-annotated-as-lombok-cleanup-use-native-java-try-with-resources-statement-instead)

## Request for addition to Lombok
There is already a GitHub issue created for Lombok to have these inspections reviewed and added in some form: [Lombok IntelliJ plugin/594](https://github.com/mplushnikov/lombok-intellij-plugin/issues/594)

## Slf4j Logger is defined explicitly. Use Lombok @Slf4j annotation instead

There are a couple of Lombok annotations for various logger APIs, so one might want to use (enforce the usage, or migrate to) one of these annotations
instead of using explicit logger field definitions. This inspection checks for explicit Slf4j logger field definitions, but with some type updates
other libraries can be validated as well.

Neither the field type (instance or static, visibility) nor the name of the field name is restricted to anything, making it more flexible.

| Compliant code | Non-compliant code |
|---|---|
| <pre>@Slf4j<br>public class ANiceClass {<br>}</pre> | <pre>public class ANiceClass {<br>    private static final Logger LOG = LoggerFactory.getLogger(ANiceClass.class)<br>}</pre> |

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

Therefore there is no way a @Singular collection could get `null` value, thus having the `@NonNull` annotation on such fields is superfluous.

This inspection only checks classes that are annotated as `@lombok.Builder`, since `@Singular` fields are allowed only is such classes. 

| Compliant code | Non-compliant code |
|---|---|
| <pre>@Singular<br>private List<Cake> cakes;</pre> | <pre>@NonNull<br>@Singular<br>private List<Cake> cakes;</pre> |

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

`@Builder.Default` annotated fields are designed to provide a default/fallback value for those fields in case they are not defined explicitly.

Thus having this annotation on a field but missing the field initialization can mean two things:
- it lacks initialization intentionally in which case the annotation may be removed
- it lacks initialization accidentally in which case it needs to be added

| Compliant code (depending on the fields's purpose) | Non-compliant code |
|---|---|
| <pre>private final City city;</pre> | <pre>@Builder.Default<br>private final City city;</pre> |
| <pre>@Builder.Default<br>private final City city = City.SZEGED;</pre> |  |

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

This inspection is based on the fact that including Lombok annotations in your code makes it a little bit more difficult to debug it, at least in my experience with IntelliJ.
Although this inspection might not be considered good practice, it might at least be an example how one can create such inspection with any desired annotation.

This inspection (for now) only works for methods whose parameters are all annotated as `Lombok.@NonNull`.

| Compliant code | Non-compliant code |
|---|---|
| <pre>public void someMethod(String strParam, Integer intParam) {<br>    doSomethingWith(requireNonNull(strParam));<br>    doSomethingWith(requireNonNull(intParam));<br>}</pre> | <pre>public void someMethod(@NonNull String strParam, @NonNull Integer intParam) {<br>}</pre> |

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

This inspection has support for all primitive types.

| Compliant code | Non-compliant code |
|---|---|
| <pre>private int anInteger;</pre> | <pre>@NonNull<br>private int anInteger;</pre> |

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

Since such logic has been introduced in Java 7 with the *try-with-resources* statement, some might prefer using the native solution over the Lombok annotation.

| Compliant code | Non-compliant code |
|---|---|
| <pre>try (InputStream inputStream = new FileInputStream(file)) {<br>}</pre> | <pre>@Cleanup<br>private InputStream inputStream;</pre> |

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

Static fields in Builder classes are not meant to be part of the built object and have builder methods. Also Lombok's `@Builder` annotation
doesn't create builder methods for static fields, thus annotating such fields as `@Builder.Default` is superfluous because it has no effect.

| Compliant code | Non-compliant code |
|---|---|
| <pre>@Builder<br>public class SomeBuilder {<br>    private static final int anInteger = 5;<br>}</pre> | <pre>@Builder<br>public class SomeBuilder {<br>    @Builder.Default<br>    private static final int anInteger = 5;<br>}</pre> |

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

| Compliant code | Non-compliant code |
|---|---|
| <pre>public class SomeBuilder {<br>    private final Integer anInteger = 5;<br>}</pre> | <pre>public class SomeBuilder {<br>    @Builder.Default<br>    private final Integer anInteger = 5;<br>}</pre> |

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

When a collection type field in a `@Builder` annotated class is annotated as `@Singular` without having defined a default value,
it is by default initialized as an empty collection, thus initializing it explicitly as an empty collection is superfluous, and can be removed.

| Compliant code | Non-compliant code |
|---|---|
| <pre>public class SomeBuilder {<br>    @Singular<br>    private final List<String> elements;<br>}</pre> | <pre>public class SomeBuilder {<br>    @Singular<br>    private final List<String> elements = new ArrayList()<>;<br>}</pre> |

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
