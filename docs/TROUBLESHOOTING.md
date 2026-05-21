# Troubleshooting Guide

## Common Issues & Solutions

## Screenshot Placeholders for Faster Support

When reporting issues, attach these screenshots (redacted) so root cause can be identified quickly:

- troubleshoot-auth-signin-error.png (guest sign-in error screen)
- troubleshoot-entra-guest-user-status.png (Entra guest user status page)
- troubleshoot-sharepoint-access-denied.png (SharePoint access denied page)
- troubleshoot-copilot-auth-settings.png (Copilot Studio authentication settings)
- troubleshoot-entra-api-permissions.png (Entra API permissions with consent state)

Store images in the `screenshots` folder and redact sensitive values before sharing.

---

## Category 1: Authentication Issues

### Issue 1.1: Guest Cannot Sign In

**Symptom**: Guest gets error when clicking "Sign in" button

**Possible Causes**:
- Guest not invited to Entra ID
- Guest hasn't accepted invitation
- Browser blocks pop-up
- Incorrect account configuration

**Solutions**:

1. **Verify Entra ID Invitation**
   - Go to Entra admin center → Users
   - Search for guest email
   - Verify user exists and status is "Invited" or "Member"
   - If not found, invite using ENTRA_ID_SETUP.md

2. **Verify Invitation Accepted**
   - Check email for invitation link
   - If expired, resend: Entra admin center → Users → Resend invite
   - Guest must click link and accept

3. **Check Browser Settings**
   - Ensure pop-ups allowed for copilotstudio.microsoft.com
   - Try incognito/private browsing mode
   - Try different browser

4. **Verify Account Type**
   - Entra admin center → Users → Select guest
   - User type should be "Guest"
   - Linked to correct external account

5. **Test Direct SharePoint Access**
   - Send guest direct SharePoint site link
   - If they can't access there either, issue is at SharePoint level
   - Review SHAREPOINT_SETUP.md

**Resolution Steps**:
```
1. Re-invite guest in Entra ID
2. Wait for email (5-10 minutes)
3. Guest clicks acceptance link
4. Return to agent and try signing in
5. Test with different browser if still failing
```

---

### Issue 1.2: Internal User Cannot Authenticate

**Symptom**: Internal user signs in but still cannot see documents

**Possible Causes**:
- Not in SPO_Internal_Users group
- Permissions not propagated yet
- Browser cache issue
- Entra ID group policy

**Solutions**:

1. **Verify Group Membership**
   ```
   Entra admin center → Groups → SPO_All_Users
   Expand and check that user is member (direct or via nested group)
   ```

2. **Verify SharePoint Permissions**
   ```
   SharePoint → Library → Settings → Permissions
   Verify SPO_All_Users group listed with "Read" or "Edit"
   ```

3. **Clear Browser Cache**
   - Press Ctrl+Shift+Delete
   - Clear "All time" cache
   - Refresh agent page

4. **Check Group Propagation**
   - Groups can take 15-30 minutes to sync
   - Wait and retry
   - Or sign out/in to refresh token

5. **Verify Tenant Configuration**
   - Ensure Entra ID and SharePoint are in the same tenant
   - Check tenant ID in both services

**Resolution Steps**:
```
1. Add user to SPO_All_Users group
2. Wait 15-30 minutes for sync
3. Clear browser cache
4. Sign out and back in
5. Test searching for document
```

---

### Issue 1.3: Session Expires Too Quickly

**Symptom**: Users are logged out after just 1-2 hours

**Possible Causes**:
- Session timeout set too low
- Entra ID conditional access policy
- Token expiration issue

**Solutions**:

1. **Check Session Timeout Setting**
   ```
   Copilot Studio → Agent Settings → Authentication
   Session timeout: Should be 8 hours or more
   ```

2. **Review Conditional Access Policies**
   ```
   Entra admin center → Protection → Conditional Access
   Check policies - look for "Sign-in frequency"
   If set to "Every time", user forced to re-auth
   ```

3. **Check Token Expiration**
   - If using manual auth, tokens refresh automatically
   - If using Entra ID, check app registration lifetime settings

**Resolution Steps**:
```
1. Increase session timeout to 8+ hours
2. Review/adjust conditional access policies
3. Test with new session
4. Increase timeout further if still happening
```

---

## Category 2: Document Access Issues

### Issue 2.1: Guest Sees "Access Denied" on Documents

**Symptom**: Guest can search but gets error when opening document

**Possible Causes**:
- Document not shared with guest
- Guest not in correct SharePoint group
- Document-level permissions restrict access

**Solutions**:

1. **Verify Library Permissions**
   ```
   SharePoint → Knowledge Base Library → Settings
   Verify SPO_All_Users or SPO_Guest_Users has Read permission
   ```

2. **Verify Document Permissions**
   ```
   Select document → Details → Manage Access
   Verify guest's group is listed with Read permission
   If only SPO_Internal_Users, guest won't see
   ```

3. **Verify Group Membership**
   ```
   Entra admin center → Groups → SPO_All_Users → Members
   Check guest is in this group (directly or via SPO_Guest_Users)
   ```

4. **Check SharePoint Sharing Settings**
   ```
   SharePoint Site → Settings → Sharing Settings
   Ensure "New and existing guests" is selected
   Not "Existing guests only"
   ```

5. **Test Direct Access**
   - Send guest SharePoint link directly
   - If they can't access there, it's SharePoint issue
   - If they can, it's Copilot Studio integration issue

**Resolution Steps**:
```
1. Add document to SPO_All_Users permissions
2. Wait 15-30 minutes for sync
3. Verify guest in SPO_All_Users group
4. Clear SharePoint cache (sign out/in)
5. Test again in Copilot Studio
```

---

### Issue 2.2: Internal User Cannot See Documents

**Symptom**: Internal user signs in but searches return no documents

**Possible Causes**:
- Not in SPO_Internal_Users group
- Library not indexed for search
- Knowledge source not connected
- Documents not uploaded

**Solutions**:

1. **Verify User in Group**
   ```
   Entra admin center → Groups → SPO_All_Users → Members
   Verify user is member
   ```

2. **Verify Library Connected**
   ```
   Copilot Studio → Agent → Knowledge
   Verify SharePoint library is listed and enabled
   ```

3. **Verify Documents Uploaded**
   ```
   SharePoint → Knowledge Base Library
   Verify documents are visible
   Check document count
   ```

4. **Check Search Indexing**
   ```
   Wait 24 hours from document upload
   Search indexing can take time
   Try searching by exact file name
   ```

5. **Verify Permissions**
   ```
   SharePoint → Library Settings → Permissions
   Verify SPO_All_Users has Read or Edit
   ```

**Resolution Steps**:
```
1. Verify user in groups
2. Verify documents exist in SharePoint
3. Wait 24 hours for indexing if recently added
4. Test with exact file name
5. Clear cache and try again
```

---

### Issue 2.3: Guest Sees Internal-Only Documents

**Symptom**: Guest can search for and access internal-only docs (security issue)

**Possible Causes**:
- Document not properly restricted
- Guest in SPO_Internal_Users group (shouldn't be)
- Library-level permissions override document permissions
- Propagation delay

**Solutions**:

1. **Remove Guest from Internal Group**
   ```
   Entra admin center → Groups → SPO_Internal_Users → Members
   Remove guest (they should only be in SPO_Guest_Users)
   ```

2. **Verify Document Permissions**
   ```
   SharePoint → Document → Details → Manage Access
   Remove SPO_All_Users if present
   Add only SPO_Internal_Users
   ```

3. **Check Library Permissions**
   ```
   Library level should be SPO_All_Users (read)
   Document level can be more restrictive (SPO_Internal_Users only)
   ```

4. **Wait for Propagation**
   ```
   Permission changes take 15-30 minutes
   Guest may see cached results temporarily
   ```

5. **Force Reindex**
   ```
   SharePoint Admin → Search Settings
   Consider reindexing if critical
   ```

**Resolution Steps**:
```
1. Remove guest from internal groups immediately
2. Update document permissions to SPO_Internal_Users only
3. Wait 30 minutes
4. Clear all caches (browser, SharePoint, agent)
5. Test with fresh browser session
6. Escalate if still showing
```

---

## Category 3: Search Issues

### Issue 3.1: Search Returns No Results

**Symptom**: Documents exist but search finds nothing

**Possible Causes**:
- Documents not indexed
- Search indexed outdated content
- User doesn't have permission (appears as no results)
- Typo in search term

**Solutions**:

1. **Verify Documents Exist**
   ```
   Navigate to SharePoint Library directly
   Confirm documents are there
   Confirm accessible to your user
   ```

2. **Wait for Indexing**
   ```
   24 hours from document upload for full indexing
   Can search by file name first
   Then by content after indexing complete
   ```

3. **Verify Permissions**
   ```
   Try searching from different user
   If internal user can find but guest can't,
   likely a permissions issue (not search)
   ```

4. **Try Exact File Name**
   ```
   Search for exact file name like "HR_Handbook.docx"
   If this works, wait for content indexing
   ```

5. **Check Knowledge Source Configuration**
   ```
   Copilot Studio → Knowledge → Verify connected
   Verify correct library selected
   Verify "Enabled" toggle is ON
   ```

**Resolution Steps**:
```
1. Navigate to SharePoint and confirm doc exists
2. Search by exact file name
3. Wait 24 hours for content indexing
4. Test again with partial content search
5. Check other user types (internal vs guest)
```

---

### Issue 3.2: Search Returns Wrong/Irrelevant Results

**Symptom**: Searches return documents not matching query

**Possible Causes**:
- Poor document organization
- Missing metadata
- Search index includes similar terms
- Confidence threshold too low

**Solutions**:

1. **Improve Document Organization**
   ```
   Group similar documents in folders
   Use clear, consistent naming
   Add version numbers (v1.0, v2.0)
   ```

2. **Add Metadata**
   ```
   SharePoint → Each Document → Details
   Add meaningful keywords and tags
   Add department/category metadata
   ```

3. **Review Document Content**
   ```
   Ensure documents contain searchable content
   Not just images/graphics
   Check file format is searchable (.docx, .pdf, etc)
   ```

4. **Adjust Confidence Threshold**
   ```
   Copilot Studio → Knowledge → Settings
   Increase to "High" to filter irrelevant results
   Or decrease to "Medium" for more results
   ```

5. **Review Topic Logic**
   ```
   Check how agent is searching
   May need to improve search queries in topics
   ```

**Resolution Steps**:
```
1. Rename documents with clear names
2. Add keywords and metadata to documents
3. Increase confidence threshold to High
4. Test with new searches
5. Adjust as needed
```

---

## Category 4: Configuration Issues

### Issue 4.1: Agent Won't Load

**Symptom**: Agent page blank or shows error

**Possible Causes**:
- Network connectivity
- Browser compatibility
- Agent not published
- Tenant issues

**Solutions**:

1. **Check Network**
   ```
   Test internet connection
   Try different website
   Check if Microsoft services are up (status.microsoft.com)
   ```

2. **Try Different Browser**
   ```
   Edge, Chrome, Safari all supported
   Clear cache first: Ctrl+Shift+Delete
   Try incognito/private mode
   ```

3. **Verify Agent Published**
   ```
   Copilot Studio → Agent → Overview
   Check status shows "Published"
   If not, click Publish
   ```

4. **Check Tenant Status**
   ```
   Try accessing other M365 apps
   If Microsoft 365 down, wait for recovery
   ```

5. **Check Browser Console**
   ```
   Press F12 to open developer tools
   Check Console tab for errors
   Note any error codes
   ```

**Resolution Steps**:
```
1. Check internet connectivity
2. Clear browser cache and cookies
3. Try different browser
4. Verify agent is published
5. Check if Microsoft services are up
```

---

### Issue 4.2: Knowledge Source Not Connecting

**Symptom**: "Cannot connect to knowledge source" error

**Possible Causes**:
- Wrong SharePoint URL
- Insufficient permissions
- Network/firewall issue
- SharePoint site inaccessible

**Solutions**:

1. **Verify SharePoint URL**
   ```
   Should be: https://tenant.sharepoint.com/sites/sitename
   Not: /docs, /SiteAssets, or other subfolder
   Test URL by opening in browser
   ```

2. **Verify Agent Permissions**
   ```
   The app registering with SharePoint needs read access
   SharePoint → Site → Settings → User permissions
   Verify agent app is listed
   ```

3. **Test Direct Access**
   ```
   Open SharePoint site in browser
   If you can't access, agent can't either
   Log in with same account used for agent
   ```

4. **Check Firewall/Network**
   ```
   If on corporate network, check for restrictions
   Try from different network if possible
   Check proxy settings
   ```

5. **Re-authorize Connection**
   ```
   Copilot Studio → Knowledge → Remove source
   Re-add knowledge source
   Authorize again
   ```

**Resolution Steps**:
```
1. Verify correct SharePoint URL
2. Test accessing SharePoint directly
3. Re-authorize the connection
4. Wait 5 minutes for connection to establish
5. Try searching again
```

---

### Issue 4.3: Wrong Authentication Method Configured

**Symptom**: Agent requires wrong type of authentication

**Possible Causes**:
- Manual auth configured but Entra ID auth expected
- Entra ID auth configured but manual expected
- Misconfiguration in settings

**Solutions**:

1. **Check Current Configuration**
   ```
   Copilot Studio → Settings → Authentication
   Note which method is enabled
   ```

2. **Understand Which to Use**
   ```
   Manual: Users sign in when needed (more flexible)
   Entra ID auth: Automatic based on Entra ID (more seamless)
   For B2B, Manual usually better
   ```

3. **Switch Authentication Methods**
   ```
   Settings → Authentication
   Disable current method
   Enable desired method
   Configure required fields
   Save
   ```

4. **Test Each User Type**
   ```
   After switching, test as internal user
   Then test as guest user
   Verify expected behavior
   ```

**Resolution Steps**:
```
1. Disable current auth method
2. Enable correct auth method
3. Configure if needed
4. Save settings
5. Test with each user type
```

---

## Category 5: Performance Issues

### Issue 5.1: Searches Are Very Slow

**Symptom**: Searches take 10+ seconds to return results

**Possible Causes**:
- Network latency
- SharePoint performance degraded
- Large document set
- Indexing still in progress

**Solutions**:

1. **Check Network Performance**
   ```
   Run speed test: speedtest.net
   Acceptable: >10 Mbps
   Try from different location/network
   ```

2. **Test SharePoint Performance**
   ```
   Navigate to SharePoint site directly
   Is it also slow there?
   If yes, SharePoint needs optimization
   ```

3. **Check Document Count**
   ```
   If >5,000 documents, can be slow
   Consider:
   - Multiple libraries by category
   - Archive old documents
   - Optimize metadata
   ```

4. **Verify Indexing Complete**
   ```
   New documents need 24 hours
   During indexing, searches slower
   Recent uploads can cause slowness
   ```

5. **Optimize Copilot Settings**
   ```
   Reduce result limit (5 instead of 10)
   Increase confidence threshold
   Disable unnecessary features
   ```

**Resolution Steps**:
```
1. Test network speed
2. Test SharePoint directly
3. Reduce result limit in knowledge settings
4. Archive old/unused documents
5. Wait for indexing to complete
```

---

## Category 6: Permissions Issues

### Issue 6.1: Inconsistent Access Between Users

**Symptom**: Same document visible to some users but not others

**Possible Causes**:
- Inconsistent group membership
- Partially propagated permissions
- Different library vs document permissions
- Caching issues

**Solutions**:

1. **Verify All Users in Correct Groups**
   ```
   Entra admin center → Groups
   SPO_All_Users: check membership
   SPO_Internal_Users: check internal users
   SPO_Guest_Users: check guests
   Verify no duplicates (user in multiple guest groups)
   ```

2. **Verify Permission Consistency**
   ```
   SharePoint → Library Settings → Permissions
   All users should have same library permissions
   Overrides should be rare and intentional
   Document-level permissions should be consistent
   ```

3. **Clear Caches**
   ```
   Browser cache: Ctrl+Shift+Delete
   SharePoint: Sign out/in
   Agent: Reload page
   ```

4. **Wait for Propagation**
   ```
   Permission changes take 15-30 minutes
   If just changed, wait before re-testing
   ```

5. **Check Entra ID Sync**
   ```
   If hybrid or synced from on-premises:
   On-premises changes take time to sync
   May need manual sync trigger
   ```

**Resolution Steps**:
```
1. Audit all group memberships
2. Ensure consistent library permissions
3. Clear all caches
4. Wait 30 minutes
5. Re-test with each user type
6. If still wrong, check Entra ID sync status
```

---

## General Troubleshooting Steps

### Before Reporting an Issue:

1. **Clear All Caches**
   ```
   Browser: Ctrl+Shift+Delete (clear all time)
   SharePoint: Sign out, clear cookies, sign back in
   Agent: Reload page (F5)
   ```

2. **Try Different Browser**
   ```
   Edge, Chrome, or Safari
   Helps isolate browser-specific issues
   ```

3. **Try Incognito/Private Mode**
   ```
   Disables extensions
   Uses fresh session
   Helps identify cache/extension issues
   ```

4. **Test as Different User Type**
   ```
   If internal user has issue, try guest
   If guest has issue, try internal
   Narrows down cause
   ```

5. **Wait 30 Minutes**
   ```
   Many issues resolve with time (propagation, indexing)
   Check back after waiting
   ```

### If Issue Persists:

1. **Document the Issue**
   - Exact error message
   - Steps to reproduce
   - User type (internal/guest)
   - Browser and OS
   - Screenshot if possible

2. **Check Logs**
   ```
   Copilot Studio → Analytics (if available)
   SharePoint → Audit Logs
   Azure AD → Audit logs
   ```

3. **Contact Support**
   - Include documentation above
   - Provide relevant logs
   - Include diagnostic info
   - Note when issue started

---

## Quick Reference

| Issue | Quick Fix |
|-------|-----------|
| Can't sign in | Verify Azure AD group membership |
| Access denied on docs | Check SharePoint permissions |
| No search results | Wait 24 hrs for indexing |
| Slow searches | Reduce result limit |
| Guest sees internal docs | Update document permissions |
| Agent won't load | Clear cache, try different browser |
| Wrong auth type | Change in agent settings |

---

## Related Documentation

- [Main Guide](../README.md)
- [Testing Guide](TESTING_GUIDE.md)
- [Azure AD Setup](AZURE_AD_SETUP.md)
- [SharePoint Setup](SHAREPOINT_SETUP.md)
- [Copilot Studio Setup](COPILOT_STUDIO_SETUP.md)
