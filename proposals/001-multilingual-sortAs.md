# Make `sortAs` a language map

* Authors: [Quentin Gliosca](https://github.com/qnga)
* Review PR: [#58](https://github.com/readium/webpub-manifest/pull/58)
* Related Issues:
    * [#48 Sorting keys are language dependent](https://github.com/readium/webpub-manifest/issues/48)


## Summary

RWPM metadata's `sortAs` key is inherently language-dependent. This proposal aims to provide a way to supply multiple sorting keys depending on the language.

## Motivation

At the moment, some RWPM's metadata are multilingual, for example `title` or contributor'`name`s. It's not possible to provide multilingual sorting keys though and it's not clear in which language the unique one should be.


## Proposal

`sortAs` may become a JSON-LD language map as `title` and contributor'`name`s already are. For example, the following snippet could appear in RWPM's metadata.

```json
{
  "title": {
    "en": "The Combat Baker and Automaton Waitress",
    "ja": "戦うパン屋と機械じかけの看板娘"
  },
  "sortAs": {
    "en": "Combat Baker and Automaton Waitress, The",
    "ja": "タタカウパンヤトオートマタンウェイトレス"
  }
}
```

### Backward Compatibility and Migration

On mobile platforms, `LocalizedString` objects called `localizedTitle` and `localizedName` are currently used to store `title` and contributor'`name`s and a simple `String` is used to store the `sortAs` property. A `localizedSortAs` property should be introduced and `sortAs` should map to `localizedSortAs.string` to preserve backward compatibility. The `LocalizedString.string` property uses heuristics to automatically choose a language in place of the developers who might not care about multilanguage capabilities.


## Rationale and Alternatives

A more systematic approach would have been to define a `LocalizedString` objet rightly at the RWPM's level in the way as [W3C Web publication manifests](https://www.w3.org/TR/2020/CR-pub-manifest-20200128/#value-localizable-string) do. This object would have `value`, `language` and `sortAs` properties. A property for text direction might be introduced too.

Making `sortAs` a language map has been assesed as more realistic in terms of backward compatibility.