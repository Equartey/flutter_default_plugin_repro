# default_plugin_repro

A sample repro to reproduce the following error message:

```
Package amplify_secure_storage:linux references amplify_secure_storage_dart:linux as the default plugin, but the
package does not exist.
Ask the maintainers of amplify_secure_storage to either avoid referencing a default implementation via `platforms:
linux: default_package: amplify_secure_storage_dart` or create a plugin named amplify_secure_storage_dart.


Package amplify_secure_storage:windows references amplify_secure_storage_dart:windows as the default plugin, but the
package does not exist.
Ask the maintainers of amplify_secure_storage to either avoid referencing a default implementation via `platforms:
windows: default_package: amplify_secure_storage_dart` or create a plugin named amplify_secure_storage_dart.
```

## Repo Steps

1. Clone repro
2. Checkout Flutter master channel, `flutter channel master`
   1. Error is present atleast between `>=3.24.0-1.0.pre.151 >3.24.0-1.0.pre.180`
3. Run `$ flutter pub get`.
4. Error should be present. App will also fail to compile.
   1. Alternative: switch to Flutter stable channel and dependencies resolve along with the app compiling.

## Context

The Amplify Secure Storage plugin uses the [current documented method](https://docs.flutter.dev/packages-and-plugins/developing-packages#endorsed-implementations) of defining a endorsed package using the key: `default_package`.

[amplify_secure_storage pubspec.yaml](https://github.com/aws-amplify/amplify-flutter/blob/0c80cdddc2d80c6599ecb94cd1e1d277e61238a5/packages/secure_storage/amplify_secure_storage/pubspec.yaml#L29-L44)

```yaml
flutter:
  plugin:
    platforms:
      android:
        package: com.amazonaws.amplify.amplify_secure_storage
        pluginClass: AmplifySecureStoragePlugin
      ios:
        pluginClass: AmplifySecureStoragePlugin
      macos:
        pluginClass: AmplifySecureStoragePlugin
      windows:
        default_package: amplify_secure_storage_dart
      linux:
        default_package: amplify_secure_storage_dart
      web:
        default_package: amplify_secure_storage_dart
```

This is no longer a valid method on the Master channel. This appears to be a bug related to the introduction of [feat: Support overriding native endorsed plugins #137040](https://github.com/flutter/flutter/pull/137040). As of now, no documentation has been updated or proposed around not using `default_package`. [Reference in-flight docs](https://github.com/flutter/website/pull/10891).

Acknowledging the error message says to use `pluginClass` or `dartPluginClass`, doing so does resolve the error. However, it is unclear why the previous method is no longer valid.

---

For reference: [amplify_secure_storage_dart pubspec.yaml](https://github.com/aws-amplify/amplify-flutter/blob/e339a4b4099f976b3a616fa18fbe263b4ab5b1ba/packages/secure_storage/amplify_secure_storage_dart/pubspec.yaml#L47-L50)
