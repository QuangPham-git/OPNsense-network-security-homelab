# 10. DVWA XSS Validation

## Scope

Only use these payloads against the isolated DVWA target. The objective is to compare low, medium, and high security implementations and document why filtering changes the result.

## Reflected XSS

1. Set security to **Low**.
2. Open **XSS (Reflected)**.
3. Inspect the page source.
4. Enter a harmless proof payload:

```html
<script>alert('XSS')</script>
```

5. Record the input, rendered response, and alert.
6. Repeat at Medium and High.
7. Compare how case-sensitive string replacement, encoding, or allow-listing changes execution.

A harmless alternate proof for filter-comparison is:

```html
<img src=x onerror="alert('XSS')">
```

## Stored XSS

1. Set security to **Low**.
2. Open **XSS (Stored)**.
3. Inspect the source and field-length restrictions.
4. Submit a harmless alert payload in the controlled guestbook/comment field.
5. Reload the page and verify the payload persists.
6. Reset the database after the exercise.
7. Repeat at Medium and High and document encoding/sanitization differences.

## DOM XSS

1. Open the DOM XSS page at Low.
2. Identify the URL parameter used by client-side JavaScript.
3. Replace the parameter value with a harmless alert payload.
4. Observe whether the browser executes the value at runtime.
5. Repeat at Medium and High.

## Evidence status

The archive contains DVWA setup and terminal screenshots, but no clean alert-box capture that can be confidently assigned to every XSS variant. This section therefore preserves the reproducible procedure and the exact evidence that should be recaptured:

- Security-level indicator
- Input or URL payload
- Alert result
- Source-code comparison
- Reset/cleanup confirmation

## Mitigations

- Context-appropriate output encoding
- Strict input validation
- Safe DOM APIs such as `textContent`
- Content Security Policy
- HttpOnly and SameSite session cookies
- Avoid blacklist-only filtering
