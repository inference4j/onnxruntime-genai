# Shadow Build: onnxruntime-genai Java Bindings

This fork publishes **community-maintained Java bindings** for [onnxruntime-genai](https://github.com/microsoft/onnxruntime-genai) to Maven Central.

## Why?

Microsoft's onnxruntime-genai includes Java bindings (`src/java/`, 16 API classes) but does not publish them to Maven Central ([Discussion #1406](https://github.com/microsoft/onnxruntime-genai/discussions/1406)). This fork fills that gap.

## Maven coordinates

```xml
<dependency>
    <groupId>io.github.inference4j</groupId>
    <artifactId>onnxruntime-genai</artifactId>
    <version>0.12.0</version>
</dependency>
```

```groovy
implementation 'io.github.inference4j:onnxruntime-genai:0.12.0'
```

## What's changed from upstream?

Only build/publish infrastructure:

- `.github/workflows/release.yml` &mdash; cross-platform CI that builds native libs and publishes a fat JAR
- `src/java/build.gradle` &mdash; updated group, POM metadata, and Sonatype Central Portal publishing
- This file (`SHADOW_BUILD.md`)

**Zero changes to Java source code or native code.** The Java package remains `ai.onnxruntime.genai` for drop-in compatibility.

## Supported platforms

| Platform | Architecture |
|----------|-------------|
| Linux    | x86_64      |
| macOS    | aarch64     |
| Windows  | x86_64      |

CPU only. GPU variants may be added later.

## Versioning

Versions track upstream exactly: our `0.12.0` = upstream `v0.12.0`.

## Deprecation

This artifact will be deprecated when Microsoft publishes official Java bindings to Maven Central. At that point, switch to:

```groovy
implementation 'com.microsoft.onnxruntime:onnxruntime-genai:VERSION'
```

## License

MIT (same as upstream).

## Links

- Upstream: https://github.com/microsoft/onnxruntime-genai
- Published artifact: https://central.sonatype.com/artifact/io.github.inference4j/onnxruntime-genai
- Built by: [inference4j](https://github.com/inference4j)
