# DataCheck – A Cross-Platform Data Integrity Verifier
A fast, cross-platform CLI tool written in Go to verify file integrity between two locations (local or cloud).

It compares files, verifies hash matches, and optionally signs hash manifests for tamper detection.

## Key Features
- Compare two directories (local ↔ local, or local ↔ S3)

- Generate cryptographically signed manifests

- Detect missing, changed, or extra files

- Fast hashing with Go concurrency

- Supports S3 via AWS SDK (for ETag comparison or full hash)

- Dry-run mode for safe previewing

- Cross-platform (Windows, Linux, macOS)

- Single static binary

## Use Cases
- Verifying backups before deleting originals

- Auditing large-scale file transfers

- Checking cold storage against original data

- Ensuring AWS S3 sync jobs didn't corrupt files

- Shipping artifacts between environments with proof of consistency

## Planned Structure
```bash
datacheck/
├── cmd/                    # CLI entrypoint
├── internal/
│   ├── hasher/             # SHA256 / multi-hash functions
│   ├── walker/             # File traversal logic
│   ├── s3/                 # AWS integration (S3 stat/hash/etc.)
│   ├── signer/             # Manifest signing/verification (GPG or KMS)
│   └── report/             # Diff output in plain, JSON, etc.
├── pkg/                    # Shared utility functions
├── go.mod
├── main.go
└── README.md
🧪 Example CLI Usage
```

### Local to S3 verification
```bash
datacheck verify --source ./photos --dest s3://mybucket/photos --output report.json
```

### Sign a manifest file
```bash
datacheck manifest --source ./project --sign --key ~/.keys/private.pem
```

### Validate a signed manifest
```bash
datacheck verify --manifest project.manifest.json --signature project.manifest.sig --pubkey ~/.keys/public.pem
```

# Dry-run: show what would be flagged as inconsistent
```bash
datacheck verify --source ./local --dest ./backup --dry-run
```
## Manifest Format
JSON file containing:
```json
{
  "timestamp": "2025-06-13T17:00:00Z",
  "source": "./project",
  "files": {
    "main.go": "f7c3bc1d808e04732adf679965ccc34ca7ae3441",
    "data/output.csv": "7c222fb2927d828af22f592134e8932480637c0d"
  }
}
```
Signed with a .sig using your GPG key or AWS KMS.

## Tech Stack
Language: Go

Cloud: AWS SDK (S3 support)

Security: RSA/GPG/KMS (for signing)

Hashing: SHA256 (default), SHA512 (optional)

Packaging: Static binary for all major platforms

## Stretch Goals
- Plugin-like support for other cloud providers (Azure Blob, GCS)

- Web UI to upload manifest + signature for browser-side verification

- TUI (Terminal UI) with progress and summary view

- Metadata compare (timestamps, permissions, etc.)

- Parallel sync-fix with retries and overwrite detection

- Scheduled verification jobs

## Why This Project?
- This tool fills a real-world gap for sysadmins and developers alike:

- Verifies critical backups

- Adds cryptographic assurance

- Written in Go for speed and concurrency

- Cross-platform and portable

