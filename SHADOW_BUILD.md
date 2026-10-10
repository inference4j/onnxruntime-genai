# Maven Central redistribution: onnxruntime-genai

This fork publishes **community-maintained Maven coordinates** for the official
[ONNX Runtime GenAI Java release JAR](https://github.com/microsoft/onnxruntime-genai/releases/tag/v0.17.0).
Microsoft now provides a combined desktop JAR in GitHub Releases. Starting with
0.17.0, we redistribute that JAR unchanged instead of compiling Java or native code.

## Maven coordinates

```xml
<dependency>
    <groupId>io.github.inference4j</groupId>
    <artifactId>onnxruntime-genai</artifactId>
    <version>0.17.0</version>
</dependency>
```

```groovy
implementation 'io.github.inference4j:onnxruntime-genai:0.17.0'
```

The Java package remains `ai.onnxruntime.genai`.

## Release workflow

Pushing a tag such as `v0.17.0` runs `.github/workflows/release.yml` on one Linux runner:

1. Download `onnxruntime-genai-0.17.0.jar` from the matching Microsoft release and
   verify its SHA-256 against the release API digest, version, and native libraries.
2. Download sources from the matching upstream tag, not this fork's source tree.
3. Use the standalone `publishing/` Gradle project to generate sources and Javadoc
   JARs and our POM. The main JAR is copied byte-for-byte, including its upstream
   manifest, embedded POM, and signed native libraries.
4. Sign all four artifacts, generate the Maven repository bundle, and upload it to
   Sonatype Central Portal with automatic publishing.
5. Wait for Sonatype to report `PUBLISHED`, then create this fork's GitHub Release.

The existing secrets remain unchanged: `GPG_SIGNING_KEY`,
`GPG_SIGNING_PASSPHRASE`, `MAVEN_CENTRAL_USERNAME`, and `MAVEN_CENTRAL_PASSWORD`.
Signing is required by default. The `src/java/` build remains available for local
source builds, but is no longer part of the release workflow.

Versions track upstream exactly. For each new release, verify the official desktop
JAR exists and update `ORT_VERSION` in the workflow to upstream's runtime dependency
from `.pipelines/packaging.yml` (`ort_runtime_version`, not `ort_build_version`).
There is no fallback to rebuilding missing release assets. If an upload times out,
inspect the reported deployment ID in Sonatype before retrying; it may still publish.

## Native platforms in the upstream 0.17.0 JAR

| Platform | Architecture |
|----------|--------------|
| Linux | x86_64, aarch64 |
| macOS | aarch64 |
| Windows | x86_64, aarch64 |

CPU only. Runtime support also requires matching ONNX Runtime natives for the platform.

## ONNX Runtime dependency

The official GenAI JAR contains GenAI and JNI native libraries, but **does not bundle
ONNX Runtime itself**. Our POM therefore declares a runtime dependency on
`com.microsoft.onnxruntime:onnxruntime:1.28.0`, matching upstream 0.17.0's runtime
dependency. Maven and Gradle fetch it transitively. Applications that manage or
override ONNX Runtime versions should align with this dependency.

Unlike our older fat JAR, this release does not duplicate ONNX Runtime's native
resources under `ai/onnxruntime/native/`. The embedded upstream POM is preserved;
dependency resolution uses the separately published POM under our coordinates.

## Local bundle verification

With Gradle 9.2.1, JDK 17, the downloaded release JAR, and its extracted sources:

```sh
gradle -p publishing clean centralPortalBundle \
  -Pversion=0.17.0 \
  -PupstreamJar=/absolute/path/onnxruntime-genai-0.17.0.jar \
  -PupstreamDir=/absolute/path/extracted-upstream-source \
  -PonnxruntimeVersion=1.28.0 \
  -Punsigned=true
```

This creates `publishing/build/central-publishing/central-bundle.zip` without uploading.
`-Punsigned=true` is only for local verification; Central requires signed artifacts.
For a signed bundle, omit it and set `ORG_GRADLE_PROJECT_signingKey` and
`ORG_GRADLE_PROJECT_signingPassword`.

## Deprecation and license

These community coordinates will be deprecated when Microsoft publishes the
official Java artifact to Maven Central. The license remains MIT, same as upstream.

- [Upstream](https://github.com/microsoft/onnxruntime-genai)
- [Published artifact](https://central.sonatype.com/artifact/io.github.inference4j/onnxruntime-genai)
- [Sonatype publishing API](https://central.sonatype.org/publish/publish-portal-api/)
