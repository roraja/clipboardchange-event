# W3C Security and Privacy Self-Review Questionnaire
## For clipboardchange Event

**Feature:** clipboardchange Event  
**Specification:** [W3C Clipboard APIs](https://w3c.github.io/clipboard-apis/#clipboard-event-clipboardchange)  
**Editor's Draft URL:** https://w3c.github.io/clipboard-apis/  
**TAG Review Issue:** https://github.com/w3ctag/design-reviews/issues/1017  
**Date:** June 26, 2025  
**Reviewers:** Rohan Raja (Microsoft Edge)

---

## 2.1. What information does this feature expose, and for what purposes?

The clipboardchange event exposes **clipboard data type information** (MIME types) to web applications when the system clipboard contents change. The event does not expose the actual clipboard contents, only metadata about what types of data are available.

**Information exposed:**
- An array of MIME type strings (e.g., "text/plain", "text/html", "image/png")
- Only standard, well-known MIME types are exposed
- Custom/application-specific data types are intentionally excluded

**Purpose and benefits:**
- Enables dynamic UI updates in web editors (showing relevant paste options)
- Improves user experience in collaborative applications like Google Docs, Excel Online
- Enables efficient clipboard synchronization in remote desktop scenarios
- Replaces inefficient polling mechanisms currently used by web applications

**Risk vs. benefit analysis:**
The benefits to users outweigh the privacy risks because:
1. Only type metadata is exposed, not content
2. No user permissions are required, reducing permission fatigue
3. Enables significantly better user experiences in productivity applications
4. Fingerprinting risk is mitigated by excluding custom data types

## 2.2. Do features in your specification expose the minimum amount of information necessary to implement the intended functionality?

**Yes.** The clipboardchange event deliberately exposes only the minimum information necessary:

- **Only data types are exposed** - not the actual clipboard contents
- **Only standard MIME types** - custom/proprietary formats are excluded to prevent fingerprinting
- **No additional metadata** - no information about data size, creation time, or source application
- **No persistent identifiers** - each event is independent with no correlation across sessions

The original design considered exposing richer information (like DataTransfer objects), but this was rejected to minimize privacy risks while still enabling the core use cases.

## 2.3. Do the features in your specification expose personal information, personally-identifiable information (PII), or information derived from either?

**No.** The clipboardchange event specifically does not expose:
- Actual clipboard content (which could contain PII)
- Information about the source application
- User-specific identifiers
- File paths or system information

Only standardized MIME type strings are exposed, which are not personal information. The design explicitly prevents access to clipboard contents to avoid PII exposure.

## 2.4. How do the features in your specification deal with sensitive information?

The clipboardchange event is designed to avoid exposure of sensitive information:

**Protections implemented:**
1. **Content isolation** - Never exposes actual clipboard data, only type metadata
2. **Focus requirement** - Only fires when the document has focus, preventing background monitoring
3. **Standard types only** - Excludes custom data types that could reveal application usage patterns
4. **No cross-origin correlation** - Each document receives independent events

**No sensitive information categories are exposed:**
- No health, financial, or demographic information
- No location or device information  
- No credentials or authentication data
- No file system or application usage patterns

## 2.5. Does data exposed by your specification carry related but distinct information that may not be obvious to users?

**Minimal risk.** The MIME types exposed are standardized and well-understood:

- "text/plain", "text/html" - indicate text content types
- "image/png", "image/jpeg" - indicate image formats
- Standard web formats only

**Potential indirect information:**
- The presence of specific MIME types could suggest the user's current activity (e.g., copying images vs. text)
- However, this information is transient and not persistently identifying

**Mitigation:** Custom data types are excluded specifically to prevent applications from embedding hidden identifying information in MIME type strings.

## 2.6. Do the features in your specification introduce state that persists across browsing sessions?

**No.** The clipboardchange event is completely stateless:

- No data is stored by the browser
- No persistent identifiers are created
- Each event is independent and ephemeral
- No correlation possible across browsing sessions
- No cache or storage mechanisms involved

The event is purely reactive to system clipboard changes and introduces no persistent state whatsoever.

## 2.7. Do the features in your specification expose information about the underlying platform to origins?

**Limited platform information.** The event exposes some platform characteristics:

**Platform information exposed:**
- Available MIME types supported by the platform
- Implicit indication that clipboard monitoring is supported (Windows) vs. not (macOS)

**Cross-platform consistency:**
- The same MIME types are exposed across platforms where possible
- Standard web MIME types provide consistency
- Custom/platform-specific types are excluded

**Fingerprinting risk mitigation:**
- Limited to standard MIME types only
- No unique platform identifiers
- No hardware or system configuration details
- Behavior designed to be consistent across implementations

## 2.8. Does this specification allow an origin to send data to the underlying platform?

**No.** The clipboardchange event is read-only and does not allow origins to:
- Modify clipboard contents
- Send data to the operating system
- Trigger clipboard operations
- Interact with other applications

This is purely a notification mechanism and provides no data transmission capabilities to the platform.

## 2.9. Do features in this specification enable access to device sensors?

**No.** The clipboardchange event does not access any device sensors. It only monitors system clipboard state changes through standard operating system APIs.

## 2.10. Do features in this specification enable new script execution/loading mechanisms?

**No.** The clipboardchange event does not enable any script execution or loading mechanisms. It is a standard DOM event that fires within the existing JavaScript execution context.

## 2.11. Do features in this specification allow an origin to access other devices?

**No.** The clipboardchange event only accesses the local system clipboard and does not enable access to:
- Network devices
- Connected peripherals  
- Other computers or devices
- Local network resources

## 2.12. Do features in this specification allow an origin some measure of control over a user agent's native UI?

**No.** The clipboardchange event does not affect the user agent's UI in any way. It is a passive notification mechanism that does not:
- Modify browser chrome
- Control native dialogs
- Affect browser controls or indicators
- Influence user agent behavior

## 2.13. What temporary identifiers do the features in this specification create or expose to the web?

**None.** The clipboardchange event does not create or expose any identifiers:
- No session IDs or tokens
- No correlation IDs across events
- No persistent or temporary identifiers
- Each event is completely independent

The MIME type strings themselves are standardized constants, not identifiers.

## 2.14. How does this specification distinguish between behavior in first-party and third-party contexts?

**Current specification:** The clipboardchange event currently fires equally in first-party and third-party contexts when the document has focus.

**Recommendations for implementation:**
- **Consider restricting to first-party contexts only** to prevent third-party tracking
- **Use Permissions Policy** to allow first-party control over third-party access
- **Implement focus requirements strictly** to prevent background monitoring by third parties

**Privacy considerations for third-party usage:**
- Third parties could potentially use clipboard type changes for timing correlation
- Focus requirement provides some protection but may not be sufficient
- Implementers should consider additional restrictions for third-party contexts

## 2.15. How do the features in this specification work in the context of a browser's Private Browsing or Incognito mode?

**Recommended behavior in private browsing:**

**Information isolation:**
- Events should fire normally but with consistent behavior across private/normal modes
- No correlation should be possible between private and normal browsing sessions
- Clipboard state should be handled consistently regardless of browsing mode

**No additional restrictions needed:**
- Since only MIME types (not content) are exposed, no additional privacy protections are required
- The focus requirement already prevents background monitoring
- No persistent state is created that could leak across modes

**Implementation guidance:**
- Treat clipboard events identically in private browsing mode
- Ensure no shared state between private and normal sessions
- Maintain the same MIME type exposure behavior

## 2.16. Does this specification have both "Security Considerations" and "Privacy Considerations" sections?

**Yes.** The W3C Clipboard APIs specification includes comprehensive Security and Privacy Considerations sections that address:

**Security Considerations:**
- Focus requirement to prevent background monitoring
- Exclusion of custom data types to prevent malicious MIME type injection
- No clipboard content exposure to prevent sensitive data leakage

**Privacy Considerations:**
- Fingerprinting mitigation through standard MIME types only
- No persistent identifiers or cross-session correlation
- Limited information exposure to minimize privacy risk

Both sections will be enhanced in the final specification based on this questionnaire review.

## 2.17. Do features in your specification enable origins to downgrade default security protections?

**No.** The clipboardchange event does not enable any security downgrades:
- No permissions can be bypassed
- No same-origin policy relaxation
- No security headers can be modified
- No default protections are weakened

The feature operates within existing security boundaries and adds no new attack surfaces.

## 2.18. What happens when a document that uses your feature is kept alive in BFCache (instead of getting destroyed) after navigation, and potentially gets reused on future navigations back to the document?

**Appropriate BFCache behavior:**

**When document becomes non-fully active:**
- clipboardchange events should stop firing immediately
- No events should be queued or delivered to non-active documents
- Prevents information leakage about clipboard activity when user has navigated away

**When document becomes fully active again:**
- Events should resume firing normally
- A single "catch-up" event should fire with current clipboard types if changed while inactive
- No historical events should be replayed

**Implementation guidance:**
```javascript
// Event should only fire when document.isFullyActive() === true
if (!document.isFullyActive()) {
  // Suppress clipboardchange events
  return;
}
```

This ensures privacy by preventing clipboard monitoring when the user has navigated away from the page.

## 2.19. What happens when a document that uses your feature gets disconnected?

**Behavior for disconnected documents:**

When an iframe containing a document becomes disconnected:
- All clipboardchange event listeners should be effectively disabled
- No events should fire to the disconnected document
- Any pending events should be discarded
- Resources associated with clipboard monitoring should be cleaned up

This follows the same principle as BFCache handling - documents that are no longer visible to users should not receive privacy-sensitive information about clipboard activity.

## 2.20. Does your spec define when and how new kinds of errors should be raised?

**No new error conditions.** The clipboardchange event does not define any new error states:
- Events are purely informational and cannot fail
- No error objects are created or exposed
- No debugging information is included that could leak system details
- Failures in clipboard monitoring are handled silently at the platform level

This approach prevents information leakage through error conditions while maintaining reliability.

## 2.21. Does your feature allow sites to learn about the user's use of assistive technology?

**No.** The clipboardchange event does not expose information about assistive technology usage:

- MIME types are the same regardless of how clipboard operations are performed
- No distinction between keyboard shortcuts, mouse operations, or assistive technology interactions
- No timing information that could reveal interaction patterns
- No device-specific information that could indicate assistive technology use

The feature is designed to be completely agnostic to the user's interaction method.

## 2.22. What should this questionnaire have asked?

NA