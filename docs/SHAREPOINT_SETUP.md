# SharePoint Setup (Exact Working Path)

This document captures the SharePoint configuration used for grounded responses in Copilot Studio.

## Objective

Prepare SharePoint as knowledge source so both internal and guest users can retrieve only content they are allowed to access.

## Step 1: Select Site and Library

- Identify the SharePoint site to ground on.
- Confirm the target document library/folders.

## Step 2: Ensure User Access Exists Before Runtime

Grant access in SharePoint to:

- Internal users
- B2B guest users

Permission level can be View/Read/Edit based on business need.

Important:

Copilot Studio grounding does not bypass SharePoint security. If a user has no SharePoint access, that user will not retrieve that content.

## Step 3: Validate Guest Access Directly in SharePoint

Before testing the agent:

1. Sign in as guest user.
2. Open the target SharePoint site.
3. Confirm guest can open intended documents.

If this fails, fix SharePoint sharing/permissions first.

## Step 4: Keep Library Content Ready for Grounding

- Keep source files current.
- Prefer clear file naming.
- Remove obsolete content that should not be retrieved.

## Validation Checklist

- Guest exists in tenant and accepted invitation.
- Guest can open target SharePoint content directly.
- Internal user can open target SharePoint content directly.
- Target library has only intended knowledge content.

## Runtime Behavior

Expected outcomes:

- User has SharePoint access: grounded answer can include that content.
- User has no SharePoint access: content is not returned.

This is the core delegated security behavior for this implementation.
