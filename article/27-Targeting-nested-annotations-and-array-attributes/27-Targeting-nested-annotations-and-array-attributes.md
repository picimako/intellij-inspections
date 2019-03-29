# Targeting nested annotations and array attributes
You can find a couple of examples for targeting annotations in [previous posts](https://ijnspector.wordpress.com/tag/annotation/), but there are two special cases which needs a little bit special treatment.

## Nested annotation and array attribute values
For this example I am going to use Selenium's `@FindAll` annotation in which you can define one or multiple `@FindBy` annotations, so this is the perfect one
to discover the possibilities of templates in case of both use cases.

### Validating the count of nested annotations
I came up with an arbitrary inspection that **FindAll annotation should not use more than three FindBy nested annotations**, so in it I configured the Count filter of `$FindBy$` as 3-Unlimited:

```java
class $Class$ { 
    @$FindAll$({@$FindBy$( )}) 
    @Modifier("Instance") $FieldType$ $Field$ = $Init$;
}
```

This marks nested annotated fields only where the enclosing **{** and **}** characters are present, e.g. this one:

```java
@FindAll({@FindBy(css = ".tardis"), @FindBy(className = "tardis-class"), @FindBy(className = "tardis-class2")})
private Tardis tardis;
```

but won't mark code samples like this:

```java
@FindAll(@FindBy(css = ".other-tardis"))
private Tardis otherTardis;
```

## Other resources

If you want to target annotation attributes you can check out the following posts:
- [Annotation with a certain attribute value is invalid](https://ijnspector.wordpress.com/2018/11/05/annotation-with-a-certain-attribute-value-is-invalid/)
- [Annotation is used with default attribute value](https://ijnspector.wordpress.com/2018/11/09/annotation-is-used-with-default-attribute-value/)
- [Annotation attribute value is greater than the max allowed](https://ijnspector.wordpress.com/2019/01/30/annotation-attribute-value-is-greater-than-the-max-allowed/)
- [Targeting method parameters in method signatures](https://ijnspector.wordpress.com/2019/03/22/targeting-method-parameters-in-method-signatures/)

I will keep updating this post when I find new use cases that require specific explanation.  
