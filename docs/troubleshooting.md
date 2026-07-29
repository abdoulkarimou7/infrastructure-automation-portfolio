# Troubleshooting Log

This document records technical issues encountered throughout this portfolio and the steps taken to investigate and resolve them.

The objective is to document the troubleshooting process, improve future debugging efficiency, and demonstrate a structured problem-solving approach.

---

## Incident 01 — GitHub Pages Deployment

### Context

Portfolio deployment to GitHub Pages was failing after configuring the GitHub Actions workflow.

### Symptoms

- Deployment completed successfully.
- The website returned a 404 page.
- Static assets were not loaded correctly.

### Root Cause

The Vite `base` path did not match the GitHub repository name, causing incorrect asset URLs after deployment.

### Resolution

- Updated the `base` option in the Vite configuration.
- Verified the GitHub Pages workflow.
- Redeployed the application.
- Confirmed that all assets loaded correctly.

### Lessons Learned

- Always configure the correct `base` path for GitHub Pages deployments.
- Validate deployment locally before publishing.
- Review generated asset paths when troubleshooting static sites.

---

## Future Incidents

Additional troubleshooting reports will be documented here as new projects are developed.