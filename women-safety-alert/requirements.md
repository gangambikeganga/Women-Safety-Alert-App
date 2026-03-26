# Requirements Document

## Introduction

The Women Safety Alert application is a single-file mobile web app (HTML/CSS/JavaScript) that helps women feel safer while traveling alone. It provides a quick-access SOS button to trigger emergency alerts, shares the user's real-time location with pre-saved trusted contacts, and offers safety tips and guidance. The app runs entirely in the browser with no backend, using localStorage for persistence and the browser Geolocation API for location data.

## Glossary

- **App**: The Women Safety Alert single-page web application
- **SOS_Button**: The primary emergency trigger button on the home screen
- **Alert**: An emergency notification containing the user's name, location, and a distress message
- **Trusted_Contact**: A saved contact entry consisting of a name and phone number
- **Contact_Manager**: The UI section for adding, viewing, and deleting trusted contacts
- **Location_Service**: The browser Geolocation API integration responsible for obtaining coordinates
- **Alert_System**: The module that composes and dispatches alert messages to trusted contacts
- **Settings_Panel**: The UI section for configuring app preferences such as custom alert message and user name
- **Info_Section**: The UI section displaying safety tips and emergency guidance
- **Confirmation_Toast**: A transient on-screen notification confirming that an alert has been sent

---

## Requirements

### Requirement 1: SOS Alert Trigger

**User Story:** As a woman traveling alone, I want to trigger an emergency alert quickly, so that my trusted contacts are notified immediately when I feel unsafe.

#### Acceptance Criteria

1. THE App SHALL display a prominent SOS_Button on the home screen that is reachable within one tap from the default view.
2. WHEN the user presses the SOS_Button, THE Alert_System SHALL compose and dispatch an Alert to all saved Trusted_Contacts.
3. WHEN the SOS_Button is held for 3 seconds, THE Alert_System SHALL trigger the same alert dispatch as a single press, providing an alternative activation method.
4. WHEN an Alert is dispatched, THE App SHALL display a Confirmation_Toast visible for at least 3 seconds confirming the alert was sent.
5. IF no Trusted_Contacts are saved, THEN THE App SHALL display an inline warning message prompting the user to add at least one contact before sending an alert.

---

### Requirement 2: Location Sharing

**User Story:** As a woman in distress, I want my current location included in the alert, so that my trusted contacts know exactly where to find me.

#### Acceptance Criteria

1. WHEN the App loads, THE Location_Service SHALL request permission to access the device's geolocation.
2. WHEN geolocation permission is granted, THE Location_Service SHALL obtain the user's current latitude and longitude coordinates.
3. WHEN an Alert is dispatched, THE Alert_System SHALL include a Google Maps link constructed from the user's current coordinates in the Alert message.
4. IF geolocation permission is denied or unavailable, THEN THE Location_Service SHALL fall back to a simulated location and THE App SHALL display a visible indicator that the location is simulated.
5. WHILE the App is open, THE Location_Service SHALL refresh the user's coordinates at most every 30 seconds to maintain location accuracy.

---

### Requirement 3: Trusted Contact Management

**User Story:** As a user, I want to add and manage trusted contacts, so that I can control who receives my emergency alerts.

#### Acceptance Criteria

1. THE Contact_Manager SHALL allow the user to add a Trusted_Contact by entering a name and a phone number.
2. WHEN a contact is submitted, THE Contact_Manager SHALL validate that both the name field and the phone number field are non-empty before saving.
3. WHEN a contact is submitted, THE Contact_Manager SHALL validate that the phone number contains only digits, spaces, plus signs, and hyphens, and is between 7 and 15 digits in length.
4. IF a contact submission fails validation, THEN THE Contact_Manager SHALL display a descriptive inline error message identifying which field is invalid.
5. THE Contact_Manager SHALL display all saved Trusted_Contacts in a list showing each contact's name and phone number.
6. WHEN the user deletes a contact, THE Contact_Manager SHALL remove that contact from the saved list and from future Alert dispatches.
7. THE App SHALL persist all Trusted_Contacts to localStorage so that contacts are retained across browser sessions.

---

### Requirement 4: Alert Message Composition

**User Story:** As a user, I want the alert message to be clear and informative, so that my contacts understand the situation and can act quickly.

#### Acceptance Criteria

1. THE Alert_System SHALL compose each Alert message to include the sender's name, a distress statement, the current timestamp, and a location link.
2. WHERE a custom alert message is configured in Settings_Panel, THE Alert_System SHALL include the custom message text in the Alert instead of the default distress statement.
3. THE Alert_System SHALL simulate sending the Alert by logging the composed message to the browser console and displaying it in the Confirmation_Toast for each contact.
4. THE Alert_System SHALL format the Alert message so it is suitable for SMS delivery (plain text, under 320 characters excluding the location link).

---

### Requirement 5: Contact Section UI

**User Story:** As a user, I want a dedicated contacts screen, so that I can easily manage my trusted contacts without cluttering the main SOS view.

#### Acceptance Criteria

1. THE App SHALL provide a Contacts navigation tab that displays the Contact_Manager when selected.
2. WHEN the Contacts tab is active, THE Contact_Manager SHALL show an add-contact form and the list of existing Trusted_Contacts.
3. THE Contact_Manager SHALL support a minimum of 10 saved Trusted_Contacts without degrading UI layout.

---

### Requirement 6: Info Section

**User Story:** As a user, I want access to safety tips and emergency guidance, so that I can make informed decisions in threatening situations.

#### Acceptance Criteria

1. THE App SHALL provide an Info navigation tab that displays the Info_Section when selected.
2. THE Info_Section SHALL display at least 5 actionable safety tips relevant to personal safety while traveling.
3. THE Info_Section SHALL display a list of at least 3 emergency contact numbers (e.g., national emergency, police, women's helpline) with their service descriptions.

---

### Requirement 7: Settings Section

**User Story:** As a user, I want to configure basic app settings, so that the app reflects my personal information and preferences.

#### Acceptance Criteria

1. THE App SHALL provide a Settings navigation tab that displays the Settings_Panel when selected.
2. THE Settings_Panel SHALL allow the user to enter and save their name, which is used in Alert message composition.
3. THE Settings_Panel SHALL allow the user to enter and save a custom alert message of up to 100 characters.
4. WHEN the user saves settings, THE App SHALL persist the name and custom alert message to localStorage.
5. THE Settings_Panel SHALL display a toggle to enable or disable the 3-second hold-to-alert activation method.

---

### Requirement 8: Responsive UI and Navigation

**User Story:** As a user on a mobile device, I want the app to be easy to use on a small screen, so that I can operate it quickly under stress.

#### Acceptance Criteria

1. THE App SHALL render correctly on viewport widths from 320px to 1440px without horizontal scrolling.
2. THE App SHALL use a bottom navigation bar with tabs for Home, Contacts, Info, and Settings.
3. THE SOS_Button SHALL have a minimum touch target size of 120x120 pixels on mobile viewports.
4. THE App SHALL use high-contrast colors (minimum 4.5:1 contrast ratio for text) to ensure readability under varying lighting conditions.
5. WHEN a navigation tab is selected, THE App SHALL display only the content for that tab and hide all other tab content without a full page reload.

---

### Requirement 9: Confirmation Feedback

**User Story:** As a user who has triggered an alert, I want clear visual confirmation, so that I know the alert was sent successfully.

#### Acceptance Criteria

1. WHEN an Alert is dispatched, THE App SHALL display a Confirmation_Toast containing the text "Alert sent to [N] contact(s)" where N is the number of contacts notified.
2. THE Confirmation_Toast SHALL be visible for 3 seconds and then automatically dismiss.
3. THE Confirmation_Toast SHALL appear in a fixed position on screen that does not obscure the SOS_Button.
