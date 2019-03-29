# Just for fun

#### Nem röhög!
Marks every class that's name ends with Facade. Only Hungarians will get this joke.
```xml
<searchConfiguration name="Nem röhög!" text="class $Class$ {}" recursive="true" caseInsensitive="true" type="JAVA">
    <constraint name="__context__" within="" contains="" />
    <constraint name="Class" script="&quot;!__context__.interface &amp;&amp; !__context__.enum&quot;" regexp=".*Facade$" target="true" within="" contains="" />
</searchConfiguration>
```
#### Get your shit together, mate...
Marks words that may be a signal that you are lazy.
```xml
<searchConfiguration name="Get your shit together, mate..." text="$Something$" recursive="true" caseInsensitive="true" type="JAVA">
    <constraint name="__context__" within="" contains="" />
    <constraint name="Something" regexp="&quot;?(something|whatever|nevermind|idontknow|whocares|anything|anywho|whoever|justbecause)&quot;?" within="" contains="" />
</searchConfiguration>
```

#### Oh, come on! Please don't swear, you son of a bitch!
When you are pissed and you may have been working on the same boring sh*t, you might swear a bit that can end up on your pull request. You don't want that to happen.
```xml
<searchConfiguration name="Oh, come on! Please don't swear, you son of a bitch!" text="$Something$" recursive="true" caseInsensitive="true" type="JAVA">
    <constraint name="__context__" within="" contains="" />
    <constraint name="Something" regexp="&quot;?(lofasz|fuck|shit)&quot;?" within="" contains="" />
</searchConfiguration>
```
