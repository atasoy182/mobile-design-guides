# Android Native Mobile App Design Guide for AI

Reviewed: 2026-10-05. Scope: Android phone, tablet, foldable, and resizable app windows. Wear OS, TV, Auto, and XR need their own platform guidance.

This independently written design brief summarizes official Android and Material guidance for AI design and coding assistants. It is not an official Google document. It complements the iOS guide in this repository but makes Android-specific choices.

**Reading key:** **Official guidance** summarizes a linked source. **Project defaults** and **project patterns** are proposed, adjustable decisions. They are not universal Android requirements.

## 1. Instructions for the AI

- Establish the minimum SDK, target SDK, UI framework, and pinned UI-library versions before selecting APIs.
- For a new native app, start with Jetpack Compose and Material 3. For an existing Views app, use its Material Components theme and preserve its established architecture.
- Use semantic theme roles, native interaction feedback, scalable typography, and Android navigation behavior.
- Use `dp` for layout and touch targets and `sp` for text. Do not mechanically convert iOS point values or CSS pixels into an Android specification.
- Separate verified Material component defaults from your proposed overrides. Resolve component shapes and metrics through the installed library.
- Design content, loading, empty, success, partial-failure, and blocking-error states together.
- Specify compact and larger-window behavior, font scaling, keyboard handling, and accessibility semantics.

## 2. Material 3 and Material 3 Expressive

**Official guidance:** Compose Material 3 supports theming through color, typography, and shapes. Material 3 Expressive expands the design system with updates including components and motion; some APIs require experimental opt-in.

**Project defaults:** Use Material 3 as the baseline. Adopt Expressive components when the pinned library supports them and they improve the task. Record any experimental dependency. An OS upgrade does not automatically update app component libraries. Do not assume Expressive features all require Android 16 or that every project already includes them.

Source: [Material Design 3 in Compose](https://developer.android.com/develop/ui/compose/designsystems/material3).

## 3. Color roles and button colors

**Official guidance:** Material color schemes provide semantic roles and paired foreground colors. Select the foreground for the actual background role rather than assuming white works on every accent.

| Purpose | Container role | Content role |
| --- | --- | --- |
| Screen | `surface` | `onSurface` |
| Grouped surface | `surfaceContainer` family | `onSurface` |
| Supporting text on surfaces | Surface role | `onSurfaceVariant` |
| Prominent filled action | `primary` | `onPrimary` |
| Primary tonal container | `primaryContainer` | `onPrimaryContainer` |
| Filled tonal action | `secondaryContainer` | `onSecondaryContainer` |
| Inline error text | Surface role | `error` |
| Error message container | `errorContainer` | `onErrorContainer` |
| Borders and subtle dividers | `outline` or `outlineVariant` | Not applicable |

**Project defaults:** Let Material buttons apply their defaults, including disabled states. Reserve accent emphasis for meaningful actions and selection. Destructive actions need explicit wording; there is no direct Compose equivalent of SwiftUI's destructive button role. If overriding destructive colors, use accessible semantic error pairs.

Sources: [ColorScheme API](https://developer.android.com/reference/kotlin/androidx/compose/material3/ColorScheme), [Android color guidance](https://developer.android.com/design/ui/mobile/guides/styles/color).

## 4. Dynamic color, dark theme, and semantic extensions

**Official guidance:** Dynamic color is available from Android 12 (API 31). Support light and dark schemes and a custom fallback when dynamic color is unavailable. Respect user theme preferences and check contrast.

**Project defaults:** Enable dynamic color where it fits the product, with an explicit branded fallback. Test multiple wallpaper-derived palettes. Define success, warning, and information roles as app theme extensions when needed; Material `ColorScheme` does not supply dedicated roles for all of these statuses. Define both container and foreground variants. Green and amber are optional conventions. Never use color alone to explain a result.

Source: [Themes](https://developer.android.com/design/ui/mobile/guides/styles/themes).

## 5. Typography and text hierarchy

**Official guidance:** Material organizes text styles into display, headline, title, body, and label families. Use the theme's typography instead of scattering fixed sizes across screens.

**Project role mapping:**

| Content role | `MaterialTheme.typography` style | Color role |
| --- | --- | --- |
| Top app-bar title | Let the app-bar component choose | Component default |
| Custom screen or result title | `headlineSmall` | `onSurface` |
| Major section heading | `titleLarge` | `onSurface` |
| Card or subsection heading | `titleMedium` | `onSurface` |
| Reading text | `bodyLarge` | `onSurface` |
| Screen description | `bodyLarge` | `onSurfaceVariant` |
| Row supporting description | `bodyMedium` | `onSurfaceVariant` |
| Helper or information text | `bodySmall`; larger for essential instructions | `onSurfaceVariant` |
| Minor metadata | `labelSmall` | `onSurfaceVariant` |
| Button label | Component default, typically `labelLarge` | Component default |
| Inline error | Component supporting text or `bodySmall` | `error` |

Use default style weights initially. Allow wrapping and font scaling. Never shrink essential copy to fit a fixed-height box. Display styles suit short hero content, not every screen title.

Android 14 supports nonlinear font scaling up to 200%. Specify custom text sizes and line heights in `sp`; keep layout padding in `dp`. Do not calculate text pixels by multiplying a single font-scale factor.

Source for scaling behavior: [Android 14 font scaling](https://developer.android.com/about/versions/14/features#non-linear-font-scaling).

Source: [Material typography in Compose](https://developer.android.com/develop/ui/compose/designsystems/material3#typography).

## 6. Descriptions and information text

**Project defaults:** Descriptions explain the screen's purpose or next step without repeating the title. Start with an 8 dp title-to-description gap and 24 dp before the main content. Align reading text to the start edge. Keep long explanations in body styles; helper copy is for short supplementary details.

An information block may use a neutral tonal surface, a short heading, and an optional information icon. Place it beside the relevant task. Essential eligibility, safety, or payment information stays readable and persistent. Links need a clear purpose and accessible interaction. Avoid all-caps paragraphs, decorative low-contrast gray, or unexplained internal error codes.

## 7. Shape, rounded corners, surfaces, and depth

**Official guidance:** Theme shapes help communicate hierarchy and identity. Material components have their own defaults; customization should remain coherent.

**Project defaults:**

| Element | Shape treatment |
| --- | --- |
| Buttons, FABs, chips, dialogs, sheets | Keep the Material component default initially |
| Custom content card | Use a named theme shape, such as `shapes.medium` |
| Custom emphasized container | Choose a larger theme shape consistently |
| Nested rounded container | Align corners visually with the enclosing surface |
| Image inside a card | Clip intentionally to its container geometry |
| Launcher icon | Use Android adaptive icon layers and masking |

Do not apply one radius to every component, turn every FAB into a circle, or copy an iOS continuous-corner template. Material surfaces commonly use tonal container roles, with elevation where needed. Avoid adding blur or heavy shadows as a universal surface treatment. Let library defaults resolve version-specific geometry.

Sources: [Material components](https://developer.android.com/design/ui/mobile/guides/components/material-overview), [Theme shape guidance](https://developer.android.com/design/ui/mobile/guides/styles/themes).

## 8. Layout, grids, and spacing

**Official guidance:** Android layout guidance uses an 8 dp grid and responsive composition. Compact content commonly starts with 16 dp margins; larger windows need adapted spacing.

**Project tokens:** Use 4 dp for fine internal adjustments, then 8, 16, 24, and 32 dp for increasingly separate content groups. Start custom card padding at 16 dp. Keep component-provided padding unless there is a deliberate reason to override it. Avoid double-padding content inside a scaffold or Material container.

Group related content through space, typography, and surfaces. Avoid putting every row in its own card. Use start/end alignment for RTL. Make long screens scrollable and limit reading width on expansive windows.

Sources: [Grids and units](https://developer.android.com/design/ui/mobile/guides/layout-and-content/grids-and-units), [Content composition](https://developer.android.com/design/ui/mobile/guides/layout-and-content/content-structure).

## 9. Edge-to-edge, system bars, and keyboard

**Official guidance:** On Android 15 and later, targeting SDK 35 activates edge-to-edge enforcement. Handle system bars and cutouts so content and controls remain usable. Material scaffolds can assist with inset handling.

**Project defaults:** Draw backgrounds behind system bars where appropriate, but keep important controls clear of bars, cutouts, and gesture regions. Apply scaffold content padding and consume insets correctly; do not add all inset modifiers indiscriminately. Handle the IME separately so the active field and submit action remain visible. Test gesture navigation and three-button navigation. Choose legible system-bar icon appearance for light and dark surfaces.

Source: [Window insets](https://developer.android.com/develop/ui/compose/system/insets).

## 10. Adaptive layouts and navigation destinations

**Official guidance:** Use the app's current window size classes, considering width and height independently. Adapt to available space rather than a fixed device label. `NavigationSuiteScaffold` can switch between a navigation bar and rail using size and posture information.

**Project patterns:**

| Available space | Suggested treatment |
| --- | --- |
| Compact | Single content pane; navigation bar for a small top-level destination set |
| Wider window | Navigation rail; consider list-detail or supporting panes |
| Very wide window | Deliberate multi-pane composition or drawer where useful; limit reading width |
| Short landscape or tabletop window | Reconsider vertical space and posture; avoid fixed bottom actions covering content |

Start with three to five destinations only when the app structure warrants them. Tabs organize sibling content within a destination. A FAB triggers an action; it is not a destination. Preserve useful selection, scroll position, and navigation state when resizing or folding.

Sources: [Window size classes](https://developer.android.com/develop/ui/compose/layouts/adaptive/use-window-size-classes), [Adaptive navigation](https://developer.android.com/develop/ui/compose/layouts/adaptive/build-adaptive-navigation).

## 11. Back, Up, and predictive Back

**Official guidance:** Back follows navigation history and can leave the app; Up follows the app hierarchy. Predictive Back lets people preview where the gesture will take them. Use supported navigation integration.

**Project defaults:** Keep Android system Back functional. Dismiss the active transient layer before navigating away where appropriate. Do not trap people at the home screen or require an exit-confirmation dialog routinely. A top app-bar navigation icon must represent the actual action: Up, menu, or close. Handle deep links and restored state deliberately. Intercept Back only for a concrete need such as unsaved changes, and preserve supported predictive behavior.

Sources: [Navigation principles](https://developer.android.com/guide/navigation/principles), [Predictive Back](https://developer.android.com/develop/ui/compose/system/predictive-back).

## 12. Buttons, FABs, and controls

**Official guidance:** Material provides filled, filled tonal, elevated, outlined, and text buttons with different emphasis levels.

**Project mapping:**

| Need | Starting component |
| --- | --- |
| Primary task action | `Button` |
| Significant alternative action | `FilledTonalButton` |
| Medium-emphasis alternative | `OutlinedButton` |
| Low-emphasis action | `TextButton` |
| Action needing surface separation | `ElevatedButton` |
| Screen's prominent creation action | FAB or extended FAB when appropriate |
| Compact icon action | `IconButton` with an accessible name |
| Immediate setting | Switch or checkbox with its associated label |
| Select or filter content | Appropriate chip or selection component |

Keep one clearly dominant action per task. Retain native press indication, focus, selected, and disabled states. Keep labels concise and allow necessary wrapping. A disabled action needs an understandable reason when the cause is unclear. Busy actions prevent duplicate submission and display meaningful progress.

Source: [Buttons](https://developer.android.com/develop/ui/compose/components/button).

## 13. Forms and text input

**Official guidance:** Material `TextField` and `OutlinedTextField` include field styling; `BasicTextField` is lower level. Configure input, state, and keyboard behavior appropriately for the installed API.

**Project defaults:** Use persistent labels, relevant keyboard options, IME actions, autofill, and secure password input. Place supporting instructions under the field. Use `isError` with specific supporting text when validation fails; color or an icon alone is insufficient. Keep valid input after errors. Validate when feedback helps rather than flashing an error before the person starts typing. Use a logical focus order and make password visibility controls accessible. Preserve non-sensitive drafts across relevant lifecycle changes without treating secrets as ordinary saved state.

Source: [Configure text fields](https://developer.android.com/develop/ui/compose/text/user-input).

## 14. Bottom sheets and dialogs

**Official guidance:** Material bottom sheets present supplementary content; modal sheets use a dedicated state and dismissal handling. Dialogs interrupt with a focused request or decision.

**Project patterns:** Use a modal bottom sheet for a short choice, filter, or contextual task. Use an alert dialog for a focused decision that needs attention. A long editing workflow usually deserves a screen. Honor Back and supported dismissal behavior, provide clear actions, and protect unsaved changes deliberately. Avoid custom iOS alert or sheet geometry. Ensure the keyboard, large text, and compact landscape layouts remain usable.

Sources: [Bottom sheets](https://developer.android.com/develop/ui/compose/components/bottom-sheets), [Dialogs](https://developer.android.com/develop/ui/compose/components/dialog).

## 15. Feedback, snackbars, loading, and empty states

**Official guidance:** Snackbars provide brief contextual feedback and can offer an action. Progress indicators distinguish measurable completion from an unknown duration.

**Project patterns:**

| State | Presentation |
| --- | --- |
| Routine completion | Update content; snackbar when confirmation helps |
| Reversible removal | Snackbar with a working Undo action |
| Local failure | Persistent feedback beside the affected content |
| Background refresh | Keep usable content; small progress cue |
| First load | Progress plus context when needed |
| Known completion fraction | Determinate indicator using actual progress |
| Unknown duration | Indeterminate indicator; do not invent percentages |
| No items yet | Explanation and useful creation action |
| No search results | Preserve query and offer editing or clearing filters |

Use `SnackbarHost` and native accessibility-aware behavior. An ephemeral message should not be the only record of an important failure or receipt. Avoid repeated identical snackbars and blocking all navigation during a routine refresh.

Sources: [Snackbar](https://developer.android.com/develop/ui/compose/components/snackbar), [Progress indicators](https://developer.android.com/develop/ui/compose/components/progress).

## 16. Success screen styles

**Project pattern — not a universal Material template:** Use a dedicated success screen for meaningful completion, such as a confirmed booking or transaction. Routine edits can use lighter feedback.

1. Optional checkmark, starting at 48 dp in an accessible app success color. Decorative duplicates should not be announced separately.
2. Specific `headlineSmall` title in `onSurface`: “Booking confirmed.”
3. `bodyLarge` description in `onSurfaceVariant` explaining the actual result.
4. Optional details on a tonal surface: reference, date, amount, or next step. Essential facts use `onSurface`.
5. A prominent “View booking” action; a quieter Done action only when useful.

Start icon-to-title spacing at 16 dp, title-to-description at 8 dp, and separate groups by 24 dp. These are project tokens. Center short confirmation copy if helpful; align detailed summaries to the start edge. Support scrolling and Android Back. Do not show confirmed success while the operation is merely queued or processing. Avoid automatically dismissing an important receipt.

## 17. Error screens and recovery

**Project pattern:** Use a blocking error screen only when the main content cannot be used. Keep local failures local.

Use an optional relevant icon, a specific `headlineSmall` title, a `bodyLarge` explanation, and one useful recovery action. For example: “Couldn’t load bookings,” followed by an accurate explanation and “Try again.” Use `onSurface` and `onSurfaceVariant` for reading text; use `error` for the relevant cue or an `errorContainer`/`onErrorContainer` pair for a grouped message. Retry is a normal primary action, not destructive.

| Failure | Recovery pattern |
| --- | --- |
| Invalid input | Explain the correction beside the field |
| Offline | Explain known connectivity state; usable cached content where available |
| Session expired | Sign in, then return to the task |
| Missing permission | Explain the affected feature and offer a supported alternative |
| Temporary service failure | Retry when useful; do not promise unknown recovery times |
| One failed section | Preserve other sections and retry the affected part |

Retain drafts and prior usable content. During retry, show progress and prevent duplicate requests. Do not expose stack traces as the main copy or create endless retry loops. Use the same spacing and surface family as other result screens. Empty content and empty search results are separate states, not failures.

## 18. Permissions and system-owned flows

**Official guidance:** Request runtime permissions in context, explain the need when appropriate, and degrade gracefully after denial.

**Project defaults:** Ask when the person invokes the affected feature rather than requesting everything at startup. Use Android's permission dialog, system picker, share sheet, and other supported flows instead of imitating them. Explain which feature is unavailable without blocking unrelated content. Offer a settings route when it is appropriate and supported; do not immediately redirect or repeatedly pressure people after denial.

Source: [Request runtime permissions](https://developer.android.com/training/permissions/requesting).

## 19. Icons, launcher identity, and motion

**Official guidance:** Adaptive launcher icons use separate layers so launchers can apply their masks; themed icons can use a monochrome layer. Compose provides animation APIs for changes in content and state.

**Project defaults:** Use a consistent Material Symbols or project icon family for interface actions. Use auto-mirrored directional icons where appropriate for RTL. Do not bake a fixed launcher mask into artwork. Distinguish launcher assets from interface icons. Keep motion purposeful, interruptible, and compatible with system animation settings; no task depends on an animation finishing. Avoid inventing one mandatory duration for every transition or applying celebratory animation to routine actions.

Sources: [Adaptive icons](https://developer.android.com/develop/ui/compose/system/icon_design_adaptive), [Compose animations](https://developer.android.com/develop/ui/compose/animation/quick-guide).

## 20. Accessibility and text scaling

**Official guidance:** Aim for at least 48 × 48 dp touch targets. Compose components provide useful accessibility defaults, but custom controls need deliberate semantics. Label meaningful icons and avoid redundant descriptions for decorative imagery.

**Project acceptance criteria:**

- Every ordinary action has a non-overlapping 48 dp or larger hit region, even when its visible icon is smaller.
- Font and display scaling do not clip labels or hide critical actions; test at 200% font scale as well as normal scale.
- TalkBack conveys headings, control roles, selected or checked states, and meaningful error text.
- Mark custom headings with heading semantics; use suitable live-region and pane semantics for important changes without repeatedly interrupting speech.
- Ordinary reading text targets at least 4.5:1 contrast; verify relevant exceptions separately. Critical non-text control boundaries and indicators target 3:1 where applicable.
- Support keyboard focus and traversal, Switch Access, and alternatives to gesture-only actions.
- Meaningful status remains understandable without color, sound, or motion alone.

Sources: [Accessibility API defaults](https://developer.android.com/develop/ui/compose/accessibility/api-defaults), [Semantics](https://developer.android.com/develop/ui/compose/accessibility/semantics), [Accessible apps](https://developer.android.com/guide/topics/ui/accessibility/apps), [Mobile accessibility design](https://developer.android.com/design/ui/mobile/guides/foundations/accessibility).

## 21. Example project tokens

These proposed tokens reference native theme roles. Component defaults take priority over custom geometry. Confirm API availability against the pinned library.

```yaml
platform: android-mobile
layoutUnits: dp
textUnits: sp
ui: compose-material3
expressive: adopt-supported-components-deliberately
appearance: follow-system-with-light-dark-fallback
color:
  dynamic: supported-on-api-31-plus
  screen: surface
  content: onSurface
  supportingContent: onSurfaceVariant
  container: surfaceContainer
  primaryAction: [primary, onPrimary]
  tonalAction: [secondaryContainer, onSecondaryContainer]
  errorContainer: [errorContainer, onErrorContainer]
  success: accessible-app-theme-extension
  warning: accessible-app-theme-extension
text:
  appBarTitle: native-component-default
  screenTitle: headlineSmall
  sectionHeading: titleLarge
  cardHeading: titleMedium
  reading: bodyLarge
  description: bodyLarge
  rowDescription: bodyMedium
  helper: bodySmall
  metadata: labelSmall
  actionLabel: native-component-default
spacingDp: [4, 8, 16, 24, 32]
compactContentMarginDp: 16
customCardPaddingDp: 16
customCardShape: material-theme-medium
ordinaryTouchTargetDp: [48, 48]
resultIconDp: 48
resultGapsDp: [16, 8, 24]
navigation: android-back-up-and-adaptive-destinations
insets: handle-system-bars-cutouts-and-ime
accessibility: talkback-semantics-and-scalable-layout
```

## 22. Copyable AI prompt and review checklist

> Design and implement a native Android mobile app using this guide. Establish SDK targets, UI framework, and pinned libraries first. Use Material theme roles and components with native Android navigation and Back behavior. Propose consistent heading, text, description, and helper styles, plus content, loading, empty, success, and error states. Specify adaptive layouts, edge-to-edge and IME handling, scalable typography, and accessibility semantics. Keep project tokens distinct from verified platform or component defaults. Explain version-specific APIs and deliberate departures.

Before shipping, verify the actual implementation on compact and wider windows, portrait and landscape, light and dark themes, dynamic palettes where enabled, larger font and display scales, RTL, keyboard-visible forms, gesture and three-button navigation, TalkBack, and recovery from failed operations. Test state restoration for relevant navigation and drafts. This Markdown guide defines design intent; it does not certify an implementation has passed these checks.
