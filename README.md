# Noodl i18next module
Module for using [i18next](https://www.i18next.com) in Noodl.
The module has three nodes, i18next, Language Bundle and Translation.

# i18next
This is used to register bundles and to set and get language.
## Inputs
### Language (string)
### ChangeLanguage (signal)

These two are used in combination to set a new language. The Language string should be a language code that is then being matched by the language bundle. For example `en` for english.

## Outputs
### OutputsLanguageChanged (signal)
### CurrentLanguage (string)

LanguageChanged is triggered whenever the language is changed, for example using the ChangeLanguage signal. If, for example, your Noodl project needs to take action when the language is changed, you use this signal.CurrentLanguage contains the code of the currently set language.

# LanguageBundle

The language bundle contains the translations for a language. If you have multiple languages you will have one LanguageBundle for each language and they generally should contain the same strings, but translated to the respective language.
Each language bundle also has a "Namespace". This can be used to separate different parts of translations to different bundles that can e.g. be loaded at different times. For example you can have one bundle for static UI-texts called "UI" and one bundle for texts that are dynamically loaded depending on content in the database called "Dbtexts". If you support two languages, english and spanish, this would mean that you have four language bundle nodes: UI and Dbtexts for english and spanish.

The bundle itself is a json-object, as described on i18next website. It contains key:string pairs for each text. It can also include an inner structure (json objects) to help organize strings based on features. For example a bundle for english language could be

```json
{
  "loginpage":{
    "user_name":"User Name",
    "confirm_button":{
      "label":"Confirm",
      "hover_text":"Press here to confirm"
    }
}
```

In that example the key `loginpage.confirm_button.hover_text` would identify the button hover text translation when used in a Translation node.
Note that i18next supports various dynamic features in the texts, e.g. plurals, date formats, etc. These are generally supported when used in Noodl.

There are typically two ways to use Language Bundles. For static texts, for example UI texts, you edit the bundle object directly in Noodl (pressing the "Edit" button on the property panel). As you build the UI you add new texts to the bundle. You have one bundle per language.For dynamic texts, for example loaded during execution from a database, you programatically create bundle objects and load them into the LanguageBundle when available and needed using AddBundleObject.

## Inputs
### Language (string)
The language that this bundle applies to. Typically a language code, e.g. `en`.
### Namespace (string)
the name of the Namespace. Can by any string.

### AddResource (signal)
### ResourceKey (string)
### ResourceValue (string)
These three inputs are used in combination to add a single new key/string to the bundle.

### AddBundleObject (signal)
### Resource Bundle Object (javascript object)
These two inputs are used in combination to add a complete Bundle object to the bundle.

# Outputs
## BundleLoaded (signal)
Triggered when a new bundle object has been loaded.

# Translation
This is the node where the translation happens. You typically connect these to your text-nodes and textinput-nodes. Translation-nodes automatically changes its output if language changes, if the bundle is changing or becomes available. The translation node have two dynamic features.
If your translation includes some of i18next dynamic features, for example having a `{{count}}` variable deciding between a plural string or not, or by inserting variables in the middle of the string, any variables used can be added as inputs on the Translation node, and be connected to other nodes in Noodl. For example if a translation uses the variable `{{count}}` adding an input named "count" and connecting it to a Number node, the translation will change when the Number node is updated.
The other dynamic feature is the naming of the key in the Translation Node. While in most cases the key of a translation is know when building the app, there might be cases when the exact name of the key is know only during execution. For example, in a database with thousands of products with related texts translated to multiple languages, the translations are generated when needed. The name of the keys are also generated dynamically. A product with an id xyz123abc may have its translations stored in an object named xyz123abc, `{"xyz123abc":{"label":"Product A", "desc":"A great product"}}`. By using dynamic naming of the key in a Translation node, using a {}-pattern, the actual key can be resolved at runtime. In this scenario naming the Key in the translation node `{product_id}.label`, will expose a new input to the Translation node called "product_id" that can be connected to a Model node in Noodl.


## Inputs
### Key (string) 
The key of the translation. This can be a static text or a text including one or more variables using `{var}` syntax. Variables will become available as inputs.
### Namespace (string)
The namespace that this translation is using. Should match a namespace of a Language Bundle.

Also, you can add custom inputs to the translation node. They will be mapped to any variable in the translated text itself (`{{var}}`) to make use of the i18next transformation functionality for example for plurals.

## Outputs
### Translation (string)
This contains the translated text.



