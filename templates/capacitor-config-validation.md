# Capacitor Configuration Validation Template

BOC-46学習結果から生成された設定検証テンプレート

## 🚨 Critical Configuration Values

### webDir Setting
```typescript
// ❌ Invalid - causes "not a valid value" error
const config: CapacitorConfig = {
  webDir: './'  // This fails Capacitor CLI validation
};

// ✅ Valid options
const config: CapacitorConfig = {
  webDir: 'dist',     // Build output directory
  webDir: 'build',    // Alternative build directory  
  webDir: 'public'    // Static assets directory
};
```

### Platform-Specific Settings
```typescript
const config: CapacitorConfig = {
  android: {
    allowMixedContent: false,  // Security: Prevent HTTP in HTTPS
    // Validate boolean values, not strings
  },
  ios: {
    // iOS-specific validations
  }
};
```

## 🔍 Phase 4.5 Checklist Integration

### Item 4: Configuration Value Validation
```bash
# Check existence (existing)
if [ -f "capacitor.config.ts" ]; then
  echo "✅ Configuration file exists"
else
  echo "❌ Configuration file missing"
  exit 1
fi

# NEW: Check content validity (BOC-46 learning)
if grep -q 'webDir.*"\\./"' capacitor.config.ts; then
  echo "❌ Invalid webDir value detected: './' "
  echo "Recommended: webDir: 'dist'"
  exit 1
fi

if grep -q 'webDir.*"dist"' capacitor.config.ts; then
  echo "✅ Valid webDir configuration detected"
fi
```

### Command Validation Enhancement
```bash
# OLD: Only exit code check
npx cap add android || exit 1

# NEW: Exit code + expected artifacts (BOC-46 learning)
npx cap add android || {
  echo "::error::npx cap add android failed with exit code $?"
  exit 1
}

# Verify expected artifacts were created
if [ ! -f "android/app/src/main/assets/capacitor.plugins.json" ]; then
  echo "::error::capacitor.plugins.json was not generated"
  echo "Platform initialization incomplete"
  exit 1
fi
```

## 📋 Common Invalid Values

| Setting | Invalid Example | Valid Example | Error Message |
|---------|----------------|---------------|---------------|
| webDir | `"./"` | `"dist"` | "not a valid value for webDir" |
| appId | `""` | `"com.company.app"` | "appId cannot be empty" |
| plugins | `undefined` | `{}` | "plugins must be object" |

## 🎯 Integration Points

- **Phase 2.5**: Include configuration validation in Sequential Thinking
- **Phase 4.5**: Apply as checklist item 4 
- **CI/CD**: Add config validation step before Capacitor commands

*Generated from BOC-46 Phase 6 learning: 2025-09-11*