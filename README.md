# TSPS Staging updater

Keeps the TSPS Staging world in AMP on the latest `RSPSApp/tsps` `main`. Every 10 minutes the
workflow reads upstream `main`. When there is a new commit, it calls the AMP instance's
webhook, and AMP stops, updates and starts the world. Expect up to ~15 minutes from merge to
update (GitHub runs scheduled jobs late under load), plus the build.

## AMP

1. Generate a webhook token in the instance's configuration. It is shown once.
2. **Schedule → Add trigger → Event trigger → Webhook Event Received**, with these tasks:
   1. **If a condition is met**: `Payload` equals `update-main`
   2. Stop the application
   3. Update the application
   4. Start the application
3. The webhook URL is
   `https://<panel>/API/ADSModule/Servers/<instance id>/API/WebhookPlugin/TriggerWebhookEvent`.
   The instance ID is under **Support and Updates** in the instance.

## GitHub

1. Put this folder in its own **public** repository. Public repos get free Actions minutes; a
   10-minute schedule uses more than a private repo's free monthly quota. Secrets stay private.
2. Add two repository secrets (Settings → Secrets and variables → Actions):
   - `AMP_WEBHOOK_URL`: the webhook URL above
   - `AMP_WEBHOOK_TOKEN`: the token from AMP
3. Run **Update TSPS Staging** once from the Actions tab (tick *force*) and check that the
   instance restarts.

GitHub turns off scheduled workflows after 60 days without commits to the repository. Re-enable
it from the Actions tab if that happens.
