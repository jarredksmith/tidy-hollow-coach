# Tidy Hollow camera coach

Private OpenAI image-analysis backend for [Tidy Hollow](https://jarredksmith.github.io/tidy-hollow/).

[Deploy to Cloudflare](https://deploy.workers.cloudflare.com/?url=https://github.com/jarredksmith/tidy-hollow/tree/main/backend)

Cloudflare asks for `OPENAI_API_KEY` and `APP_TOKEN` as encrypted secrets. Generate the household token in the app's Settings, copy it here, then save the same token in the app. Do not put the OpenAI key into the app or repository.

See [SETUP.md](SETUP.md) for browser-only setup, testing and limits. This backend directory is self-contained and can be cloned by Cloudflare's deployment flow. That creates a separate backend repository; changes in the original Tidy Hollow repository do not automatically propagate to the clone.
