# Build ZIP File Action

[![Tests](https://github.com/Digital-Gravy/action-build-zip-file/actions/workflows/test.yml/badge.svg)](https://github.com/Digital-Gravy/action-build-zip-file/actions/workflows/test.yml)

A GitHub Action that executes a build command and locates the generated ZIP file, providing its path and filename as outputs. This action is particularly useful for build processes that generate ZIP archives, such as WordPress plugin/theme builds or other packaged distributions.

## Features

- Executes a configurable build command
- Automatically extracts and tests the generated ZIP file
- Uploads the extracted contents as an artifact
- Supports custom build directories
- Verifies artifact contents

## Usage

Add the following step to your workflow:

```yaml
- name: Build and Upload ZIP
  uses: Digital-Gravy/action-build-zip-file@v1
  with:
    dist_dir: "dist"
    plugin_slug: "my-plugin"
    build_command: "npm run build:zip"
```

### Inputs

| Input                  | Description                                    | Required | Default |
| ---------------------- | ---------------------------------------------- | -------- | ------- |
| `dist_dir`             | Directory where the ZIP file will be generated | Yes      | -       |
| `plugin_slug`          | Slug/name for the plugin or package            | Yes      | -       |
| `build_command`        | Command to execute to build the ZIP file       | Yes      | -       |
| `include_hidden_files` | Whether to include hidden files in the ZIP file | No      | `false` |


### Example Workflow

Here's a complete example of how to use this action in a workflow:

```yaml
name: Build and Package

on:
  push:
    branches: [main]
  pull_request:
    branches: [main]

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4

      - name: Setup Node.js
        uses: actions/setup-node@v4
        with:
          node-version: "20"
          cache: "npm"

      - name: Install dependencies
        run: npm ci

      - name: Build and Upload ZIP
        uses: Digital-Gravy/action-build-zip-file@v1
        with:
          dist_dir: "dist"
          plugin_slug: "my-plugin"
          build_command: |
            npm run build
            cd dist && zip -r my-plugin.zip .

      # Optional: Download the artifact in subsequent steps
      - name: Download artifact
        uses: actions/download-artifact@v4
        with:
          name: my-plugin.zip
```

## How It Works

1. Executes the provided build command to generate the ZIP file
2. Extracts the ZIP file contents to verify integrity
3. Uploads the extracted contents as an artifact
4. Fails if any step encounters an error

## Error Handling

The action will fail with an error message if:

- The build command fails to execute
- The ZIP file extraction fails
- No files are found to upload as an artifact

## License

GPLv3 - see LICENSE file for details
