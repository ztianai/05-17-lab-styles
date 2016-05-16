# Lab: Styles & Themes
For this lab, you will learn to use Android [Styles & Themes](http://developer.android.com/guide/topics/ui/themes.html) to easily modify and _abstract_ the appearance of an app's user interfaces---that is, the XML resource attributes that define what your `Views` look like.

- I recommend you keep the above documentation guide open as a reference, though this lab will walk you through the steps as well.

Check out and open the code as normal. You'll be working almost exclusively with the **XML resources** (e.g., `res/layout/activity_main.xml`), so make sure to read over that first: it includes a very simple screen showing a pile of `TextViews` organized in a `RelativeLayout`.


## Defining Styles
If you look at the `TextViews`, you'll see that they share a lot of the same attributes: text sizing, width and height, boldness, etc. If I told you that all the text should be bigger (e.g., for readability), then you'd need to change like 6 different attributes, which is a lot of work.

Enter [**Styles**](http://developer.android.com/guide/topics/ui/themes.html#DefiningStyles). Styles ___encapsulate a collection of XML properties___, allowing you to define a set of properties once and then use a single attribute to apply all of those properties to a view.

- This is _almost_ like a [CSS](https://en.wikipedia.org/wiki/Cascading_Style_Sheets) style, but without the "cascading" part.

Styles are themselves defined as an [XML resource](http://developer.android.com/guide/topics/resources/style-resource.html)---specifically a `<style>` element inside the **`res/values/styles.xml`** file.

- This XML file was created for us when Android Studio created our project. Open the file, and you can see that it even has some content in it! 

- Style resource files, like String resource files, use `<resource>` as a top-level element, declaring that this XML file contains (generic-ish) resources to use.

Styles are declared with a `<style>` tag, which represents a **single** style. You can _almost_ think of this as a "class" in CSS (though again, without the cascading behavior).

- The `<style>` element is given a `name` attribute, similar to how we'd define a CSS class name. Names are normally written using [PascalCase](http://c2.com/cgi/wiki?PascalCase); they need to be legal Java identifiers since will be compiled into `R`, and since they are "classes" we capitalize them!

- We'll get to the `parent` attribute in the next part.

**Inside** the `<style>` element we define `<item>` elements. Each `<item>` represents a _single_ attribute we want our style to include. This is similar to each item being a _property_ of a CSS class.

- `<item>` elements get a `name` attribute which is the the name of the property you want to include. For example: `name="android:layout_width"` to specify that this item refers to the `layout_width` attribute.

- The _content_ of the `<item>` tag is the value we want to assign to that attribute, e.g., `wrap_content` (not in quotes, because the content of an XML tag is already a String!)

- Again, see the [resource documentation](https://developer.android.com/guide/topics/resources/style-resource.html) for more examples.

Finally, you can specify that you want a particular `View` (e.g., in your `layout`) to have a style by giving that view a `style` attribute, with a value that references the style that you've defined (using `@style/...`, since this resource is of type "style").

- Note that the `style` attribute does **not** use the `android` namespace!

### Task \#1
Define a new style (e.g., `TextStyle`) that defines the attributes _shared_ by the 6 `TextViews`: the text size, the with, and the height. Additionally, have the style define the text color as [UW purple](http://www.washington.edu/brand/graphic-elements/primary-color-palette/).

Refactor these TextViews so that they use the style you just defined _instead of_ duplicating attributes. See how much code you've saved?

___After you've done that___ and demonstrated it works, go ahead and change the size of _all_ the `TextViews` to be `22dp`. You should be able to make this change in exactly one place!


## Style Inheritance
This is a good start, but we still have some duplicated attributes--in particular, the "labels" share a couple of attributes (e.g., they are all bold). Since each View can only have a single style and there is no cascading, if we wanted to create a separate "TextLabel" style, it would end up having some duplicated attributes between them (size and color). It would be nice to not have to redefine a style if it only changes a little bit.

Luckily, while styles don't cascade, they can [**inherit**](http://developer.android.com/guide/topics/ui/themes.html#Inheritance) from one another (a la Java inheritance, e.g., `extends`). We can do this by specifying the `parent` attribute for the `<style>`, and having it reference (with `@`) the "parent" style:

```xml
<style name="ChildStyle" parent="@style/ParentStyle"> ... </style>
```

This will cause the `ChildStyle` to include all of the `<item>` tags defined in the `parent` style.

- We can then "override" the inherited properties by redefining the `<item>` you want to change, just like when inheriting and overriding Java methods.

When inheriting from our own _custom styles_ (e.g., ones that we've defined within the same package), it's also possible to use **Dot Notation** _instead of_ the `parent` attribute. The dot notation is used to "namespace" the inherited class like it was a "nested" class we wanted to reference.

- For example, naming a style `ParentStyle.ChildStyle` will define a style (`ChildStyle`) that inherits from `ParentStyle`. This would be referenced in the layout as `@style/ParentStyle.ChildStyle`.

- We can chain these together as much as we want: `MyStyle.Red.Big` (though that would be distinct from the `MyStyle.Big.Red` style! It's not doing CSS combining, but Java class inheritance!)

### Task \#2
Define another style (e.g., `Label`) that **inherits** from your first style to encapsulate attributes shared by the labels (e.g., boldness). Refactor your `layout` so that the labels use this new style.

Define _another_ style (e.g., `Gold`) that **inherits** from your `Label`'s style and has a [background color](https://developer.android.com/reference/android/view/View.html#attr_android:background) that is [UW Gold](http://www.washington.edu/brand/graphic-elements/primary-color-palette/) and a text color of black. Apply this style to _one_ of your labels.


### Built-in Styles
Android also includes a large number of [built-in platform styles](http://developer.android.com/guide/topics/ui/themes.html#PlatformStyles) that we can apply and/or inherit from. These **must** be inherited via the `parent` attribute (you can't use dot notation for them). They are referenced with the format:

```xml
<style name="MyStyle" parent="@android:style/StyleName">...</style>
```

There are a bunch of these... and Android's recommendation is to understand them by browsing the [source code](https://android.googlesource.com/platform/frameworks/base/+/refs/heads/master/core/res/res/values/styles.xml) and seeing how they are defined.

- This makes discoverability difficult---it's a lot like trying to learn Bootstrap by reading the CSS file.

- You can also look at a list of defined in the [R.style](http://developer.android.com/reference/android/R.style.html) class.

- Honestly, most of the styles are not very effective bases for inheritance; you're often better using your own.

    - But look and find the `TextAppearance` style that the docs recommend. Does it seem worthwhile to inherit from this?

### Task \#3
As practice, define a new style for the `Button` at the bottom of the screen that inherits from `MediaButton`, but has a size of `22dp`. What does the inheritance do to the appearance?


## Themes
Unlike CSS, Android styles do not _cascade_: that is, if you apply a style to a `ViewGroup` (a layout), that style will not be applied to all the components of that `ViewGroup`. 

But what we can do is have a style apply to _every_ `View` in a `Context` (an **Activity** or an **Application**) by applying that style as a [**Theme**](http://developer.android.com/guide/topics/ui/themes.html#ApplyATheme)

- Themes are styles that are applied to every single `View` in the whole application or in a particular Activity; you can't get any finer granularity (without moving to per-View _Styles_). Theme styles will apply to _every_ View in the context, though we can overwrite specify styling for a View as normal.

Themes _are_ styles, and so are defined the exact same way (as `<style>` elements inside a resource XML file. You can define them in either `styles.xml`, `theme.xml`, or any other `values` file---resource filenames are arbitrary, and their content will still be compiled into `R` no matter what file that content is in.

Themes are applied to an Activity or Application by specifying a `android:theme` attribute in the `Manifest` (where the Activity/Application is defined).

- If you look at the project's `Manifest` created by Android Studio, you'll see that it already has a theme (`AppTheme`). In fact, this is the `<style>` that was provided inside `styles.xml`!

### Task \#4
**Experiment with removing the theme attribute from the application** How does your app's appearance change? _NOTE_: You will need to change `MainActivity` to subclass `Activity`, not `AppCompatActivity`.

- You might also try commenting out the stylings you applied to the individual `TextViews` to really see what happens.


## Material Themes
Along with styles, Android provides a number of platform-specific themes. And again, the somewhat unhelpful recommendation is to understand these by browsing the [source code](https://android.googlesource.com/platform/frameworks/base/+/refs/heads/master/core/res/res/values/themes.xml), or the list in [R.style](http://developer.android.com/reference/android/R.style.html) (scroll down to constants that start with `Theme`).

One of the most useful set of Android-provided themes are the [**Material Themes**](http://developer.android.com/training/material/theme.html). These are themes that support Google's [Material Design](http://www.google.com/design/spec/material-design/introduction.html), a visual design language Google uses (or aims to use) across its products, including Android.

- Material Design includes a set of themes, widgets (lists and cards), animations, etc. Explore the [Material design docs](http://developer.android.com/design/material/index.html) for more details.

- Material Themes are only available in API 21+, though there are some compatibility options.

There are two main Material themes:

- `@android:style/Theme.Material` a Dark version of the theme
- `@android:style/Theme.Material.Light` a Light version of the theme

And a bunch of variants:

- `@android:style/Theme.Material.Light.DarkActionBar` a Light version with a Dark action bar
- `@android:style/Theme.Material.Light.LightStatusBar` Variant of the material (light) theme that has a light status bar background with dark status bar contents.
- `@android:style/Theme.Material.Light.NoActionBar` Variant of the material (light) theme with no action bar.
- ... etc. See [R.style](http://developer.android.com/reference/android/R.style.html) for more (do a ctrl-f "find" for `Material`)

### Task \#5
**Experiment with applying different material themes to your application** How does your app's appearance change?


## Theme Attributes
One of the big advantages of Themes is that they can be used to define attributes that can be [referenced](http://developer.android.com/guide/topics/resources/accessing-resources.html#ReferencesToThemeAttributes) from inside individual, per-View Styles.

- For example, a Theme could define a "color scheme" as a set of variables; these variables can then be references by the `Style`, allowing the style to use "the primary color of the current theme".

Theme-level attributes are referenced in the XML using the `?` symbol (in place of the `@` symbol). For example: `?android:textColorPrimary` will refer to the value of the `<item name="textColorPrimary">` element inside the theme.

Indeed, one of the advantages of the Material Themes (and Material-themed widgets) is that they are implemented to utilize a small set of color theme attributes, making it incredibly easy to specify a color scheme for your app. See [Customize the Color Palette](https://developer.android.com/training/material/theme.html#ColorPalette) for a diagram of what attributes color what parts of the screen.

- It is possible to apply a Theme to an individual `View` or `ViewGroup`, but all that "cascades" is these theme attributes, which need to be explicitly references by the component `Views`.

### Task \#6
Redefine the colors in your custom Styles (from Task 1 and 2) so that they reference the theme colors instead of purple and gold. What happens now when you change the application's Material theme between light and dark?

- Can you have the logo image reference those color attributes as well? Hint: use the `tint` attribute.

Lastly, set the theme of your app back to the provided `AppTheme`. Return to the `styles.xml` page and modify the custom `AppTheme` style:

- Have it **inherit** from a Material Theme (your choice of which)

- Have it define theme attribute colors using the [UW colors](http://www.washington.edu/brand/graphic-elements/primary-color-palette/) (purple and gold). These theme colors should now have references by your custom Styles, allowing you to once again Huskify your app.


---

That covers the basics of Styles and Themes. These are not _necessary_ (you can just define XML attributes as we've been doing all quarter long), but can make it easier to customize and adjust your layouts as they change through the power of **abstraction**.
