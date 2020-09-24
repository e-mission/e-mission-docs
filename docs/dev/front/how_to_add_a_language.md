# How to add a language
---

For now, only English, French and Italian are available but in the future we hope to cover more languages. 

## Update the phone app

### Adding a language

For now, each language is declare staticly in `www/js/app.js`.

```js
$translateProvider
.fallbackLanguage('en')
.registerAvailableLanguageKeys(['en', 'fr'], {
    'en_*': 'en',
    'fr_*': 'fr',
    '*': 'en'
})
.determinePreferredLanguage()
.useStaticFilesLoader({
    prefix: 'i18n/',
    suffix: '.json'
});
```

To add a language to the app, we have to register a new language key. If I want to add deutsch, I will modify the file like below:

```js
$translateProvider
.fallbackLanguage('en')
.registerAvailableLanguageKeys(['en', 'fr','de'], {
    'en_*': 'en',
    'fr_*': 'fr',
    'de_*': 'de',
    '*': 'en'
})
.determinePreferredLanguage()
.useStaticFilesLoader({
    prefix: 'i18n/',
    suffix: '.json'
});
```

### Changing the fallback language

By default, the fallback language is english but it can easily be modified by just changing `en` in the line below by the key of your language in `www/js/app.js`.

```js
.fallbackLanguage('en')
```

You can find more information about the fallback language in the [documentation](https://angular-translate.github.io/docs/#/guide/08_fallback-languages). 

### Translating the files

All the text that needs to be translated are in https://github.com/e-mission/e-mission-translate. Please double check the key list against the corresponding `en` versions since the non-en translations are not guaranteed to be updated. The mapping from the translated files to the corresponding `en` files is in the `README`.

## Adding custom translations

If you do not want to use the translation provided by `e-mission-translate` you can either fork the repo or just delete the line below from the `config.xml` to stop using the hook which is pulling the translation from the git repo.

```xml
<hook src="hooks/before_prepare/download_translation.js" type="before_prepare" />
```

If you chose to fork the repo, do not forget to add `bin/conf/translate_config.json` and add the link of the forked repo as below.

```json
{
    "url": "https://github.com/e-mission/e-mission-translate"
}
```
