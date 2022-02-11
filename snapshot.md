In order to try out REST Assured versions before they are released you need to add the following repository in your Maven `pom.xml` file:

```xml
<repositories>
        <repository>
            <id>sonatype</id>
            <url>https://oss.sonatype.org/content/repositories/snapshots/</url>
            <snapshots />
        </repository>
</repositories>
```

The latest snapshot version usually has the same version number as the latest released version but with an increased patch version. For example, if the latest released version is `4.5.1` then the latest snapshot release should be `4.5.1-SNAPSHOT`.
