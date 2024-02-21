# Build Container Action

This is a github action to build container images. This tool was designed for uBlue, but can be modified to accept any OIC.

## Inputs

| Parameter          | Description                             | Required | Default                                |
| ------------------ | --------------------------------------- | -------- | -------------------------------------- |
| image_name         | Name of the image to build              | true     | -                                      |
| image_variant      | Name of the image variant               | false    | ""                                     |
| version            | Primary tag to assign to the image      | true     | -                                      |
| support            | Latest, gts, or empty                   | false    | ""                                     |
| extra_build_args   | Extra args to be passed to buildah      | false    | ""                                     |
| signing_key        | Key to sign images                      | true     | -                                      |
| target             | Target to build in Containerfile        | false    | none                                   |
| container_registry | Registry to store resulting container   | false    | ghcr.io/${{ github.repository_owner }} |
| container_repo     | Repository for the container image      | false    | ${{ github.repository }}               |
| container_ref      | Repository ref for the container image  | false    | ${{ github.ref }}                      |
| push_container     | Whether to push the resulting container | false    | "true"                                 |

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
        uses: ./
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
