# Changelog

All notable changes to this project will be documented in this file.

# Unreleased

### Fixed

- Add `disable_mlock = true` to vault.hcl — required by Vault 1.20+
- Replace execlineb finish scripts with sh equivalents — s6-test is not available in the base image
- Set `ingress_entry: ui/` (no leading slash) — supervisor concatenates token path + entry, leading slash produces double-slash `//ui/` which returns 404
- Strip `Content-Security-Policy` and `X-Frame-Options` headers in nginx — Vault sets `frame-ancestors 'none'` which blocks HA ingress iframe

### Changed

- Upgrade Vault from 1.6.2 to 1.21.4
- Upgrade Terraform from 0.14.3 to 1.15.8

### Added

- nginx

## [0.3] - 2021-01-02

### Added

- s6 process supervision
- terraform for provisioning
- unsafe auto unseal
- gpg/keybase encryption of init keys for the users
- migrations between modes (hacky)
- gpg encryption of init keys for (less) unsafe auto unseal and migrations (very hacky)

## [0.2] - 2020-12-27

### Added

- setting for node_id
- badges

## [0.1] - 2020-12-27

### Added

- launch vault using available certs
- use tempio to template the config
- config for aws auto unseal
