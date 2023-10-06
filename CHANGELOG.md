# bedrock-quasar-components ChangeLog

## 5.0.0 - 2023-10-xx

### Added
- Add quasar linting.

### Changed
- Update dependencies.
- **BREAKING**: Update `@bedrock/quasar` to v10.0.0.

## 4.1.0 - 2022-11-16

### Added
- Add base splash screen component.

## 4.0.0 - 2022-08-19

### Changed
- **BREAKING**: Use `exports` instead of `module`.
- Lint module.

## 3.2.0 - 2022-07-05

### Added
- Allow image value to be manually set by webdriver (or similar) during
  tests subsequent to a call to `getImage()`.

## 3.1.3 - 2022-06-08

### Fixed
- Extend timeout used to prevent false-positive image upload
  cancelation detection.

## 3.1.2 - 2022-06-08

### Fixed
- Fix image upload cancelation detection in older browsers that
  do not properly order `focusin` and `change` events.

## 3.1.1 - 2022-06-02

### Fixed
- Clean up event listeners in `BrQResizableImageInput`.

## 3.1.0 - 2022-06-02

### Added
- Add `BrQResizableImageInput` component.

## 3.0.0 - 2022-05-26

### Changed
- **BREAKING**: Require `@bedrock/quasar@8` (Quasar 2 and Vue 3).

## 2.0.2 - 2022-04-16

### Fixed
- Fix dependency typo.

## 2.0.1 - 2022-04-10

### Fixed
- Fix component exports.

## 2.0.0 - 2022-04-10

### Changed
- **BREAKING**: Rename package to `@bedrock/quasar-components`.
- **BREAKING**: Convert to module (ESM).

## 1.1.0 - 2021-04-05

### Changed
- Use v-slot shorthand.

## 1.0.0 - 2021-04-01

### Added
- Add core files.

- See git history for changes previous to this release.
