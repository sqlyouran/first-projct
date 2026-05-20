## 1. Navigation Labels

- [x] 1.1 Update `Layout.tsx` nav labels: "首页" → "Home", "交流" → "Community"
- [x] 1.2 Verify header renders correctly with English labels

## 2. HTML Lang Attribute

- [x] 2.1 Update `frontend/index.html` `<html lang>` attribute to `"en"`

## 3. Chinese Character Audit

- [x] 3.1 Run `grep -rP '[\x{4e00}-\x{9fff}]' frontend/src/` to find all Chinese characters in source
- [x] 3.2 Evaluate each match: data reference (keep) vs UI label (convert to English)
- [x] 3.3 Convert any remaining Chinese UI labels to English

## 4. Verification

- [x] 4.1 Run `npm run build` — zero errors
- [x] 4.2 Re-run Chinese grep to confirm no UI-label Chinese remains in source
