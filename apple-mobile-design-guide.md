# Apple Mobile App Design Guide for AI

Reviewed: 2026-10-05. Scope: iOS and iPadOS mobile apps.

This is an independently written, condensed design brief based on Apple's Human Interface Guidelines (HIG), not an official Apple document or a complete reproduction. Give this file to an AI design or coding assistant as project context.

## 1. Instructions for the AI

- Establish the minimum supported OS version and implementation framework before choosing components.
- Prefer native system components, semantic colors, system typography, and adaptive layouts.
- Preserve recognizable iOS interaction patterns while expressing the app's identity through content and a deliberate accent color.
- Treat **HIG guidance** below as source-based recommendations. Treat **project defaults** as adjustable example decisions, never as Apple specifications.
- Use logical points (`pt`) for native dimensions. Physical pixels depend on display scale. CSS pixels in a browser prototype approximate logical dimensions but do not reproduce native sizing or behavior automatically.
- If generating Android screens, adapt to that platform instead of copying this guide indiscriminately.
- Document exceptions and check them against the relevant official source. Do not invent an Apple-required hex color, corner radius, spacing grid, or animation duration.

Official starting point: [Apple Human Interface Guidelines](https://developer.apple.com/design/human-interface-guidelines).

## 2. Color and appearance

**HIG guidance:** Use semantic system colors whenever possible. They adapt to appearance and accessibility settings. Keep color meanings consistent, and provide suitable light, dark, and increased-contrast variants for custom colors. Avoid making decorative text look interactive through the same tint used for actions.

| Purpose | UIKit semantic example | SwiftUI approach |
| --- | --- | --- |
| Main content background | `systemBackground` | System background via platform color |
| Grouped screen background | `systemGroupedBackground` | Platform grouped background color |
| Grouped content surface | `secondarySystemGroupedBackground` | Platform grouped surface color |
| Primary text | `label` | `.primary` |
| Supporting text | `secondaryLabel` | `.secondary` |
| Dividers | `separator` | `Divider()` |
| Main action or selection | App tint | `.tint(...)` |
| Destructive action | System destructive appearance | Semantic destructive role |

**Project defaults:** Choose one accent family. Blue is a reasonable starting choice when there is no brand palette; it is not mandatory. Do not hardcode a single blue or white-on-blue combination as universally accessible. Verify the actual foreground/background pair. Use calm neutral surfaces and reserve saturated color for meaningful emphasis.

Source: [Color](https://developer.apple.com/design/human-interface-guidelines/color).

## 3. Buttons and interaction states

**HIG guidance:** Give buttons a touch region of at least 44 × 44 pt as a general rule; a small visible icon can sit inside that larger region. Make the most likely action prominent, typically using the app accent. Keep prominent actions limited to one or two per view. Distinguish peer options through style rather than inconsistent size. Use concise action labels. Custom buttons need visible press feedback. Destructive actions use the destructive role and system red treatment; avoid making them the default primary action. Show progress when an action takes time.

**Project defaults:** Use one main CTA per task screen, labeled with a verb such as “Save” or “Continue.” Use a quieter native style for secondary actions. For custom async actions, prevent duplicate submission while busy and expose loading, completion, and failure states. Preserve a stable layout during progress.

Source: [Buttons](https://developer.apple.com/design/human-interface-guidelines/buttons).

## 4. Rounded corners and shape patterns

**Project defaults — not universal Apple radius specifications:**

| Element | Suggested treatment |
| --- | --- |
| Native button, sheet, tab bar, menu | Let the system determine shape |
| Custom prominent text button | Capsule when appropriate; otherwise a consistent rounded rectangle |
| Icon-only custom action | Circle if it fits the visual grouping; maintain a generous hit region |
| Custom content card | Continuous rounded rectangle, starting around 16 pt |
| Nested card surface | Keep corners visually concentric with the outer surface |
| App icon | Follow app-icon production resources and system masking |

For an ordinary nested rounded rectangle, `innerRadius ≈ max(0, outerRadius − inset)` is a useful geometric starting point, not an Apple rule. Optical adjustment may be needed for continuous curves. Avoid unrelated radii for identical components.

Do not copy visionOS gaze-target spacing or watchOS button-shape requirements into iOS. Apple’s button page includes several platforms; read the relevant platform section.

Useful references: [Buttons](https://developer.apple.com/design/human-interface-guidelines/buttons), [Apple Design Resources](https://developer.apple.com/design/resources/).

## 5. Materials and Liquid Glass

**HIG guidance:** Liquid Glass creates a functional layer for navigation and controls above content. Avoid applying it to ordinary content surfaces. Use custom glass effects sparingly. Prefer the regular variant when text or backgrounds could reduce legibility. The clear variant suits controls above visually rich media. Standard materials can establish structure within the content layer. Material appearance responds to system settings, so judge its semantic purpose rather than one screenshot's color.

**Project defaults:** On systems supporting Liquid Glass, start with native navigation and controls so the OS supplies appearance and behavior. For older targets, use the available native presentation. Do not imitate glass by blurring every card or placing translucent surfaces over paragraphs. Provide readable results when transparency is reduced. Gate version-specific APIs according to the deployment target.

Source: [Materials](https://developer.apple.com/design/human-interface-guidelines/materials).

## 6. Typography

**HIG guidance:** Use system text styles and support Dynamic Type. Prefer typography that remains legible when people enlarge it. For custom iOS/iPadOS typography, Apple's recommended default size is 17 pt and minimum is 11 pt; that minimum is not a good default for ordinary reading text.

**Project defaults:** Map screen titles, section headings, body text, supporting labels, and captions to the corresponding native styles. Use weight and hierarchy before adding more colors. Allow multiline labels and flexible component heights. Avoid forcing all content into fixed-height rows.

| Content role | SwiftUI style example |
| --- | --- |
| Main screen title | `.largeTitle` or `.title` |
| Section heading | `.headline` |
| Main reading text | `.body` |
| Supporting detail | `.subheadline` |
| Minor annotation | `.caption` |

Let the platform resolve actual sizes, line metrics, and scaling rather than building the interface around fixed font pixels.

Source: [Typography](https://developer.apple.com/design/human-interface-guidelines/typography).

## 7. Layout, spacing, and safe areas

**HIG guidance:** Organize by importance, align related elements, group related information, and reveal complexity progressively. Adapt layouts across display sizes and orientations. Account for device features and safe areas. Prefer leading/trailing alignment so interfaces can adapt to right-to-left languages.

**Project defaults:** Use these only for custom content, after accounting for native container margins:

| Token | Starting value | Example use |
| --- | --- | --- |
| `space.xs` | 4 pt | Tight internal relationship |
| `space.sm` | 8 pt | Related icon and text |
| `space.md` | 12 pt | Compact group spacing |
| `space.lg` | 16 pt | Custom card padding |
| `space.xl` | 24 pt | Section separation |
| `space.xxl` | 32 pt | Major content separation |

Start custom screen margins at 16–20 pt and adjust to native conventions and available width. This is not a mandatory Apple grid. Avoid applying extra margins on top of native list padding unintentionally. Let content scroll; ensure the keyboard and fixed controls do not cover inputs or final rows.

Source: [Layout](https://developer.apple.com/design/human-interface-guidelines/layout).

## 8. Navigation and tabs

**HIG guidance:** Tabs switch between major app sections; they do not execute actions. Preserve each section's navigation state. Keep tab navigation available during normal section navigation, except when a temporary modal covers it. Favor a manageable number of sections and avoid overflow that hides destinations. Keep unavailable sections discoverable with explanatory content rather than disabling their tab.

**Project defaults:** Start with three to five tabs only when the information architecture warrants them; this is not an Apple requirement. Use a native navigation stack for drill-down screens and its standard back behavior. Put “Create,” “Add,” or “Upload” in an action control instead of pretending it is a destination tab. Adapt complex iPad navigation to a sidebar when appropriate.

Source: [Tab bars](https://developer.apple.com/design/human-interface-guidelines/tab-bars).

## 9. Sheets and focused tasks

**HIG guidance:** Use a sheet for a focused task or information request related to the parent view. iOS and iPadOS sheets can be modal or nonmodal. Cancel or Close dismisses; Done completes or saves; Back moves through a hierarchy rather than dismissing the sheet. Consider another presentation for prolonged or complex flows.

**Project defaults:** Use native sheet sizing and presentation. Make completion and dismissal understandable, including when gestures are unavailable. Handle unsaved work deliberately. Choose full-screen presentation when the task needs sustained attention or substantial space.

Source: [Sheets](https://developer.apple.com/design/human-interface-guidelines/sheets).

## 10. Forms and text input

**HIG guidance:** Use text fields for short input and text views for longer content. Use secure fields for sensitive values. A persistent label can clarify a field after its placeholder disappears. Keep spacing, widths, and focus order coherent, and validate at a useful time.

**Project defaults:** Match keyboard type and content hints to expected input. Show a specific, actionable error near its field. Preserve valid input after errors. Allow password managers and appropriate autofill. Make the active field visible above the keyboard, with a clear route to submit or dismiss it.

Source: [Text fields](https://developer.apple.com/design/human-interface-guidelines/text-fields).

## 11. Icons and motion

**HIG guidance:** SF Symbols offers interface icons that coordinate with system typography. Check availability against the target OS. Choose rendering modes deliberately. Do not use SF Symbols as app icons, logos, or trademarks. Motion should explain feedback or transitions, remain brief, and offer alternatives; system components can adapt to accessibility preferences.

**Project defaults:** Use familiar symbols consistently and label ambiguous actions. Prefer native transitions. For custom animations, respect Reduce Motion and avoid decorative repeated bouncing or parallax. Keep core information understandable without animation.

Sources: [SF Symbols](https://developer.apple.com/design/human-interface-guidelines/sf-symbols), [Motion](https://developer.apple.com/design/human-interface-guidelines/motion).

## 12. Accessibility checks

**HIG guidance:** Support larger text, VoiceOver descriptions, sufficient contrast, and alternatives to complex gestures. Communicate status through more than color. Check both light and dark appearances and accessibility color settings.

**Project acceptance criteria:**

- Use 44 × 44 pt or larger hit regions for ordinary mobile actions.
- Validate ordinary reading text at a conservative 4.5:1 contrast target; use the source's size/weight guidance when assessing exceptions.
- Test Dynamic Type, including accessibility sizes; labels and actions remain usable.
- Icon-only controls expose an accessible name and appropriate state.
- Errors include readable text or another meaningful indicator.
- Important tasks work without a swipe-only or drag-only interaction.
- Reduce Motion, Reduce Transparency, and Increase Contrast produce usable results.
- Reading and focus order follows the content's logical order.

Source: [Accessibility](https://developer.apple.com/design/human-interface-guidelines/accessibility).

## 13. Example project tokens

These are starter decisions for custom surfaces, not Apple-issued tokens. Native component geometry should take priority.

```yaml
platform: ios-ipados
units: logical-points
appearance: adaptive-light-dark
colors:
  accent: project-accent-with-accessible-variants
  background: systemBackground
  groupedBackground: systemGroupedBackground
  groupedSurface: secondarySystemGroupedBackground
  textPrimary: label
  textSecondary: secondaryLabel
  divider: separator
  destructive: system-semantic-destructive
spacing: [4, 8, 12, 16, 24, 32]
customScreenMargin: 16
customCardPadding: 16
customCardRadius: 16
customCardCurve: continuous
customPrimaryButtonShape: capsule
ordinaryActionHitRegion: [44, 44]
typography: system-text-styles-with-dynamic-type
navigation: native-components
materials: semantic-native-materials
```

## 14. Copyable AI prompt

> Use this guide to design and implement an iOS/iPadOS app. First establish the target OS and framework. Apply the source-based guidance and use labeled project defaults only where custom design decisions are needed. Resolve colors, typography, control geometry, navigation, and materials through native components where possible. Produce a coherent screen hierarchy, complete interaction states, adaptive layouts, and accessible behavior. Explain any deliberate departure and distinguish your proposed numeric tokens from Apple's recommendations.

## 15. Existing Markdown resource found online

A community-maintained [Markdown mirror of Apple's HIG](https://github.com/realmaitreal/Human-Interface-Guidelines) is available if you need a larger reference corpus. It is unofficial; verify freshness and platform-specific statements against Apple's original pages before relying on it. This guide uses Apple's official documentation as its factual basis.
