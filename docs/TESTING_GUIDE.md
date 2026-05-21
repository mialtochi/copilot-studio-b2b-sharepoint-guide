# Testing & Validation Guide

## Overview

This guide provides detailed test cases and validation procedures to ensure your B2B SharePoint Copilot agent works correctly for both internal and guest users.

---

## Pre-Test Checklist

Before starting tests, verify:

- [ ] Entra ID groups created and users assigned
- [ ] SharePoint permissions configured
- [ ] Copilot Studio agent published
- [ ] Authentication configured
- [ ] Test users available:
  - 1-2 internal users
  - 1-2 guest users
- [ ] Documents uploaded to SharePoint
- [ ] Search indexing completed (24 hours)

---

## Test Scenario 1: Internal User Access

### Setup
- User: Internal employee (alice@company.com)
- Status: Signed into corporate environment
- Expected: Full access to all shared documents

### Test Cases

#### TC1.1: Agent Loads Successfully
1. Navigate to agent
2. **Verify**: Agent interface loads
3. **Verify**: Welcome message displays
4. **Expected**: "Sign in" or "Connected" status visible

#### TC1.2: Search Knowledge Base - Successful
1. Ask: "What's the vacation policy?"
2. **Verify**: Results display within 3 seconds
3. **Verify**: At least 1 relevant document appears
4. **Expected**: Can see HR Handbook or Benefits document

#### TC1.3: Access Document Link
1. From search results, click document link
2. **Verify**: SharePoint document opens
3. **Verify**: Can view full content
4. **Expected**: No "Access Denied" errors

#### TC1.4: Multiple Search Queries
1. Ask 5 different questions (see table below)
2. **Verify**: Each returns relevant results
3. **Verify**: No "403 Forbidden" errors
4. **Expected**: 80%+ relevant results

#### TC1.5: Follow-up Questions
1. Ask initial question: "Product features?"
2. Follow up: "Tell me more about security"
3. **Verify**: Agent maintains context
4. **Expected**: Follow-up results relate to context

### Test Questions (TC1.4)

| # | Question | Expected Document |
|---|----------|------------------|
| 1 | "What's our vacation policy?" | HR Handbook.docx |
| 2 | "Product capabilities" | Product Guide.pdf |
| 3 | "Onboarding process" | New Hire Checklist.xlsx |
| 4 | "Technical requirements" | System Requirements.pdf |
| 5 | "Company code of conduct" | Code of Conduct.pdf |

### Acceptance Criteria
- All 5 tests pass ✓
- All questions return relevant results
- No permission errors
- Load times < 3 seconds

---

## Test Scenario 2: Guest User Access

### Setup
- User: External guest (guest@externalcompany.com)
- Status: B2B invited and signed in
- Expected: Limited access to shared documents only

### Test Cases

#### TC2.1: Guest Can Sign In
1. Open agent
2. Attempt to search
3. See "Sign in" prompt
4. Click sign-in button
5. **Verify**: Redirected to authentication
6. **Verify**: Can authenticate successfully
7. **Expected**: Returned to agent, signed-in status shown

#### TC2.2: Guest Sees Only Shared Documents
1. Ask: "What's the vacation policy?"
2. **Verify**: Results appear
3. **Verify**: All results are marked as "shared"
4. **Expected**: No "Access Denied" on links
5. **Expected**: No internal-only documents

#### TC2.3: Guest Cannot Access Internal-Only Docs
1. Search for: "Executive Summary" (internal-only doc)
2. **Verify**: No results returned
3. **Expected**: Not shown in search results
4. **Expected**: No error message (security best practice)

#### TC2.4: Document Link Works for Guest
1. From search results (TC2.2), click link
2. **Verify**: Document opens in SharePoint
3. **Verify**: Can view content
4. **Expected**: No "Access Denied" errors

#### TC2.5: Session Timeout
1. Leave agent idle for 8+ hours
2. Ask new question
3. **Verify**: Re-authentication required
4. **Expected**: "Please sign in again" message
5. **Expected**: Can re-authenticate

### Acceptance Criteria
- All 5 tests pass ✓
- Guest authentication works
- Only shared docs visible
- Internal-only docs hidden
- Session timeout enforced

---

## Test Scenario 3: Permission Enforcement

### Setup
- Documents: Mix of public and internal-only
- Users: 1 internal + 1 guest
- Expected: Permissions correctly enforced

### Document Setup (if not already done)

| Document | Internal Access | Guest Access | Group Permission |
|----------|----------------|--------------|-----------------|
| HR Handbook.docx | ✓ Read | ✓ Read | SPO_All_Users |
| API Guide.pdf | ✓ Read | ✓ Read | SPO_All_Users |
| Executive Summary.docx | ✓ Read | ✗ None | SPO_Internal_Users |
| Budget Report.xlsx | ✓ Read | ✗ None | SPO_Internal_Users |

### Test Cases

#### TC3.1: Public Documents Visible to Both
1. Internal user searches: "API Guide"
2. **Verify**: Finds document, can access
3. Guest user searches: "API Guide"
4. **Verify**: Finds document, can access
5. **Expected**: Both see same results

#### TC3.2: Internal-Only Hidden from Guest
1. Internal user searches: "Executive Summary"
2. **Verify**: Finds and accesses document
3. Guest user searches: "Executive Summary"
4. **Verify**: Gets no results
5. **Expected**: No permission error shown

#### TC3.3: Verify SharePoint Permissions
1. Guest attempts direct SharePoint access
2. Navigate to internal-only doc
3. **Expected**: "Access Denied" from SharePoint
4. (Proves permissions are enforced at source)

#### TC3.4: Permission Change Propagation
1. Add guest to internal document
2. Wait 15 minutes
3. Guest searches for document
4. **Verify**: Now appears in results
5. **Expected**: Change effective after 15-30 mins

### Acceptance Criteria
- Public docs accessible to both users
- Internal docs hidden from guests
- SharePoint enforces permissions
- Changes propagate within 30 minutes

---

## Test Scenario 4: Authentication Methods

### Setup (if using both methods)

#### Test 4A: Manual Authentication

#### TC4A.1: Manual Auth Prompt
1. Not signed in
2. Try to search
3. **Verify**: "Please sign in" prompt appears
4. **Verify**: Sign-in button visible
5. **Expected**: Clear instructions

#### TC4A.2: Manual Auth Completes
1. Click "Sign in" button
2. **Verify**: SharePoint login page opens
3. Sign in with credentials
4. **Verify**: Redirected to agent
5. **Expected**: Successfully authenticated

#### Test 4B: Entra ID Authentication (if configured)

#### TC4B.1: Auto-auth on Start
1. Agent loads
2. **Verify**: Automatically attempts authentication
3. **Verify**: No manual sign-in required
4. **Expected**: Connected immediately

#### TC4B.2: Entra ID Error Handling
1. Revoke permissions in Entra ID
2. Reload agent
3. **Verify**: Graceful error message
4. **Expected**: Option to re-auth

### Acceptance Criteria
- Auth method works as configured
- Users can authenticate successfully
- Tokens refresh automatically
- Errors handled gracefully

---

## Test Scenario 5: Error Handling

### Test Cases

#### TC5.1: No Results Found
1. Ask: "Xyzabc company policy" (gibberish)
2. **Verify**: Graceful "no results" message
3. **Expected**: Helpful suggestion like "Try different keywords"

#### TC5.2: SharePoint Down
1. (If possible) Take SharePoint offline temporarily
2. Try to search
3. **Verify**: Timeout message appears
4. **Expected**: Message like "Knowledge base temporarily unavailable"

#### TC5.3: Permission Error
1. Document removed from guest's group
2. Search for document
3. **Verify**: Not in results
4. **Expected**: No error shown

#### TC5.4: Authentication Timeout
1. Leave agent signed in for 8+ hours (or simulate)
2. Try to access document
3. **Verify**: Re-authentication prompted
4. **Expected**: Can re-auth without error

#### TC5.5: Malformed Query
1. Ask: "!@#$%^&*()" (special characters only)
2. **Verify**: Either graceful message or normal search
3. **Expected**: No system errors/crashes

### Acceptance Criteria
- All error cases handled gracefully
- No unhandled exceptions
- User receives clear error messages
- Recovery options available

---

## Test Scenario 6: Performance Testing

### Test Cases

#### TC6.1: Search Speed
1. Ask 10 varied questions (TC1.4 list)
2. Measure response time each
3. **Expected**: Average < 3 seconds per response
4. **Acceptable**: 90% of searches < 3 sec

#### TC6.2: Large Result Sets
1. Ask broad question: "Company"
2. **Verify**: Returns multiple results
3. **Verify**: Results load within 5 seconds
4. **Expected**: Display truncated gracefully

#### TC6.3: High User Load (if possible)
1. Have 5+ users search simultaneously
2. **Verify**: System remains responsive
3. **Expected**: No degradation for any user

#### TC6.4: Concurrent Guest/Internal
1. Internal user: Searches knowledge base
2. Simultaneously: Guest user signs in and searches
3. **Verify**: Both complete successfully
4. **Expected**: No interference between users

### Acceptance Criteria
- Search responses < 3 seconds
- System handles concurrent users
- No timeouts or disconnections

---

## Test Scenario 7: Cross-Browser & Device

### Devices to Test

| Browser | OS | Platform |
|---------|----|----|
| Edge | Windows | Desktop |
| Chrome | Windows | Desktop |
| Safari | macOS | Desktop |
| Chrome | iOS | Mobile |
| Safari | iOS | Mobile |
| Chrome | Android | Mobile |

### Test Cases (per browser/device)

#### TC7.1: Agent Loads
- Load agent
- **Verify**: UI renders correctly
- **Verify**: All buttons visible

#### TC7.2: Search Functions
- Ask question
- **Verify**: Results display
- **Verify**: Responsive to taps/clicks

#### TC7.3: Sign In Works
- Test authentication flow
- **Verify**: Sign-in completes
- **Verify**: Responsive design works

#### TC7.4: Links Work
- Click document links
- **Verify**: Opens in new tab
- **Verify**: Document readable on device

### Acceptance Criteria
- Works on Edge, Chrome, Safari
- Mobile experience acceptable
- No layout issues
- Touch responsive

---

## Test Results Template

```
Test Scenario: [Name]
Date: [Date]
Tester: [Name]
Environment: [Internal/Guest/Both]

Results:
- TC1: [✓ Pass / ✗ Fail] - [Notes]
- TC2: [✓ Pass / ✗ Fail] - [Notes]
- TC3: [✓ Pass / ✗ Fail] - [Notes]

Issues Found:
1. [Issue]
   - Severity: [Critical/High/Medium/Low]
   - Steps to reproduce: [Steps]
   - Proposed fix: [Solution]

Signed Off: [Tester Name]
```

---

## UAT Sign-Off

Once all tests pass:

1. **Internal User UAT** - Approved by: [Manager]
2. **Guest User UAT** - Approved by: [Manager]
3. **Security Review** - Approved by: [Security Team]
4. **Compliance Review** - Approved by: [Compliance]
5. **Production Ready** - Approved by: [Executive]

---

## Post-Deployment Monitoring

### Week 1
- Daily check of agent functionality
- Monitor error logs
- Collect user feedback

### Week 2-4
- Weekly check
- Gather usage metrics
- Address issues

### Ongoing
- Monthly performance review
- Update documentation
- Plan improvements

---

## Related Documentation

- [Main Guide](../README.md)
- [Copilot Studio Setup](COPILOT_STUDIO_SETUP.md)
- [Troubleshooting](TROUBLESHOOTING.md)
