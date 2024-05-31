# ownCloud web client Russian translations
This is a mostly automated Russian translation to fix the missing Russian UI translations in the ownCloud Infinite Scale web client.

### How to use

1. `git clone` or download the release source code of the owncloud/web (https://github.com/owncloud/web).
2. Add Russian language to the file `./packages/web-runtime/src/defaults/languages.ts`.
3. Install dependencies and build: `pnpm install && pnpm build:w` (https://owncloud.dev/clients/web/backend-ocis/).
4. Install the compiled web-client to use by your ocis instance (https://doc.owncloud.com/ocis/next/deployment/services/s-list/web.html).
5. Download `translations.json` file and place it to the root of your web-client.
6. Add `"customTranslations": [{ "url": "https://ocis.test/translations.json" }]` to the web-client config json file.
