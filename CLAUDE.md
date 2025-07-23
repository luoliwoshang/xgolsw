# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

xgolsw is a lightweight XGo language server that runs in the browser using WebAssembly. It implements the Language Server Protocol (LSP) using JSON-RPC 2.0 for message exchange but operates directly in the browser's memory space through API interfaces rather than requiring network transport.

Key differences from xgols:
- Runs in browser via WebAssembly vs locally
- Supports single project vs multiple projects (workspace)  
- Pure XGo project support vs mixed Go/XGo programming

## Build Commands

### Standard Build
```bash
./build.sh
```

### Manual WebAssembly Build
```bash
GOOS=js GOARCH=wasm go build -trimpath -o spxls.wasm
```

### Generate Package Data (optional)
```bash
go generate ./internal/pkgdata
```

## Testing

Run all tests with coverage:
```bash
go test -v -race -coverprofile=coverage.txt ./...
```

Run tests for specific package:
```bash
go test -v ./internal/server
```

## Architecture

### Core Components

- **main.go**: WebAssembly entry point exposing JavaScript API
- **internal/server/**: LSP server implementation with full method support
- **internal/analysis/**: Static analysis and diagnostic passes
- **xgo/**: XGo project management, caching, and type information
- **jsonrpc2/**: JSON-RPC 2.0 message handling
- **protocol/**: LSP protocol definitions and structures

### Key Features

The server implements comprehensive LSP support including:
- Document synchronization and lifecycle management
- Code intelligence (hover, completion, signature help)
- Navigation (definition, references, implementation)
- Code quality (diagnostics, formatting)
- Semantic features (tokens, inlay hints)
- Refactoring (rename operations)
- Custom spx commands (resource renaming, input slot detection)

### Virtual File System

Uses `internal/vfs/mapfs.go` for in-memory file system operations, designed for browser environments where traditional filesystem access is limited.

### Testing Structure

Tests use mock repliers and schedulers to simulate browser environment. The `server_test.go` demonstrates comprehensive testing patterns for both LSP calls and notifications.

## Development Notes

- Go 1.24+ required
- Built specifically for WebAssembly target (js/wasm)
- Uses testify for testing framework
- Package data can be customized via `SetCustomPkgdataZip`
- Scheduler yields processor to JavaScript event loop for browser compatibility