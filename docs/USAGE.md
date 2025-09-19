# Usage details

## Choosing a base URL

- CDN (recommended for quick start):
  - `https://cdn.jsdelivr.net/gh/willwade/js-tts-wrapper-assets@main/sherpaonnx/tts/<sherpa_tag>`
- Self-hosting (recommended for production):
  - Copy the files to your own origin/CDN and use either `wasmPath` (exact glue JS URL) or `wasmBaseUrl` (directory) in the wrapper.

## js-tts-wrapper configuration patterns

1) Using `wasmPath` with upstream filenames (safest):
```js
const base = 'https://cdn.jsdelivr.net/gh/willwade/js-tts-wrapper-assets@main/sherpaonnx/tts/<sherpa_tag>';
const tts = new SherpaOnnxWasmTTSClient({ wasmPath: `${base}/sherpa-onnx.js` });
```

2) Using `wasmBaseUrl` (only if you control filenames):
```js
const tts = new SherpaOnnxWasmTTSClient({ wasmBaseUrl: '/assets/sherpaonnx' });
// The engine expects sherpa-onnx.js / sherpa-onnx-wasm-main.wasm / .data next to each other
```

3) Environment variable style (apps that inject at build time):
```js
const base = process.env.SHERPAONNX_WASM_BASEURL;
const tts = new SherpaOnnxWasmTTSClient({ wasmPath: `${base}/sherpa-onnx.js` });
```

## MIME types

- `.wasm` -> `application/wasm`
- `.js`   -> `text/javascript`
- `.data` -> `application/octet-stream`

## Common pitfalls

- 404 on `.wasm`: The loader relies on `Module.locateFile` to find the `.wasm` next to the glue JS; ensure the path is reachable and the host allows cross-origin requests if applicable.
- Wrong MIME types: Many static hosts mis-serve `.wasm` by default. Add a content-type rule.
- Mixed content: If your site is HTTPS, your asset base must also be HTTPS.
- Model index: `merged_models.json` is not hosted here. Host it with your app or point to a trusted URL and pass `mergedModelsUrl`.
