# Changelog

All notable changes to `swiftui-microinteractions` are documented here.

Format: `[version] — date — summary`

---

## [1.0.0] — 2026-05-21

Initial public release.

- Create mode: generates complete SwiftUI animation files from plain-English prompts
- Edit mode: reads existing file, applies targeted change, rewrites in place
- Asset scanning: detects `.imageset` folders in `Assets.xcassets` before generating
- Haptics check: uses shared `HapticFeedback.swift` if present, falls back to inline UIKit calls
- Xcode registration: auto-adds generated file to `.pbxproj` (PBXFileReference, PBXBuildFile, group child, Sources phase)
- Spring preset table: 7 presets mapped from feel words to exact `response`/`dampingFraction` values
- Progress output: streams `⚙️ v1.0.0` + 6 status lines so long generations feel responsive
- Supports 3 prompt levels: plain English · English + tech hints · fully technical
