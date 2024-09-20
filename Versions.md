# Versions

The Microsoft Graph SDKs MUST follow [Semantic Versioning 2.0.0](https://semver.org/). This is important for capturing information about the use of each SDK via the SdkVersion header emitted by our libraries.

Changes made to the metadata and our generation tools that break a signature MUST result in a major version change.

Changes made to the underlying API behavior of the service API that are not intentional should be considered a service bug and not result in a major version bump. Ideally we block the change from being propagated to the generated service libraries and the service API gets fixed.

## Version format per language

Unless otherwise specified, the SDK library and packages should use the following format for specifying preview versions: `x.y.z-preview.n`.  An example of this is `1.3.0-preview.2`. The first preview release should be labeled like `1.3.0-preview`. Subsequent iterations of a preview can be suffixed with the preview iteration like this: `1.3.0-preview.1`.

Service libraries for the beta endpoints will not increment the pre-release version when new releases are made. We will typically increment the minor version across new beta service libraries `4.3.0-preview` --> `4.4.0-preview`.

Prelease identifiers like `preview`, `beta`, and `rc` should be lower case.

### PHP version format

Composer has a [more restrictive format](https://getcomposer.org/doc/articles/versions.md#stabilities) so we should use `x.y.z-rcn` for release candidates and `x.y.z-beta` for service libraries based on the beta endpoint. The first release candidate (preview) release should look like `2.6.0-rc`. Subsequent iterations of a preview can be suffixed with the preview iteration like this: `2.6.0-rc1`.

### Golang version format

[Golang standards](https://golang.org/doc/modules/version-numbers) specify that the version should use `vx.y.z` for release versions and `vx.y.z-beta.n` for pre-release versions. For example, a production release version may look like `v1.3.4`, and release candidate may look like `v1.3.4-beta`, with updates to the release candidate looking like `v1.3.4-beta.1`. 