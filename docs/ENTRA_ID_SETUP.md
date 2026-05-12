# Entra ID Setup (Exact Working Path)

This document captures the exact Entra configuration used for Copilot Studio manual authentication with federated credentials.

## Scope

- Single-tenant app registration
- Federated credential from Copilot Studio values
- Delegated Graph permissions for user-context SharePoint grounding

## Preconditions

- Guest users were invited to tenant and accepted invitation.
- SharePoint permissions were already granted to internal and guest users.

## Step 1: Create App Registration

Path:

Entra ID → App registrations → New registration

Configuration:

- Name: CopilotStudio-Auth-App (or your naming standard)
- Supported account types: Accounts in this organizational directory only (single tenant)

Create app.

## Step 2: Configure Redirect URI

Path:

Authentication → Add platform → Web

Add redirect URI:

https://token.botframework.com/.auth/web/redirect

Enable tokens:

- Access tokens
- ID tokens

Save.

## Step 3: Add Federated Credential

Path:

Certificates and secrets → Federated credentials → Add credential

Configuration:

- Scenario: Other issuer
- Issuer: value copied from Copilot Studio
- Subject/value: value copied from Copilot Studio
- Name: Copilot-FIC (or your naming standard)

Save.

## Step 4: Add Microsoft Graph Delegated Permissions

Path:

API permissions → Add permission → Microsoft Graph → Delegated permissions

Add:

- openid
- profile
- User.Read
- Sites.Read.All
- Files.Read.All

Then click Grant admin consent.

Important:

Without admin consent, SharePoint grounding can fail.

## Step 5: Capture Client ID

From app overview, copy Application (client) ID.

Use this value in Copilot Studio authentication settings.

## Validation

- App registration exists and is single-tenant.
- Redirect URI matches exactly.
- Federated credential issuer and subject match Copilot Studio output.
- Delegated Graph permissions are present.
- Admin consent is granted.

## Notes

This implementation used delegated access model.

The agent acts on behalf of the signed-in user, and SharePoint permission boundaries are preserved at runtime.
