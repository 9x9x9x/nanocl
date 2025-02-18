# Changelog

All notable changes to this project will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.0.0/),
and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

## [0.3.0] - 2023-27-06

### Changed

- Dependencies upgrade and multiplatform images

## [0.2.0] - 2023-06-03

### Changed

- The `DnsRule` resource is created at runtime.

## [0.1.1] - 2023-05-14

### Fixed

- Backup dns using CloudFlare dns by default

## [0.1.0] - 2023-05-05

### Fixed

- Reload configuration after apply and remove rule

### Added

- Apply a rule
- Remove an existing rule
- Default config ignore resolv.conf and hosts
- Entry IpAddress can target a namespace with a syntax like: `{namespace name}.nsp`
