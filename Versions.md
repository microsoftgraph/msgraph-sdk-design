# Versions

The Microsoft Graph SDKs MUST follow [Semantic Versioning 2.0.0](https://semver.org/). 

Changes made to the metadata and our generation tools that break a signature MUST result in a major version change.

Changes made to the underlying API behavior of the service API that are not intentional should be considered a bug and not result in a major version bump. Ideally we block the change from being propagated to the generated service libraries and the service API gets fixed. 

SDK library and packages should use the following format for specifying preview versions: `x.y.z-preview.n`.  An example of this is `1.3.0-preview.2`. The first preview release should be labeled like `1.3.0-preview`. Subsequent iterations of a preview can be suffixed with the preview iteration like this: `1.3.0-preview.1`. Composer has a [more restrictive format](https://getcomposer.org/doc/articles/versions.md#stabilities) so we should use `x.y.z-RCn` for release candidates and `x.y.z-beta` for service libraries based on the beta endpoint. The first release candidate (preview) release should look like `2.6.0-RC`. Subsequent iterations of a preview can be suffixed with the preview iteration like this: `2.6.0-RC1`.