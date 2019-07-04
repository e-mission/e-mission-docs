# Guidelines to support i18n
---

The phone app now supports i18n. In order to do that, we put most of the strings seen by the user in external files translated in different languages. To ensure i18n for the application, you have to follow some guidelines.

## While working on native Android

Android provides a native support to localize its applications by using `resources`. By default, Android is storing its strings in **xml** files in the `res/values/`directory. If you want to localize those strings, you can create another `values-<language>` folder which will contains those same **xml** files but with your translations.

```xml
<?xml version="1.0" encoding="utf-8"?>
<resources>
    <string name="<key name>">value</string>
</resources>
```

When you are creating your plugin for **e-mission**, you will want to put your strings into an external **xml** file to ensure the i18n support and then place the file in `res/values`.  You can then call those strings in your code by using:

```java
context.getString(R.string.<key of your string>);
```

It is also important to add the resource file in the `plugin.xml`, you can do it by adding the following line into it which will copy the file in the right directory when adding the plugin.

```xml
<resource-file src="res/android/values/<filename>.xml" target="res/values/<filename>.xml"/>
```
You can find further information in the [Android documentation](https://developer.android.com/guide/topics/resources/localization). 

## While working on native iOS

iOS uses the same logic as Android but the strings are placed in `strings` files instead of `xml`. Moreover, those files are placed in an `en.lproj` directory and the translated files in a `<language>.lproj` directory.

If you want to call those strings in your code, you can use `NSLocalizedStringFromTable` in Objective-C.

```objc
NSLocalizedStringFromTable(@"key", @"filename", nil)
```

Like Android, do not forget to add the following line in your `plugin.xml`.

```xml
<resource-file src="res/ios/en.lproj/<filename>.strings" target="en.lproj/<filename>.strings"/>
```

You can find further information in the [iOS documentation](https://developer.apple.com/library/archive/documentation/MacOSX/Conceptual/BPInternational/LocalizingYourApp/LocalizingYourApp.html#//apple_ref/doc/uid/10000171i-CH5-SW1).  

## While working on the Cordova phone app

To support i18n in the phone app, we are using [angular-translate](https://angular-translate.github.io/). Each strings will be placed into the file `i18n/en.json`. You can define a *namespace* when you are working on a specific file to ensure a better readability.

```json
    "trip-confirm": {
        "recenttrip": "Recent trip from: {{startTime}} &rarr;  to: {{endTime}}",
        "continue": "Continue",
        "done": "Done",
        "services-please-fill-in": "Please fill in the {{text}} not listed.",
        "services-cancel": "Cancel",
        "services-save": "Save"
    }
```

### In HTML

When you want to read a string in your HTML file, you can do it by adding `translate` to the HTML tag and by putting `{'key of the string'}` between the tags. 

```html
<div translate>{{'trip-confirm.recenttrip'}}</div>
```

You can also define a namespace for the child of the tag by adding `translate-namespace=<name of the namespace>`. 

```html
<div translate-namespace="trip-confirm">
    <div translate>{{'.recenttrip'}}</div>
</div>
```

It is also possible to use variables within translations by simply adding the variables into the translation and then interpolating them by adding `translate-values={variablename: variable}`.

```html
<div translate=".recenttrip" translate-values="{startTime: getFormattedTime(mapCtrl.start_ts), endTime: getFormattedTime(mapCtrl.end_ts)}"></div>
```

You can find more information about **Variable Replacement** in the [doc](https://angular-translate.github.io/docs/#/guide/06_variable-replacement).

### In JavaScript

>**WARNING**: Do not forget to add `$translate` to your controller or service. 

If you want to retrieve the language used by the user, you can use:

```js
$translate.use();
```

To translate a string directly in the Javascript, you can use:

```js
$translate.instant('key of the string');
```

### Working with date

Because each language has different format for date and hour, we are using the JavaScript library [moment](https://momentjs.com/) to handle that. 

To display hours in the adequate format, you can use: 

```js
moment(dt).format("LT");
```

To display date: 

```js
moment(day).format('LL');
```

You can also use the following lines to retrieve weekdays and months:

```js
moment.weekdaysMin()
moment.monthsShort()
```
