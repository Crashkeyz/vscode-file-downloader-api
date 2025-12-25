# VS Code File Downloader API - AI Coding Agent Guide

## Project Overview

This is a **wrapper package** that provides a typed API interface for the VS Code File Downloader extension. It does NOT implement downloading logic—it exports an interface and helper to access the actual extension's API via VS Code's extension system.

**Critical:** This package is a thin client layer. The actual file downloader lives at https://github.com/microsoft/vscode-file-downloader

## Architecture

Three-file structure serving distinct purposes:

- [src/FileDownloader.ts](../src/FileDownloader.ts) - TypeScript interface definition only (`FileDownloader` interface and `FileDownloadSettings` interface)
- [src/ExtensionHelper.ts](../src/ExtensionHelper.ts) - Extension activation helper (gets extension by ID `mindaro-dev.file-downloader`)
- [src/index.ts](../src/index.ts) - Public API surface (exports `getApi()` function and `FileDownloader` type)

**Pattern:** Users call `getApi()` which uses `ExtensionHelper` to retrieve the actual extension's API. The `FileDownloader` interface in this package must match the API exported by the extension.

## Development Workflows

### Build
```bash
npm run compile    # TypeScript compilation to out/ directory
```

### Lint
```bash
npm run lint       # ESLint with TypeScript parser on src/**/*.ts
```

### Publish
```bash
npm run prepublish # Automatically compiles before publishing
```

No test suite exists in this package—testing happens in the actual extension repository.

## Key Conventions

1. **Interface-only pattern:** Never implement download logic here. Only define TypeScript interfaces that mirror the actual extension's API.

2. **Extension dependency:** Consumers must declare in their `package.json`:
   ```json
   "extensionDependencies": ["mindaro-dev.file-downloader"]
   ```

3. **API retrieval pattern:** Always use async `getApi()`:
   ```typescript
   const fileDownloader: FileDownloader = await getApi();
   ```

4. **Headers copyright:** All source files use Microsoft copyright header (see existing files for template)

5. **Strict TypeScript:** Project uses `"strict": true` - all types must be explicit, no implicit any

## Making Changes

- **Adding API methods:** Update `FileDownloader` interface in [src/FileDownloader.ts](../src/FileDownloader.ts) to match new extension APIs
- **Settings changes:** Modify `FileDownloadSettings` interface for new download options
- **Export changes:** Only touch [src/index.ts](../src/index.ts) to change public API surface
- **Extension ID changes:** Modify hardcoded ID in [src/ExtensionHelper.ts](../src/ExtensionHelper.ts) only if extension is republished

## Output Structure

Compiled JavaScript goes to `out/` directory (matching `package.json` main: `"./out/index.js"`). Declaration files (`.d.ts`) are generated automatically due to `"declaration": true` in [tsconfig.json](../tsconfig.json).
