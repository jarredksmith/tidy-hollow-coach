# Turn on OpenAI camera feedback

The app is at **https://jarredksmith.github.io/tidy-hollow/**. Its private camera coach runs in Cloudflare and calls the OpenAI Responses API. An OpenAI key by itself cannot securely run inside GitHub Pages.

## Browser-only setup (no terminal or Xcode)

### 1. Create your OpenAI API key

Open **https://platform.openai.com/api-keys** and create a key for this project. Make sure API billing is enabled. Keep the key private; you will enter it into Cloudflare, not into GitHub, the app, or a chat message. If using restricted key permissions, allow Responses requests and model lookup (for the connection test).

### 2. Generate your household token

Open Tidy Hollow → **Settings** (sliders icon) → **Generate household token** → **Copy token**. Keep it in a password manager. This is a separate access password for your private backend, not your OpenAI key. Save the app settings to retain it in the current tab's memory. You can enter the backend URL later.

### 3. Deploy the backend

Open **[Deploy the Tidy Hollow camera coach](https://deploy.workers.cloudflare.com/?url=https://github.com/jarredksmith/tidy-hollow/tree/main/backend)** and sign in to or create your Cloudflare account. The flow connects GitHub and creates a separate repository containing the self-contained backend. Use a name such as `tidy-hollow-coach`.

Enter these values when the deployment asks for secrets:

| Setting | Value |
| --- | --- |
| `OPENAI_API_KEY` | Your OpenAI API key |
| `APP_TOKEN` | The household token you generated in Tidy Hollow |
| `ALLOWED_ORIGIN` | Keep `https://jarredksmith.github.io` — no path or trailing slash |
| `OPENAI_MODEL` | Keep `gpt-4.1-mini` initially |

If secrets are not requested during setup, open the deployed Worker → **Settings → Variables and Secrets** and add `OPENAI_API_KEY` and `APP_TOKEN` as **Secret** values, then deploy the change. Never put either value into plaintext source code or ordinary build variables. The Worker rejects requests until both are configured.

The backend has no build step or external runtime dependencies. Its deploy script runs `npx wrangler@4 deploy`. The configuration includes the rate-limiter binding. Keep the Worker name aligned with the name in `wrangler.jsonc` if the deployment interface asks.

Cloudflare will return an address similar to `https://tidy-hollow-coach.your-subdomain.workers.dev`. Copy the actual URL. Opening it directly may show an origin/access error; the private backend is intended to be called through the app.

The deploy button creates a separate backend repository. Later changes to the original app repository will not automatically update that clone. For updates, use the backend repository Cloudflare created, or connect Cloudflare directly to the original repository with root directory `backend`.

### 4. Connect the app

In Tidy Hollow Settings:

1. Paste the Worker URL into **Private AI backend URL**.
2. Enter the same household token you set as `APP_TOKEN`.
3. Press **Test connection**. This checks backend authentication and whether OpenAI accepts the key for the selected model. It does not send room images or verify available analysis credits.
4. Save settings.

The app keeps the household token only in this tab's memory; you may need to re-enter it after closing or reloading the app. The OpenAI key stays in Cloudflare.

### 5. Try one counter

1. Open **Little missions → Turn on camera** and frame one counter or corner.
2. Check the consent box for sending selected images to OpenAI through your backend.
3. Tap **Find tasks in this room**. This is the first real image-analysis request and checks the full vision/billing path.
4. Approve a few useful suggestions.
5. Tap **Start live feedback** and keep the phone still and unlocked.

The app makes a check about every 15 seconds plus processing time, compares it with the starting frame, and gives spoken guidance. After two confident observations, it suggests completion. Automatic points are optional and off by default. Off-camera storage needs your confirmation. The phone's own speech synthesis reads the feedback; no microphone or speech API is used.

## Costs and limits

- API analysis incurs usage charges. No paid live API call has been tested by the developer in this session because a key was not available.
- Checks stop after 40 requests per run and can be restarted.
- The Worker limits analysis to 6 requests per minute per Cloudflare location. This is not a global spending cap. Configure appropriate API quotas and billing controls in your account.
- `gpt-4.1-mini` is a configurable starting model, not an accuracy guarantee. Test a small area before enabling automatic points.
- No room frames are saved by this app or its backend. Requests use OpenAI `store: false`; this does not override OpenAI's other applicable retention policies.

## Troubleshooting

- **Backend setup incomplete:** check the encrypted secrets and `RATE_LIMITER` binding.
- **Household access token error:** the app token must exactly match Cloudflare `APP_TOKEN`.
- **OpenAI rejected the API key:** replace `OPENAI_API_KEY` in Cloudflare.
- **Credits or quota unavailable:** check billing for the OpenAI API project; Test connection alone cannot confirm available credits.
- **Cannot reach backend:** verify the HTTPS Worker URL and `ALLOWED_ORIGIN`.
- **Model unavailable:** choose an image-capable model supporting structured output that your API project can access, and update `OPENAI_MODEL`.
- **Wrong tasks or missed progress:** move closer, uncover some objects, or complete the task manually. There is no penalty for uncertain camera results.

## Optional terminal deployment

From this directory:

```sh
npx wrangler@4 login
npx wrangler@4 secret put OPENAI_API_KEY
npx wrangler@4 secret put APP_TOKEN
npx wrangler@4 deploy
```

Enter secrets only at the interactive prompts. If creating a new Worker, retain its configured name.

## Verification still needed on your iPhone

Try app switching, screen locking, resuming, poor connectivity, and an obstructed view. The app should pause the camera and analysis, ignore stale responses, and keep manual chores available. Confirm that completing the same task twice never awards points twice.

## References

- [OpenAI images and vision](https://developers.openai.com/api/docs/guides/images-vision)
- [OpenAI structured outputs](https://developers.openai.com/api/docs/guides/structured-outputs)
- [GPT-4.1 mini](https://developers.openai.com/api/docs/models/gpt-4.1-mini)
- [Cloudflare deployment buttons](https://developers.cloudflare.com/workers/platform/deploy-buttons/)
- [Cloudflare secrets](https://developers.cloudflare.com/workers/configuration/secrets/)
