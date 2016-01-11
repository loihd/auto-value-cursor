# AutoValue: Cursor Extension

An extension for Google's [AutoValue][auto] that generates a `createFromCursor(Cursor c)` method for AutoValue annotated objects.

**Note**: This is an early version that requires the extension support currently in AutoValue 1.2-SNAPSHOT.

## Usage

Include auto-value-cursor in your project and add a static factory method to your auto-value object.

```java
import com.gabrielittner.auto.value.cursor.ColumnName;

@AutoValue public abstract class User {
  abstract String id();
  abstract String name();
  // use the annotation if column name and field name aren't the same
  @ColumnName("email_address") abstract String email();

  public static User create(Cursor cursor) {
    return AutoValue_User.createFromCursor(cursor);
  }

  // if your project includes RxJava the extension will generate a Func1<Cursor, User> for you
  public static Func1<Cursor, User> mapper() {
    return AutoValue_User.MAPPER;
  }
}
```

**Important:** The extension will only be applied when there is (replace `User` with your own class)
- a static method that returns `User` and takes a `Cursor` as parameter
- a static method that returns a `Func1<Cursor, User>`
- or both mentioned methods

The following types are supported by default:

 * `byte[]`/`Byte[]`
 * `double`/`Double`
 * `float`/`Float`
 * `int`/`Integer`
 * `long`/`Long`
 * `short`/`Short`
 * `String`
 * `boolean`/`Boolean`

For other types, you need to use the `@CursorAdapter` annotation and specify a factory
class that will be used to construct the type from the `Cursor` object. By convention, this factory
class needs to have a public static method that takes the `Cursor` and returns the
custom type. Eg.:

`User.java`:

```java
@AutoValue public abstract class User {
  abstract String id();
  abstract String name();
  @AutoValueCursorFactory(AvatarFactory.class) Avatar avatar();

  public static User createFromCursor(Cursor cursor) {
    return AutoValue_User.createFromCursor(cursor);
  }
}
```

`AvatarFactory.java`:

```java
public class AvatarFactory {
  public static Avatar createFromCursor(Cursor cursor) {
    String smallImageUrl = cursor.getString(cursor.getColumnIndex("small_image_url");
    String largeImageUrl = cursor.getString(cursor.getColumnIndex("large_image_url");
    return new Avatar(smallImageUrl, largeImageUrl);
  }
}
```

`Avatar.java`:

```java
public class Avatar {
  private final String smallImageUrl;
  private final String largeImageUrl;

  public Avatar(String smallImageUrl, String largeImageUrl) {
    this.smallImageUrl = smallImageUrl;
    this.largeImageUrl = largeImageUrl;
  }
}
```

## Download

Add a Gradle dependency:

```groovy
apt 'com.gabrielittner.auto.cursor:auto-value-cursor:0.2-SNAPSHOT'
// if you need the @ColumnName annotation also include this:
provided 'com.gabrielittner.auto.cursor:auto-value-cursor-annotations:0.2-SNAPSHOT'
```
(Using the [android-apt][apt] plugin)

Snapshots of the development version are available in [Sonatype's `snapshots` repository][snap].

## License

This project is heavily based on [Ryan Harter][ryan]'s [auto-value-gson][auto-gson]

```
Copyright 2015 Ryan Harter.
Copyright 2015 Gabriel Ittner.

Licensed under the Apache License, Version 2.0 (the "License");
you may not use this file except in compliance with the License.
You may obtain a copy of the License at

   http://www.apache.org/licenses/LICENSE-2.0

Unless required by applicable law or agreed to in writing, software
distributed under the License is distributed on an "AS IS" BASIS,
WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
See the License for the specific language governing permissions and
limitations under the License.
```



 [auto]: https://github.com/google/auto
 [snap]: https://oss.sonatype.org/content/repositories/snapshots/
 [apt]: https://bitbucket.org/hvisser/android-apt
 [ryan]: https://github.com/rharter/
 [auto-gson]: https://github.com/rharter/auto-value-gson

