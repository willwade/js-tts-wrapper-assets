# js-tts-wrapper-assets

Prebuilt, versioned assets for js-tts-wrapper â€” currently focused on hosting SherpaONNX Text-to-Speech (TTS) WebAssembly builds for easy consumption via CDN.

- Upstream project: https://github.com/k2-fsa/sherpa-onnx (Apache-2.0)
- Wrapper project: https://github.com/willwade/js-tts-wrapper

## What lives here

This repository stores published WebAssembly artifacts produced from upstream sherpa-onnx TTS builds, arranged by upstream tag under:

```
sherpaonnx/tts/<sherpa_tag>/
  sherpa-onnx.js                 # glue script
  sherpa-onnx-wasm-main.wasm     # WebAssembly binary
  sherpa-onnx-wasm-main.js       # Emscripten runtime JS
  sherpa-onnx-wasm-main.data     # Emscripten data (eSpeak, etc.)
  SHERPAONNX-LICENSE             # upstream license snapshot
```

We recommend you self-host for production, but these hosted assets give you a quick and stable default.

## Using from a CDN (jsDelivr)

The default CDN base for the `main` branch:

```
https://cdn.jsdelivr.net/gh/willwade/js-tts-wrapper-assets@main/sherpaonnx/tts/<sherpa_tag>
```

Example with js-tts-wrapper in the browser:

```html
<script type="module">
  import { SherpaOnnxWasmTTSClient } from 'js-tts-wrapper/browser';

  const base = 'https://cdn.jsdelivr.net/gh/willwade/js-tts-wrapper-assets@main/sherpaonnx/tts/<sherpa_tag>';

  const tts = new SherpaOnnxWasmTTSClient({
    // Prefer wasmPath when using upstream filenames
    wasmPath: `${base}/sherpa-onnx.js`,
    // Host merged_models.json yourself and point to it here if you use the model list
    mergedModelsUrl: '/assets/data/merged_models.json',
  });

  await tts.speak('Hello from SherpaONNX WASM');
</script>
```

Notes:
- Prefer `wasmPath` for the glue JS when consuming upstream build outputs; the runtime will fetch the `.wasm` and `.data` next to it.
- If you host your own filenames in a directory, you can instead pass `wasmBaseUrl` and the engine will look for the expected names inside that directory.
- Ensure `.wasm` is served with `application/wasm` and `.js` with `text/javascript`.

## How are these files published?

We use a manual GitHub Action in the main js-tts-wrapper repo that:
- Sets up Emscripten
- Clones `k2-fsa/sherpa-onnx` at a specified (or latest) tag
- Builds the TTS WebAssembly artifacts per upstream docs
- Copies the outputs into this repo under `sherpaonnx/tts/<sherpa_tag>/`
- Commits and pushes the changes

Action location (in js-tts-wrapper):
```
.github/workflows/build-publish-sherpaonnx-wasm.yml
```
Run it from the Actions tab (workflow_dispatch). It will print the CDN base URL on completion.

## Licensing

- SherpaONNX is licensed under Apache-2.0. Each published folder includes `SHERPAONNX-LICENSE` copied from the upstream tag used to build that version.
- This repository itself is documentation and static hosting for build outputs; see upstream for code licenses and notices.

## Contributing

- File an issue or PR in https://github.com/willwade/js-tts-wrapper if you need a new upstream tag built and published.
- For large binary artifacts, do not open PRs directly here; instead, use the publishing action from the main repo.
