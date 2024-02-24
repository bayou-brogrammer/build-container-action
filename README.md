# Build Container Action

<div align="center" width="50%">
  <img alt="orora" src="assets/thumbnail_gha.webp">
</div>

## What am i?

This is a github action to build and push container images to docker / github registry. This tool was designed for uBlue tooling, but can be modified to accept any OIC variant.

## Inputs

| Parameter          | Description                             | Required | Default                                |
| ------------------ | --------------------------------------- | -------- | -------------------------------------- |
| file               | Path to the Containerfile               | false    | ./Containerfile                        |
| target             | Target to build in Containerfile        | false    | none                                   |
| version            | Primary tag to assign to the image      | true     | -                                      |
| support            | Latest, gts, or empty                   | false    | ""                                     |
| signing_key        | Key to sign images                      | true     | -                                      |
| image_name         | Name of the image to build              | true     | -                                      |
| image_variant      | Name of the image variant               | false    | ""                                     |
| container_ref      | Repository ref for the container image  | false    | ${{ github.ref }}                      |
| container_repo     | Repository for the container image      | false    | ${{ github.repository }}               |
| container_registry | Registry to store resulting container   | false    | ghcr.io/${{ github.repository_owner }} |
| push_container     | Whether to push the resulting container | false    | "true"                                 |
| extra_build_args   | Extra args to be passed to buildah      | false    | ""                                     |

## Example

```yml
name: Test container workflow

on:
  push:
    branches:
      - main
  pull_request:

env:
  SUPPORT: latest
  MAJOR_VERSION: 39
  AKMODS_FLAVOR: main
  IMAGE_VARIANT: nokmods
  IMAGE_NAME: silverblue

jobs:
  run-main-build:
    name: Build uBlue-OS Main
    continue-on-error: false
    runs-on: ubuntu-latest
    permissions:
      contents: read
    strategy:
      fail-fast: false
      max-parallel: 5
    steps:
      - name: Checkout
        uses: actions/checkout@v4
        with:
          submodules: recursive

      - name: Build image
        uses: deploy-container-action@main
        with:
          container_ref: main
          file: ./Containerfile
          push_container: "false"
          container_repo: ublue-os/main
          signing_key: ${{ secrets.SIGNING_SECRET }}
          container_registry: ghcr.io/${{ github.repository }}
          support: ${{ env.SUPPORT }}
          target: ${{ env.IMAGE_NAME }}
          image_name: ${{ env.IMAGE_NAME }}
          version: ${{ env.MAJOR_VERSION }}
          image_variant: ${{ env.IMAGE_VARIANT }}
          extra_build_args: |
            AKMODS_FLAVOR=${{ env.AKMODS_FLAVOR }}
```

### Contributing

Please refer to the [contributing](CONTRIBUTING.md) file for information on how to contribute to this project.

### Copyright and Licensing

[Apache 2.0 license](http://www.apache.org/licenses/LICENSE-2.0).
