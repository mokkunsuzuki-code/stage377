# Stage375: Production ML-DSA-65 Dual-Signature Verification & Downgrade Prevention Gate

Stage375 extends Stage374 by adding real ML-DSA-65 signing and verification for the same Stage373 attestation blob already signed and verified through Sigstore, Cosign, GitHub Actions OIDC, and Rekor.

## Purpose

Stage374 established:

- GitHub Actions OIDC identity
- Cosign signing
- Sigstore bundle verification
- Rekor inclusion verification
- concrete Rekor log position
- `external_transparency_bound: true`

Stage375 adds:

- a real ML-DSA-65 key pair
- ML-DSA-65 signing
- ML-DSA-65 signature verification
- public-key fingerprint verification
- exact target binding with the Stage374 Sigstore signature
- logical attestation hash consistency
- fail-closed PQC downgrade prevention

## Dual-Signature Model

The same Stage373 file is signed through two independent signature rails:

1. Stage374 Sigstore / Cosign / Rekor
2. Stage375 ML-DSA-65

Both signatures must target:

`docs/final-acceptance-attestation/stage373_final_acceptance_attestation.json`

Expected blob SHA256:

`6ecf58d0070d8db920744b7d32331e01e8e1aef2eded02dde428b80def79d5e6`

Expected logical attestation SHA256:

`d54b7524ced420f664da9d370985585d649ce80584b64bdf87342f89dbfde89f`

## Initial State

Before the GitHub Actions ML-DSA workflow runs:

- `decision: mldsa_execution_pending`
- `mldsa_signature_verified: false`
- `dual_signature_target_matches: false`
- `pqc_downgrade_prevented: false`

## Successful State

After complete ML-DSA signing and verification:

- `decision: quantum_safe_dual_signature_verified`
- `sigstore_signature_verified: true`
- `rekor_inclusion_verified: true`
- `mldsa_signature_verified: true`
- `dual_signature_target_matches: true`
- `pqc_downgrade_prevented: true`

## Downgrade Prevention

Stage375 uses:

- `pqc_required: true`
- `downgrade_policy: fail_closed`

After ML-DSA execution begins, a missing, invalid, removed, or mismatched ML-DSA signature causes `block`.

A valid Sigstore signature alone is not sufficient when the PQC policy is active.

## Key Protection

The ML-DSA private key:

- is created locally
- remains under `private/stage375-mldsa/`
- is excluded by `.gitignore`
- is excluded from Git and may remain in the local private directory; the production workflow receives a copy through an encrypted GitHub Actions secret
- is reconstructed temporarily inside the GitHub Actions runner
- has its temporary runner copy deleted before artifact upload
- is never published in GitHub Pages or the repository

The public key and ML-DSA signature may be public.

## OpenSSL

Stage375 uses OpenSSL 3.5.7 LTS in GitHub Actions.

OpenSSL 3.5 introduced native ML-DSA-44, ML-DSA-65, and ML-DSA-87 support.

This stage claims use of the FIPS 204 ML-DSA algorithm.

It does not claim that the custom-built OpenSSL binary is a FIPS 140 validated cryptographic module.

## Public Evidence

- `docs/mldsa-production/stage375_mldsa65_input.json`
- `docs/mldsa-production/stage375_mldsa65_public_key.pem`
- `docs/mldsa-production/stage375_mldsa65_signature.bin`
- `docs/mldsa-production/stage375_mldsa65_execution_receipt.json`
- `docs/mldsa-production/stage375_dual_signature_verification_result.json`
- `docs/mldsa-production/stage375_dual_signature_verification_summary.txt`

## Safety Boundary

Stage375 does not publish:

- ML-DSA private keys
- ML-DSA seeds
- OIDC tokens
- GitHub tokens
- raw QKD key material
- raw timestamp binaries
- free-form shell commands

## License

MIT License.

## Current Verified State

Stage375 completed real ML-DSA-65 signing and verification.

Current result:

- `decision: quantum_safe_dual_signature_verified`
- `sigstore_signature_verified: true`
- `rekor_inclusion_verified: true`
- `mldsa_signature_verified: true`
- `dual_signature_target_matches: true`
- `pqc_downgrade_prevented: true`
- GitHub Actions run: `29327350883`
- OpenSSL version: `3.5.7`
- ML-DSA signature size: `3309 bytes`

The Stage374 Sigstore/Rekor signature and the Stage375 ML-DSA-65 signature target the same Stage373 attestation blob.

The ML-DSA private key was not published. A local development copy may remain under the Git-ignored `private/stage375-mldsa/` directory, while the production workflow uses an encrypted GitHub Actions secret.
