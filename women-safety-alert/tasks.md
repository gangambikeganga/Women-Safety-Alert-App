# Implementation Plan: Women Safety Alert

## Overview

Implement a single `index.html` file containing all HTML, CSS, and JavaScript for the Women Safety Alert mobile web app. The app uses the Revealing Module Pattern, localStorage for persistence, and the browser Geolocation API. No build step or external dependencies are required for the app itself; tests use fast-check via CDN.

## Tasks

- [x] 1. Scaffold index.html with base structure, CSS variables, and layout
  - Create `index.html` with `<!DOCTYPE html>`, viewport meta tag, and base CSS custom properties (colors, spacing, typography)
  - Implement mobile-first CSS: bottom nav bar, tab panels, SOS button sizing (min 120×120px), high-contrast palette
  - Add four tab panel `<div>` elements: `#tab-home`, `#tab-contacts`, `#tab-info`, `#tab-settings`
  - Add bottom `<nav>` with four buttons: Home, Contacts, Info, Settings
  - _Requirements: 8.1, 8.2, 8.3, 8.4_

- [x] 2. Implement StorageModule and UIModule
  - [x] 2.1 Implement StorageModule IIFE
    - Write `getContacts()`, `saveContacts(list)`, `getSettings()`, `saveSettings(obj)`, `clearAll()` with `wsa_` key namespace
    - Wrap all localStorage calls in try/catch; on `SecurityError` set an in-memory fallback and expose `StorageModule.unavailable` flag
    - _Requirements: 3.7, 7.4_

  - [ ]* 2.2 Write property test for StorageModule contact round-trip
    - **Property 5: Contact persistence round-trip**
    - **Validates: Requirements 3.7**

  - [ ]* 2.3 Write property test for StorageModule settings round-trip
    - **Property 7: Settings persistence round-trip**
    - **Validates: Requirements 7.4**

  - [x] 2.4 Implement UIModule IIFE
    - Write `showTab(tabName)`: toggle `active` class on panels and nav buttons
    - Write `showToast(message, ms)`: create fixed-position element, append, auto-remove after `ms` (default 3000)
    - Write `showWarning(el, message)` and `clearWarning(el)` for inline field errors
    - Write `renderContacts(list)`: build one `<li>` per contact showing name, phone, and a delete button
    - _Requirements: 8.5, 9.1, 9.2, 9.3, 3.5_

  - [ ]* 2.5 Write property test for tab switching (Property 9)
    - **Property 9: Tab switching shows only active tab**
    - **Validates: Requirements 8.5**

  - [ ]* 2.6 Write property test for contact list rendering (Property 11)
    - **Property 11: Contact list renders all saved contacts**
    - **Validates: Requirements 3.5**

- [x] 3. Checkpoint — verify StorageModule and UIModule
  - Ensure all tests pass, ask the user if questions arise.

- [x] 4. Implement LocationModule and ContactModule
  - [x] 4.1 Implement LocationModule IIFE
    - On `init()`: call `navigator.geolocation.watchPosition`; on success store `{ lat, lng, simulated: false }`; on error/unavailable store `{ lat: 0, lng: 0, simulated: true }` and show persistent banner
    - Set `setInterval` every 30 000 ms to call `getCurrentPosition` and refresh coords
    - Expose `getCoords()` and `isSimulated()`
    - _Requirements: 2.1, 2.2, 2.4, 2.5_

  - [x] 4.2 Implement ContactModule IIFE
    - Write `validate(name, phone)`: name non-empty after trim; phone matches `/^[0-9 +\-]{7,15}$/` with digit count 7–15; return `{ ok, errors }`
    - Write `add(name, phone)`: call validate, on pass generate id via `crypto.randomUUID()` (fallback `Date.now()+Math.random()`), push to StorageModule, return `{ ok }`
    - Write `getAll()` and `remove(id)`
    - _Requirements: 3.1, 3.2, 3.3, 3.4, 3.6_

  - [ ]* 4.3 Write property test for contact validation (Property 4)
    - **Property 4: Contact validation rejects invalid inputs**
    - **Validates: Requirements 3.2, 3.3, 3.4**

  - [ ]* 4.4 Write property test for contact deletion (Property 6)
    - **Property 6: Contact deletion removes from list**
    - **Validates: Requirements 3.6**

- [x] 5. Implement AlertModule
  - [x] 5.1 Implement AlertModule IIFE
    - Write `compose(contacts, coords, settings)`: build message string per contact with name, distress statement (or `customMessage` if set), ISO timestamp, and Google Maps URL; enforce body ≤ 320 chars
    - Write `dispatch(contacts, coords, settings)`: call `compose`, `console.log` each message, return `{ sent: number }`
    - _Requirements: 4.1, 4.2, 4.3, 4.4_

  - [ ]* 5.2 Write property test for alert dispatch count (Property 1)
    - **Property 1: Alert dispatched to all contacts**
    - **Validates: Requirements 1.2, 1.3**

  - [ ]* 5.3 Write property test for alert message fields (Property 2)
    - **Property 2: Alert message contains all required fields**
    - **Validates: Requirements 4.1, 4.4, 2.3**

  - [ ]* 5.4 Write property test for custom message override (Property 3)
    - **Property 3: Custom message replaces default distress statement**
    - **Validates: Requirements 4.2**

  - [ ]* 5.5 Write property test for custom message length enforcement (Property 8)
    - **Property 8: Custom message length enforcement**
    - **Validates: Requirements 7.3**

- [x] 6. Checkpoint — verify LocationModule, ContactModule, AlertModule
  - Ensure all tests pass, ask the user if questions arise.

- [x] 7. Build Home tab UI and SOS button logic
  - [x] 7.1 Implement Home tab HTML and SOS button
    - Add large circular SOS button (min 120×120px) inside `#tab-home`
    - Add location status indicator element and simulated-location banner (hidden by default)
    - Add inline warning element for no-contacts case (hidden by default)
    - _Requirements: 1.1, 8.3_

  - [x] 7.2 Wire SOS button click and hold-to-alert events in AppInit
    - On click: call `ContactModule.getAll()`; if empty show inline warning; else call `AlertModule.dispatch` then `UIModule.showToast("Alert sent to N contact(s)", 3000)`
    - On `pointerdown`: start 3-second `setTimeout`; on `pointerup`/`pointerleave` clear timer (hold-to-alert, gated by `settings.holdToAlertEnabled`)
    - _Requirements: 1.2, 1.3, 1.4, 1.5_

  - [ ]* 7.3 Write property test for toast content and lifecycle (Property 10)
    - **Property 10: Toast displays correct contact count and auto-dismisses**
    - **Validates: Requirements 1.4, 9.1, 9.2**

- [x] 8. Build Contacts tab UI
  - [x] 8.1 Implement Contacts tab HTML
    - Add add-contact form with name input, phone input, submit button, and inline error containers
    - Add `<ul id="contact-list">` for rendered contacts
    - _Requirements: 5.1, 5.2_

  - [x] 8.2 Wire contact form submit in AppInit
    - On submit: call `ContactModule.add(name, phone)`; on error call `UIModule.showWarning` for each field; on success call `UIModule.renderContacts` and reset form
    - _Requirements: 3.1, 3.2, 3.3, 3.4_

  - [x] 8.3 Wire contact delete buttons in AppInit (event delegation on `#contact-list`)
    - On delete click: call `ContactModule.remove(id)` then `UIModule.renderContacts`
    - _Requirements: 3.6_

- [x] 9. Build Info tab and Settings tab UI
  - [x] 9.1 Implement Info tab HTML
    - Add at least 5 safety tips as `<li>` elements
    - Add at least 3 emergency numbers with service descriptions
    - _Requirements: 6.1, 6.2, 6.3_

  - [x] 9.2 Implement Settings tab HTML and wire save logic
    - Add name input, custom message textarea (maxlength=100), hold-to-alert toggle checkbox, save button, clear-data button
    - On save: truncate customMessage to 100 chars if needed, call `StorageModule.saveSettings`, show toast "Settings saved"
    - On clear: call `StorageModule.clearAll()`, reload contacts and settings state
    - _Requirements: 7.1, 7.2, 7.3, 7.4, 7.5_

- [x] 10. Implement AppInit bootstrap and wire all modules together
  - Write `AppInit` function called on `DOMContentLoaded`
  - Load settings and contacts from StorageModule; populate Settings form; call `UIModule.renderContacts`
  - Call `LocationModule.init()`; on simulated flag show persistent banner
  - Wire tab nav buttons to `UIModule.showTab`
  - Show `StorageModule.unavailable` warning if storage is unavailable
  - _Requirements: 2.1, 8.5_

- [x] 11. Final checkpoint — full integration
  - Ensure all tests pass, ask the user if questions arise.

- [x] 12. Create tests.html with fast-check property-based tests
  - Create `tests.html` loading fast-check UMD from CDN and the app's module functions
  - Implement all 11 property-based tests (Properties 1–11) with ≥ 100 iterations each
  - Tag each test with `// Feature: women-safety-alert, Property N: <property_text>`
  - _Requirements: all correctness properties from design.md_

## Notes

- Tasks marked with `*` are optional and can be skipped for a faster MVP
- All code lives in a single `index.html`; tests live in `tests.html`
- Each task references specific requirements for traceability
- Checkpoints ensure incremental validation
