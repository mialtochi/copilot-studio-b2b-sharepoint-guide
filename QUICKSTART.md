# Quick Start: Real Working Path

This is the shortest path to reproduce the working implementation documented in [README.md](README.md).

## Scope

- Copilot Studio agent
- SharePoint Online grounding via Microsoft Graph
- Internal and B2B guest access
- Manual authentication with Microsoft Entra ID v2 federated credentials
- Web app deployment using embed code

## Important Constraint

For this scenario, use manual authentication.

Do not implement SSO for this guest plus SharePoint grounding path.

Reference:

- https://learn.microsoft.com/en-us/microsoft-copilot-studio/knowledge-add-sharepoint#advanced-authentication-scenarios

## 12-Step Runbook

1. Prepare SharePoint knowledge source and grant user access.
2. Create Copilot Studio agent and add SharePoint knowledge.
3. Set Authentication to Authenticate manually.
4. Copy federated credential issuer and value from Copilot Studio.
5. Create single-tenant Entra app registration.
6. Add web redirect URI: https://token.botframework.com/.auth/web/redirect.
7. Add federated credential (Other issuer) in app registration.
8. Add delegated Graph permissions and grant admin consent:
   - openid
   - profile
   - User.Read
   - Sites.Read.All
   - Files.Read.All
9. Add app Client ID back in Copilot Studio auth settings.
10. Share agent with internal and guest users (or security group).
11. Publish agent.
12. Deploy in Web channel using iframe embed.

## Validation

Test both user types in private/incognito browser sessions:

- Internal user
- Guest user

Expected behavior:

- User has SharePoint access: content returned
- User has no SharePoint access: no content returned

## Open Full Guide

- Full implementation details: [README.md](README.md)
- Deep-dive docs: [docs](docs)
- Publishing prep: [PUBLISH_TO_GITHUB.md](PUBLISH_TO_GITHUB.md)
