# AI Review Knowledge Base

This document accumulates code review knowledge learned by the AI.

All items are used as a checklist in Phase 4.5 of the automated workflow.

---

## JavaScript / TypeScript

### Reliability

- [ ] Does the code attempt to use the return value of a function that does not return anything (e.g., an `async` function without a `return` statement)?
- [ ] Is the `catch` block of a `try-catch` statement empty? It should at least log the error or re-throw it.
- [ ] Are `import type` statements properly handled by ESLint TypeScript parser?
- [ ] Are environment-specific global variables (browser, Service Worker, Node.js) properly defined in ESLint config?

### Maintainability

- [ ] Are there any unnecessary escape characters in strings or regular expressions?
- [ ] Is the ESLint configuration using modern patterns like `globals` package for environment management?
- [ ] Are TypeScript files configured with appropriate parser and plugins (@typescript-eslint/parser, @typescript-eslint/eslint-plugin)?

## HTML

### Accessibility

- [ ] Does every form control (`<input>`, `<textarea>`, `<select>`) have an associated `<label>` tag?

---

## Learning History

### BOC-48 (2025-09-10): Multi-Phase Learning Results

#### Phase 1-2: ESLint Configuration Issues
**Initial Problem:** 32 ESLint problems (1 error, 31 warnings) causing CI/CD failure
**Root Cause:** Inadequate TypeScript and environment-specific configuration

**Missed Issues by Claude Initial Review:**
1. **TypeScript Parser Configuration:** `capacitor.config.ts` 
   - **Issue:** `import type` syntax not recognized by ESLint
   - **Learning:** Always verify @typescript-eslint/parser is properly configured in eslint.config.js
   - **Solution:** Add TypeScript-specific configuration block with proper parser

2. **Environment Global Variables:** `script.js` and `sw.js`
   - **Issue:** 30+ no-undef errors for browser/Service Worker APIs
   - **Learning:** Use `globals` package for environment-specific variable management
   - **Solution:** Implement ...globals.browser, ...globals.worker, ...globals.node patterns

### BOC-46 (2025-09-11): Phase 4.5 vs SonarCloud Gap Analysis

#### Configuration Value Validation Gap
**Phase 4.5 Review:** Focused on implementation logic, error handling, performance
**SonarCloud Detection:** Configuration file content validation (capacitor.config.ts webDir: './')
**Learning Gap:** Configuration file existence ≠ configuration value validity
- **Issue:** `webDir: './'` marked as invalid value by Capacitor CLI
- **Claude Miss Pattern:** Checked file existence but not content validity
- **New Learning:** Configuration files require both existence AND value validation

#### Production Code Quality Standards Gap  
**Phase 4.5 Review:** Accepted console.log/confirm as acceptable for development
**SonarCloud Detection:** 9 code quality issues (7 console.log + 2 confirm statements)
- **Issue:** Development debugging code flagged for production environment
- **Claude Miss Pattern:** Applied development standards to production-bound code
- **New Learning:** Distinguish development vs production code quality criteria

#### Runtime Dependency Chain Gap
**Phase 4.5 Review:** Validated command execution success
**SonarCloud Detection:** Missing capacitor.plugins.json file during platform initialization
- **Issue:** Command succeeded but didn't generate expected dependency files
- **Claude Miss Pattern:** Verified command exit code but not output completeness  
- **New Learning:** Command success requires both exit code AND expected artifact validation

#### Phase 6: SonarCloud Detection Results
**Advanced Static Analysis Findings:**

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

## SonarQube Integration Knowledge (BOC-52)

### Static Analysis Rule Mapping

#### JavaScript/TypeScript Core Rules
- **javascript:S3699** → ESLint: no-void-return (戻り値なし関数の戻り値使用)
  - [ ] Check for usage of return values from functions that don't return anything
  - [ ] Verify async functions have proper return statements if their values are used
- **javascript:S2486** → ESLint: no-empty-catch (空のcatchブロック)
  - [ ] Ensure catch blocks contain error handling logic (logging, re-throwing, or recovery)
  - [ ] Avoid completely empty catch blocks without comments explaining why
- **javascript:S6535** → ESLint: no-useless-escape (不要エスケープ文字)
  - [ ] Remove unnecessary escape characters in strings and regex patterns
  - [ ] Validate that escape sequences are actually needed for the context

#### Security Pattern Recognition
- **Taint Source Detection**: user input sources requiring validation
  - [ ] Check req.body, location.hash, URL params for proper sanitization
  - [ ] Verify user inputs are validated before processing
- **Dangerous Sink Prevention**: high-risk operations requiring careful input handling
  - [ ] Audit usage of eval(), innerHTML, SQL queries, file operations
  - [ ] Ensure proper sanitization before dangerous operations

### AST Analysis Guidelines

#### Variable Scope Tracking
- [ ] Verify variable declaration position matches usage context
- [ ] Detect unused variables and unreachable code patterns
- [ ] Check for unintended variable reassignment in outer scopes
- [ ] Validate variable lifetime and scope boundaries

#### Data Flow Analysis  
- [ ] Verify function argument → return value type consistency
- [ ] Validate asynchronous processing Promise chain integrity
- [ ] Track error propagation paths through function calls
- [ ] Ensure data transformations maintain expected types

### Security Vulnerability Pattern Library

#### XSS Prevention Patterns
❌ **Dangerous**: `element.innerHTML = userInput`  
✅ **Safe**: `element.textContent = userInput` or `DOMPurify.sanitize(userInput)`
- [ ] Check for direct innerHTML assignments with user data
- [ ] Verify HTML sanitization is applied to user-generated content

#### SQL Injection Prevention  
❌ **Dangerous**: `query = "SELECT * FROM users WHERE id = " + userId`  
✅ **Safe**: `query = "SELECT * FROM users WHERE id = ?" with parameterized values [userId]`
- [ ] Audit string concatenation in database queries
- [ ] Ensure parameterized queries or prepared statements are used

#### Path Traversal Prevention
❌ **Dangerous**: `fs.readFile(userPath)`  
✅ **Safe**: `fs.readFile(path.resolve(basePath, path.normalize(userPath)))`
- [ ] Check file system operations with user-provided paths
- [ ] Verify path normalization and base directory restrictions

### Code Structure Analysis Checklist

#### Control Flow Complexity
- [ ] Calculate and validate cyclomatic complexity stays within reasonable bounds
- [ ] Identify deeply nested conditional statements requiring refactoring
- [ ] Check for unreachable code after return/break/continue statements

#### Function Analysis Patterns
- [ ] Verify function signatures match their actual usage patterns
- [ ] Check parameter validation at function entry points
- [ ] Ensure proper error handling and return value management

---

## Review Guidelines

### Phase 4.5 Enhanced Checklist

When performing code reviews, systematically check:

#### Pre-Review Configuration Validation
1. **ESLint Setup:** Verify TypeScript parser (@typescript-eslint/parser) is configured for .ts files
2. **Environment Globals:** Confirm browser/Service Worker/Node.js globals are properly defined using `globals` package
3. **Parser Coverage:** Ensure all file types (.js, .ts, .html) have appropriate parsing configuration
4. **Configuration Value Validation:** Check configuration file content validity, not just existence (e.g., webDir paths, API endpoints)

#### Code Quality Analysis  
5. **Function Return Values:** Verify all function calls handle return values appropriately
6. **HTML Accessibility:** Ensure all form elements have proper label associations
7. **Exception Handling:** Check that all try-catch blocks contain meaningful error handling
8. **Code Optimization:** Review for unnecessary escape characters and redundant code
9. **JavaScript Logic:** Validate async function usage and promise handling
10. **Web Standards:** Verify compliance with HTML/CSS accessibility guidelines
11. **Production Code Quality:** Distinguish development vs production standards (console.log, confirm, alert usage)

#### Advanced Patterns (SonarCloud Integration)
12. **Function Call Analysis:** Check for void functions being used in assignment operations
13. **HTML Form Semantics:** Validate all form controls have associated labels with proper `for` attributes
14. **Error Handling Completeness:** Ensure catch blocks have logging, re-throwing, or meaningful error handling
15. **String/RegExp Optimization:** Remove unnecessary escape characters in strings and regex patterns
16. **Command Output Validation:** Verify command success includes both exit code AND expected artifact generation

### Update Process

This knowledge base is updated automatically when:
- SonarCloud detects issues missed by Claude's self-review
- New patterns of Claude review failures are identified  
- Quality gate failures occur in Phase 6 of BOC-46 workflow
- ESLint configuration issues are discovered and resolved
- Multi-phase learning patterns emerge from BOC workflow execution

### Integration with BOC-46 Workflow

**Phase 2.5 Analysis:** Use this knowledge base to enhance Sequential Thinking analysis
**Phase 4.5 Review:** Apply all checklist items systematically before implementation
**Phase 6 Learning:** Add new findings to knowledge base for future improvement

---

## ESLint Configuration Standards

### TypeScript Integration Pattern
```javascript
// Required in eslint.config.js for TypeScript support
{
  files: ["**/*.ts", "**/*.tsx"],
  languageOptions: {
    parser: "@typescript-eslint/parser",
    parserOptions: {
      ecmaVersion: "latest",
      sourceType: "module"
    }
  },
  plugins: {
    "@typescript-eslint": tsPlugin
  },
  rules: {
    "no-undef": "off", // TypeScript handles this
    "@typescript-eslint/no-unused-vars": ["error", { "argsIgnorePattern": "^_" }]
  }
}
```

### Environment Globals Pattern
```javascript
// Use globals package for environment management
import globals from "globals";

export default [
  {
    files: ["**/*.js"],
    languageOptions: {
      globals: {
        ...globals.browser, // Browser APIs
        // Add specific globals as needed
      }
    }
  },
  {
    files: ["sw.js", "**/*.sw.js"],
    languageOptions: {
      globals: {
        ...globals.worker, // Service Worker APIs
        self: "readonly",
        caches: "readonly",
        clients: "readonly"
      }
    }
  }
];
```

---

*Last Updated: 2025-09-12 (BOC-52 SonarQube Integration Knowledge - Phase 1 Implementation)*