# AI Review Knowledge Base

This document accumulates code review knowledge learned by the AI.

All items are used as a checklist in Phase 4.5 of the automated workflow.

---

## JavaScript / TypeScript

### Reliability

- [ ] Does the code attempt to use the return value of a function that does not return anything (e.g., an `async` function without a `return` statement)?
- [ ] Is the `catch` block of a `try-catch` statement empty? It should at least log the error or re-throw it.

### Maintainability

- [ ] Are there any unnecessary escape characters in strings or regular expressions?

## HTML

### Accessibility

- [ ] Does every form control (`<input>`, `<textarea>`, `<select>`) have an associated `<label>` tag?

---

## Learning History

### BOC-48 (2025-09-10): SonarCloud Detection Results

**Missed Issues by Claude Self-Review:**

1. **JavaScript Logic Error (BUG):** `sw.js:263`
   - **Issue:** Attempting to use return value of `doBackgroundSync` function that doesn't return anything
   - **SonarCloud Rule:** javascript:S3699
   - **Learning:** Always verify function return types before using their results

2. **HTML Accessibility (CODE_SMELL):** `index.html:124, 129, 138`
   - **Issue:** Form labels not associated with controls
   - **SonarCloud Rule:** Web:S6853
   - **Learning:** Check HTML accessibility requirements systematically

3. **Exception Handling (CODE_SMELL):** `sw.js:101-110`
   - **Issue:** Empty catch block without proper error handling
   - **SonarCloud Rule:** javascript:S2486
   - **Learning:** Ensure all exception handling blocks have meaningful content

4. **Code Optimization (CODE_SMELL):** `index.html:29`
   - **Issue:** Unnecessary escape character in HTML
   - **SonarCloud Rule:** javascript:S6535
   - **Learning:** Review string escape sequences for necessity

**Quality Gate Result:** 🔴 FAILED (Reliability Rating: 3/5)

---

## Review Guidelines

### Phase 4.5 Enhanced Checklist

When performing code reviews, systematically check:

1. **Function Return Values:** Verify all function calls handle return values appropriately
2. **HTML Accessibility:** Ensure all form elements have proper label associations
3. **Exception Handling:** Check that all try-catch blocks contain meaningful error handling
4. **Code Optimization:** Review for unnecessary escape characters and redundant code
5. **JavaScript Logic:** Validate async function usage and promise handling
6. **Web Standards:** Verify compliance with HTML/CSS accessibility guidelines

### Update Process

This knowledge base is updated automatically when:
- SonarCloud detects issues missed by Claude's self-review
- New patterns of Claude review failures are identified
- Quality gate failures occur in Phase 6 of BOC-46 workflow

---

*Last Updated: 2025-09-10 (BOC-48 SonarCloud Integration)*