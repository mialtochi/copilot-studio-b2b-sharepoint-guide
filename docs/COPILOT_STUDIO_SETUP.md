# Copilot Studio Setup (Exact Working Path)

This document captures the exact Copilot Studio configuration used in a working implementation for:

- Internal users
- B2B guest users
- SharePoint grounding
- Manual authentication with Entra ID v2 federated credentials

## Preconditions

Before this section:

- SharePoint site is ready
- Internal and guest users already have SharePoint access
- Guest users were invited and accepted in tenant

## Step 1: Create Agent

In Copilot Studio:

1. Create new agent.
2. Set name, description, and instructions.
3. Save.

## Step 2: Add SharePoint as Knowledge Source

1. Open agent settings for knowledge.
2. Add SharePoint source.
3. Select the target site/library.
4. Save.

Note:

Knowledge retrieval is user-contextual. Returned content depends on each signed-in user's SharePoint permissions.

## Step 3: Switch to Manual Authentication

Path:

Settings → Security → Authentication

Set:

- Authenticate manually
- Provider: Microsoft Entra ID v2 with federated credentials
- Require users to sign in: On
- Scopes: profile openid

Save.

## Step 4: Copy Federated Credential Values

After saving manual authentication, copy:

- Federated credential issuer
- Federated credential value (subject)

You will use them in Entra app registration.

## Step 5: Add Client ID Back to Copilot Studio

After app registration is configured in Entra:

1. Return to Copilot Studio authentication settings.
2. Enter app registration client ID.
3. Save.

## Step 6: Share Agent

Path:

Copilot Studio → Share

Share with:

- Internal users
- Guest users
- Or security group

Important:

If user is not shared on the agent, user cannot run the agent.

## Step 7: Publish Agent

Publish after authentication and sharing changes.

## Step 8: Deploy to Web Channel

1. Open Channels → Web app.
2. Copy embed code.
3. Embed in HTML page:

```html
<iframe src="your-copilot-url"></iframe>
```

## Validation

Test in incognito/private browser sessions:

- Internal user
- Guest user

Expected:

- User with SharePoint access gets grounded answers
- User without SharePoint access does not receive that content

## Important Limitation

For this scenario, manual authentication was required.

SSO was intentionally not used for guest plus SharePoint grounding.

Reference:

- https://learn.microsoft.com/en-us/microsoft-copilot-studio/knowledge-add-sharepoint#advanced-authentication-scenarios
