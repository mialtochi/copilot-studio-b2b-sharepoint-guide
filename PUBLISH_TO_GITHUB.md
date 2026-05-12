# Publish Checklist

Use this file as a final pre-release checklist before publishing the repository.

## 1. Content Readiness

- [ ] [README.md](README.md) reflects the exact working implementation.
- [ ] [QUICKSTART.md](QUICKSTART.md) matches the same 12-step flow.
- [ ] Deep-dive docs are consistent:
  - [ ] [docs/ENTRA_ID_SETUP.md](docs/ENTRA_ID_SETUP.md)
  - [ ] [docs/SHAREPOINT_SETUP.md](docs/SHAREPOINT_SETUP.md)
  - [ ] [docs/COPILOT_STUDIO_SETUP.md](docs/COPILOT_STUDIO_SETUP.md)
  - [ ] [docs/TESTING_GUIDE.md](docs/TESTING_GUIDE.md)
  - [ ] [docs/TROUBLESHOOTING.md](docs/TROUBLESHOOTING.md)

## 2. Safety and Compliance

- [ ] No secrets, tenant IDs, real emails, or credentials in files.
- [ ] Sample values remain placeholders only.
- [ ] [configs/agent-config-example.json](configs/agent-config-example.json) contains no production secrets.

## 3. Optional but Recommended

- [ ] Add license file (`MIT` commonly used for docs repos).
- [ ] Add screenshots in [screenshots](screenshots) if you want visual walkthroughs.
- [ ] Add CONTRIBUTING guide if you want community PRs.

## 4. Create Repository

Option A (GitHub Web):

1. Open `https://github.com/new`
2. Repository name: `copilot-studio-b2b-sharepoint-guide`
3. Set visibility (public/private)
4. Create repository

Option B (GitHub CLI):

```bash
gh repo create copilot-studio-b2b-sharepoint-guide \
  --description "Copilot Studio manual auth + SharePoint grounding + B2B guide" \
  --public \
  --source=. \
  --remote=origin \
  --push
```

## 5. Push From Local

If remote is not added yet:

```bash
git remote add origin https://github.com/YOUR_USERNAME/copilot-studio-b2b-sharepoint-guide.git
```

Publish:

```bash
git add .
git commit -m "Prepare publishing-ready guide"
git push -u origin main
```

## 6. Post-Publish Validation

- [ ] Repository opens successfully on GitHub.
- [ ] README renders correctly.
- [ ] All markdown links resolve.
- [ ] Docs folder structure is correct.

## 7. Share and Maintain

- [ ] Share the repo in your network (LinkedIn, Microsoft communities, Teams communities).
- [ ] Watch issues and answer clarifications.
- [ ] Update docs when Copilot Studio or Entra behavior changes.

## Primary Repo Files

- [README.md](README.md)
- [QUICKSTART.md](QUICKSTART.md)
- [docs/ENTRA_ID_SETUP.md](docs/ENTRA_ID_SETUP.md)
- [docs/SHAREPOINT_SETUP.md](docs/SHAREPOINT_SETUP.md)
- [docs/COPILOT_STUDIO_SETUP.md](docs/COPILOT_STUDIO_SETUP.md)
- [docs/TESTING_GUIDE.md](docs/TESTING_GUIDE.md)
- [docs/TROUBLESHOOTING.md](docs/TROUBLESHOOTING.md)
- [configs/agent-config-example.json](configs/agent-config-example.json)
