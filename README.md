# s3

An S3-compatible object-storage client for [Brood](https://broodlang.org) —
AWS Signature V4 over `http` (rustls HTTPS). Works with **Tigris, AWS S3,
MinIO, and Cloudflare R2** — anything speaking the S3 API. Pure Brood, no
external dependencies.

Path-style addressing (`<endpoint>/<bucket>/<key>`), which every S3 service
accepts and is the simplest to sign. Object bodies are byte-faithful `bytes`
values. Every operation returns `[:ok …]` or `[:error status message]`.

## Usage

```brood
(:alias s3)

(let (client (s3/client {:endpoint "https://fly.storage.tigris.dev"
                         :region "auto"
                         :access-key (env "AWS_ACCESS_KEY_ID")
                         :secret-key (env "AWS_SECRET_ACCESS_KEY")
                         :bucket "my-bucket"}))
  (s3/put-object client "hello.txt" (str->bytes "hi") "text/plain")
  (s3/get-object client "hello.txt")      ; => [:ok #bytes …]
  (s3/head-object client "hello.txt")
  (s3/delete-object client "hello.txt"))
```

Add it to a project with:

```bash
nest add s3 :version 0.1.0
```

## Publishing

Releases go to [hive](https://github.com/broodlang/hive), the Brood package
registry at <https://brood.fly.dev>.

**One-time setup** — register and mint an API token:

1. Create an account at <https://brood.fly.dev/register>.
2. Mint an API token on your <https://brood.fly.dev/settings> page (it's shown
   once), then expose it to `nest`:

   ```bash
   export HIVE_TOKEN=<your token>
   # or, persistently, add to ~/.config/brood/config.blsp:  :registry-token "<your token>"
   ```

**Each release:**

1. Bump `:version` in `project.blsp` — releases are **immutable**, so a version
   can never be republished.
2. Confirm the tests pass:

   ```bash
   nest test
   ```

3. Publish:

   ```bash
   nest publish
   ```

`nest publish` builds a source tarball (excluding `_deps/`, `tests/`, `.git/`,
and the lock file), records its sha256, and POSTs it to the registry. Only
`:version` (registry) dependencies are recorded in the published metadata. Docs
build automatically and appear at `https://brood.fly.dev/packages/s3`.

## License

AGPL-3.0-only. See [LICENSE](LICENSE).
