# Image-comparison

Image comparison on GitHub: https://github.com/romankh3/image-comparison

## Table of contents

#### ImageComparison
- [Set ImageComparison pixel tolerance level will have no effect](#set-imagecomparison-pixel-tolerance-level-will-have-no-effect)
- [ImageComparison instantiation may be simplified](#imagecomparison-instantiation-may-be-simplified)
- [Setters of ImageComparison with default values can be omitted](#setters-of-imagecomparison-with-default-values-can-be-omitted)

## Set ImageComparison pixel tolerance level will have no effect

In the setter of `ImageComparison.setPixelToleranceLevel(double)` there is a condition that the value should be 0.0 <= level < 1, otherwise nothing is set.

| Compliant code | Non-compliant code |
|---|---|
| <pre></pre> | <pre>new ImageComparison(expected, actual).setPixelToleranceLevel(0.0)</pre> |
| <pre></pre> | <pre>new ImageComparison(expected, actual).setPixelToleranceLevel(1)</pre> |

**Script filter ($pixelTolerance$)**

```groovy
try {
    def pixelTol = pixelTolerance?.text?.toDouble()
    return pixelTol < 0.0 || pixelTol >= 1.0
} catch (NumberFormatException e) {
    false
}
```

**Template:**

```xml
<searchConfiguration name="pixelToleranceLevel should be 0.0 &lt;= level &lt; 1, otherwise nothing is set." text="$ImageComparison$.setPixelToleranceLevel($pixelTolerance$)" recursive="true" caseInsensitive="true" type="JAVA" pattern_context="default">
  <constraint name="__context__" within="" contains="" />
  <constraint name="ImageComparison" nameOfExprType="com\.github\.romankh3\.image\.comparison\.ImageComparison" expressionTypes="com.github.romankh3.image.comparison.ImageComparison" within="" contains="" />
  <constraint name="pixelTolerance" script="&quot;try {&#10;    def pixelTol = pixelTolerance?.text?.toDouble()&#10;    return pixelTol &lt; 0.0 || pixelTol &gt;= 1.0&#10;} catch (NumberFormatException e) {&#10;    false&#10;}&quot;" target="true" within="" contains="" />
</searchConfiguration>
```

## ImageComparison instantiation may be simplified

These are only when being created inline with the instantiation, otherwise it can't really be made sure that the inspection will show correct results.

| Compliant code | Non-compliant code |
|---|---|
| <pre>ImageComparison(expectedImage, actualImage)</pre> | <pre>ImageComparison(expectedImage, actualImage, null)</pre> |

**Template:**

```xml
<replaceConfiguration name="Instantiation can be simplified. Apply quick fix to use a simpler constructor." text="new com.github.romankh3.image.comparison.ImageComparison($bufferedImage1$, $bufferedImage2$, null)" recursive="false" caseInsensitive="true" type="JAVA" pattern_context="default" reformatAccordingToStyle="false" shortenFQN="true" useStaticImport="true" replacement="new com.github.romankh3.image.comparison.ImageComparison($bufferedImage1$, $bufferedImage2$)">
    <constraint name="__context__" within="" contains="" />
    <constraint name="bufferedImage1" within="" contains="" />
    <constraint name="bufferedImage2" within="" contains="" />
</replaceConfiguration>
```

## Setters of ImageComparison with default values can be omitted

Each of the following setters of `ImageComparison` has a default value for which explicit calls are not necessary.

The following setter method calls are all incorrect 

```java
ImageComparison imageComparison = new ImageComparison(expected, actual);

imageComparison.setPixelToleranceLevel(0.1);
imageComparison.setThreshold(5);
imageComparison.setRectangleLineWidth(1);
imageComparison.setMinimalRectangleSize(1);
imageComparison.setMaximalRectangleCount(-1);
imageComparison.setDrawExcludedRectangles(false);
imageComparison.setDrawExcludedRectangles(true).setRectangleLineWidth(1); //due to setRectangleLineWidth(1)
imageComparison.setDrawExcludedRectangles(false).compareImages(); //due to setDrawExcludedRectangles(false). compareImages() is ignored by the Script filter
```

The idea behind the Script filter below is the following:
- iterate through the methods in the call chain (it happens in reverse order due to the PSI tree structure)
- if the inspected method is not a setter with a proper value, skip the inspection of that method continue checking the next method call.
Skip happens when:
    - the method name doesn't start with "set", or
    - the method has no parameter defined, or
    - the method has a parameter but it's not a literal value
- if the parameter value of the currently inspected method matches the default value of its respective field,
then flag the call chain as incorrect, and skip all other methods in the chain.

Here are the reasons behind why the inspection of some method calls are skipped:
- **the method name doesn't start with set**: since this template is about validating setter method parameter values, it doesn't really make
sense to inspect non-setter methods.

    ```java
    imageComparison.setRectangleLineWidth(1).compareImages();
    ```

- **no setter parameter value is defined**: it might happen that the setter method call has already had a parameter specified,
but the user deletes the value to set another one. In that case the script filter would throw an exception due to not being able to find and validate
the a literal param value. Thus skipping checking the value is in this case make sense.

    ```java
    imageComparison.setRectangleLineWidth();
    ```

- **the setter parameter value is not a literal**: it might happen that the value passed to a setter method is not a literal value
but instead e.g a method call returning the value. Validation is skipped in this case as well to keep the Script filter as simple as possible.

    ```java
    imageComparison.setDrawExcludedRectangles(getRectangles());
    ```

**Script filter (Complete match)**

```groovy
import com.intellij.psi.*;

def setterDefaults = [setPixelToleranceLevel: 0.1f, setThreshold: 5, setRectangleLineWidth: 1, setMinimalRectangleSize: 1, setMaximalRectangleCount: -1, setDrawExcludedRectangles: false]
PsiExpression methodCall = setterMethod; //PsiExpression is the closest common superclass of PsiMethodCallExpression and PsiReferenceExpression
//This iterates through the method call in a reverse order, from the last to the first
while (methodCall instanceof PsiMethodCallExpression) {
	def methodName = methodNameOf(methodCall)
	//Ignoring non-setter methods in the call chain, e.g. imageComparison.setRectangleLineWidth(1).compareImages();
	if (!methodName.startsWith("set") || !hasParameterDefined(methodCall) || !isFirstParamLiteral(methodCall)) {
		methodCall = methodCall.firstChild?.firstChild
		continue
	}
	def methodParamValue = methodParamValueOf(methodCall)
	if (methodParamValue == setterDefaults[methodName]) {
		return true
	}
	methodCall = methodCall.firstChild?.firstChild //firstChild: PsiReferenceExpression, firstChild.firstChild: PsiMethodCallExpression
}
return false //fallback return value

String methodNameOf(PsiMethodCallExpression expr) {
	expr.firstChild.referenceName //firstChild: PsiReferenceExpression
}

boolean hasParameterDefined(PsiMethodCallExpression expr) {
	expr.lastChild.expressions.length > 0
}

boolean isFirstParamLiteral(PsiMethodCallExpression expr) {
	expr.lastChild.expressions[0] instanceof PsiLiteralExpression
}

Object methodParamValueOf(PsiMethodCallExpression expr) {
	expr.lastChild.expressions[0].value //lastChild: PsiExpressionList, lastChild.expressions[0]: PsiLiteralExpression
}
```

**Template:**

```xml
<searchConfiguration name="ImageComparion setter method is defined with default value. That setter has no effect." text="$ImageComparison$.$setterMethod$($setValue$);" recursive="true" caseInsensitive="true" type="JAVA" pattern_context="default">
    <constraint name="__context__" script="&quot;import com.intellij.psi.*;&#10;&#10;def setterDefaults = [setPixelToleranceLevel: 0.1f, setThreshold: 5, setRectangleLineWidth: 1, setMinimalRectangleSize: 1, setMaximalRectangleCount: -1, setDrawExcludedRectangles: false]&#10;PsiExpression methodCall = setterMethod; //PsiExpression is the closest common superclass of PsiMethodCallExpression and PsiReferenceExpression&#10;//This iterates through the method call in a reverse order, from the last to the first&#10;while (methodCall instanceof PsiMethodCallExpression) {&#10;&#9;def methodName = methodNameOf(methodCall)&#10;&#9;//Ignoring non-setter methods in the call chain, e.g. imageComparison.setRectangleLineWidth(1).compareImages();&#10;&#9;if (!methodName.startsWith(&quot;set&quot;) || !hasParameterDefined(methodCall) || !isFirstParamLiteral(methodCall)) {&#10;&#9;&#9;methodCall = methodCall.firstChild?.firstChild&#10;&#9;&#9;continue&#10;&#9;}&#10;&#9;def methodParamValue = methodParamValueOf(methodCall)&#10;&#9;if (methodParamValue == setterDefaults[methodName]) {&#10;&#9;&#9;return true&#10;&#9;}&#10;&#9;methodCall = methodCall.firstChild?.firstChild //firstChild: PsiReferenceExpression, firstChild.firstChild: PsiMethodCallExpression&#10;}&#10;return false //fallback return value&#10;&#10;String methodNameOf(PsiMethodCallExpression expr) {&#10;&#9;expr.firstChild.referenceName //firstChild: PsiReferenceExpression&#10;}&#10;&#10;boolean hasParameterDefined(PsiMethodCallExpression expr) {&#10;&#9;expr.lastChild.expressions.length &gt; 0&#10;}&#10;&#10;boolean isFirstParamLiteral(PsiMethodCallExpression expr) {&#10;&#9;expr.lastChild.expressions[0] instanceof PsiLiteralExpression&#10;}&#10;&#10;Object methodParamValueOf(PsiMethodCallExpression expr) {&#10;&#9;expr.lastChild.expressions[0].value //lastChild: PsiExpressionList, lastChild.expressions[0]: PsiLiteralExpression&#10;}&quot;" within="" contains="" />
    <constraint name="ImageComparison" nameOfExprType="com\.github\.romankh3\.image\.comparison\.ImageComparison" expressionTypes="com.github.romankh3.image.comparison.ImageComparison" within="" contains="" />
    <constraint name="setValue" minCount="0" maxCount="2147483647" within="" contains="" />
    <constraint name="setterMethod" within="" contains="" />
</searchConfiguration>
```
