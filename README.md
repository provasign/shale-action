# shale-action

Composite GitHub Action that renders the [Shale](https://github.com/provasign/shale)
card — agent intent, session evidence, recorded checks — on pull requests.
Advisory only: it never blocks and never fails the build.

This repo is packaging glue (ADR D10): it downloads a checksum-pinned `shale`
release binary and runs `shale render`. The source of truth lives in
[`provasign/shale`](https://github.com/provasign/shale) under `action/`.

## Usage

`shale init` writes this workflow for you. By hand:

```yaml
name: shale
on:
  pull_request_target:          # write-capable token for fork PRs — safe because
    types: [opened, synchronize, reopened]   # we never check out PR code
permissions:
  contents: read
  pull-requests: write
  checks: write
jobs:
  card:
    runs-on: ubuntu-latest
    steps:
      - uses: provasign/shale-action@v1   # no actions/checkout — everything via API
```

## Security (ADR D12)

Designed for `pull_request_target` and **never checks out PR code**. Do not add
an `actions/checkout` step to workflows that use it — that reintroduces the
classic privilege-escalation hole. Everything the renderer needs (diff,
`.shale/` files) is fetched via the API.

## License

Apache-2.0
