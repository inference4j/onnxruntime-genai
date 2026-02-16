# Shadow Build: onnxruntime-genai Java Bindings

This fork publishes **community-maintained Java bindings** for [onnxruntime-genai](https://github.com/microsoft/onnxruntime-genai) to Maven Central.

## Why?

Microsoft's onnxruntime-genai includes Java bindings (`src/java/`, 16 API classes) but does not publish them to Maven Central ([Discussion #1406](https://github.com/microsoft/onnxruntime-genai/discussions/1406)). This fork fills that gap.

## Maven coordinates

```xml
<dependency>
    <groupId>io.github.inference4j</groupId>
    <artifactId>onnxruntime-genai</artifactId>
    <version>0.15.2</version>
</dependency>
```

```groovy
implementation 'io.github.inference4j:onnxruntime-genai:0.15.2'
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

## ONNX Runtime version

This release links against **ONNX Runtime 1.26.0**, the version upstream pins in
`cmake/ortlib.cmake` for v0.15.2. Three places must stay in sync:

- `cmake/ortlib.cmake` &mdash; upstream's native build pin
- `ORT_VERSION` in `.github/workflows/release.yml` &mdash; the ORT release we download and link against
- `onnxruntimeVersion` in `src/java/build.gradle` &mdash; the compile classpath for the Java bindings (the workflow passes `-DONNXRUNTIME_VERSION`)

### Classpath collision with the official ONNX Runtime JAR

The published fat JAR bundles ONNX Runtime's own native libraries under
`ai/onnxruntime/native/<platform>/`, which is **the same resource path used by
`com.microsoft.onnxruntime:onnxruntime`**. When both JARs are on the classpath,
whichever comes first wins &mdash; ONNX Runtime's loader takes the first match it
finds, and classpath order is not something you should rely on.

The POM does not declare a dependency on ONNX Runtime (the release build publishes
`allJar` rather than `components.java`, so no dependency metadata is attached), so
your build tool cannot resolve this for you.

**Keep the ONNX Runtime version on your classpath equal to the version above.**
A mismatch means you may silently load a different ONNX Runtime than the genai
natives were built against. For inference4j, this is why `inference4j-core` pins
the matching ONNX Runtime version whenever this artifact is refreshed.

## Versioning

Versions track upstream exactly: our `0.15.2` = upstream `v0.15.2`.

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
