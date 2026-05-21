---
name: swiftui-microinteractions
description: Generate premium SwiftUI animations in the legendary-Animo style — spring physics, CoreHaptics, glass morphism, complete compilable files.
---

Generate a complete, compilable SwiftUI animation file in the legendary-Animo style. No placeholders. No TODO comments.

## When to Use
- Building a SwiftUI button, gesture, slider, or liquid effect with premium physics
- Need haptic feedback timed correctly to animation phases
- Creating a drag interaction with resistance, snap, or threshold trigger
- Editing an existing SwiftUI animation file

## Mode Detection
- **Edit mode**: prompt contains "edit", "update", "add X to", "change", or a `.swift` filename → read file first, change only what's asked, overwrite file on disk
- **Create mode**: everything else → generate a new complete file and write it to disk

---

## Asset Scanning (run before generating any image-dependent animation)

If the animation involves images, photos, or cards, scan for existing assets first:

1. Check these paths in order:
   - `legendary-Animo/Res/Assets.xcassets/Images/` — project asset catalog
   - `Assets.xcassets/Images/` — standard Xcode layout
   - `Assets.xcassets/` — flat layout

2. List `.imageset` folder names found — strip the `.imageset` suffix to get the asset name used in `Image("name")`

3. **If assets found** → use them directly. Print:
   ```
   🖼️  Assets found: photo1, photo2, photo3 … (using in carousel)
   ```

4. **If no assets found** → use `Image(systemName: "photo.fill")` inside a colored `RoundedRectangle` as placeholder. Output Asset Notes at the end listing exactly what to add.

Never invent asset names. Only reference names confirmed to exist on disk.

---

## Style Rules

**Backgrounds:** `Color(white: 0.06).ignoresSafeArea()` standalone · `Color("BgColor").ignoresSafeArea()` in-project

**Glass surfaces:** `.ultraThinMaterial` or `.thickMaterial` clipped to `Capsule()` · track background `.white.opacity(0.06)` + 1pt stroke `.white.opacity(0.08)`

**Opacity levels:** ghost `0.06–0.08` · subtle `0.12` · inactive `0.3` · secondary `0.5` · active `0.8–0.9` · full `1.0`

**Colors:** white + opacity dominant · cyan `Color(red: 0.45, green: 0.65, blue: 1.0)` · green `Color(red: 0.55, green: 0.95, blue: 0.75)` · never `.blue/.green/.red` on dark bg

**Gradients:** two-tone only · blob fill `[.white, Color(white: 0.88)]` · progress `[cyan, green]`

---

## Spring Presets

| Feel | Value |
|---|---|
| Snap / bounce | `.spring(response: 0.35, dampingFraction: 0.5)` |
| UI pop | `.spring(response: 0.35, dampingFraction: 0.6)` |
| Standard settle | `.spring(response: 0.4, dampingFraction: 0.65)` |
| Physics settle | `.spring(response: 0.45, dampingFraction: 0.7)` |
| Slow morph | `.spring(response: 0.6, dampingFraction: 0.8)` |
| Precision stiff | `.interpolatingSpring(stiffness: 220, damping: 22)` |
| Dial / scrub | `.interactiveSpring(response: 0.3, dampingFraction: 0.7)` |

Never use bare `.spring()` — always explicit `response` + `dampingFraction`. If user supplies values, use them verbatim. Map feel words: "stretchy" → 0.4/0.5, "snappy" → 0.35/0.6, "melts" → 0.6/0.8.

---

## Haptics (always include)

```
drag start → .lightImpact()
cross threshold / zone → .selectionChanged()
commit / snap / release → .mediumImpact()
tear / destroy → .heavyImpact()
```

Include this at the bottom of standalone files:
```swift
import UIKit
private struct HapticFeedback {
    static func lightImpact() { UIImpactFeedbackGenerator(style: .light).impactOccurred() }
    static func mediumImpact() { UIImpactFeedbackGenerator(style: .medium).impactOccurred() }
    static func heavyImpact() { UIImpactFeedbackGenerator(style: .heavy).impactOccurred() }
    static func selectionChanged() { UISelectionFeedbackGenerator().selectionChanged() }
}
```

---

## State & Code Rules

- Default: pure `@State` for all gesture tracking · `@GestureState` only if value must auto-reset
- Constants: camelCase `private let` at struct level (2–5 values) — never SCREAMING_SNAKE_CASE
- Liquid metaball: `Canvas` (black fill → white circles) + `.blur(20)` + `.contrast(50)` + `.blendMode(.screen)`
- Multi-phase sequences: stacked `DispatchQueue.main.asyncAfter` with overlapping delays

**File layout (mandatory MARK order):**
```
// MARK: - Model
// MARK: - Main View   (tokens → @State → body → subviews → gesture → actions)
// MARK: - Supporting Shapes
// MARK: - Haptic Helper
// MARK: - Preview
```

---

## Output (Create mode)

Stream these progress lines one by one — user sees them immediately:

```
🖼️  Assets: <found: name1, name2… · or · none found, using placeholders>
🎯  Archetype: <archetype name>
⚡  Physics: <spring preset and why — one phrase>
🎮  Haptics: <2–3 haptic moments>
🏗️  State: <tier> — <@State var names>
✍️  Writing <FileName>.swift…
```

After the last progress line, **write the file to disk** using the Write tool:
- If carousel animation and `legendary-Animo/Carousels/` exists → write there
- Else if `legendary-Animo/Animations/` exists → write there
- Else if `Animations/` folder exists → write there
- Else write `<FileName>.swift` in the current directory

Then print:
```
✅  Saved to <full relative path>
```

Then output the **ContentView registration** snippet (```swift block) for the user to paste manually:
```swift
DemoItem(
    row: RowView(icon: "💧", title: "Title Here", desc: "Feature · feature · feature"),
    destination: wrappedDestination { YourView() },
    date: "May 21, 2026",
    hasDateHeader: true
)
```

Then **Asset notes** — always include if placeholders were used:
```
ASSET NOTES:
- Add portrait images to Assets.xcassets/Images/ named: photo1, photo2, photo3…
  → Recommended size: 400×600pt, portrait ratio
- Remove placeholder RoundedRectangle once real assets are added
```

---

## Output (Edit mode)

Stream before editing:
```
📂  Reading <FileName>.swift…
✏️  Applying: <one-line summary of change>
✍️  Writing updated file…
```

**Overwrite the file at its existing path on disk** using the Write tool, then print:
```
✅  Updated <full relative path>
```

Then output a **Changes** bullet list of what was modified and why.
