# ownCloud web-client Russian translations
This is a mostly automated Russian translation to fix the missing Russian UI translations in the ownCloud Infinite Scale web-client. Feel free to edit.

Repo's Home: https://github.com/dutty5/owncloud-web-ru-translations

### How to use

1. `git clone` or download the release source code of the [owncloud/web](https://github.com/owncloud/web).
2. Add Russian language `ru: 'Русский - Russian'` to the file `./packages/web-runtime/src/defaults/languages.ts`.
3. Install dependencies and build: `pnpm install && pnpm build:w` (https://owncloud.dev/clients/web/backend-ocis/).
4. Install the compiled web-client to use by your oCIS instance (https://doc.owncloud.com/ocis/next/deployment/services/s-list/web.html).
5. Download [translations.json](translations.json) file and place it to the root of your web-client.
6. Add `"customTranslations": [{ "url": "https://ocis.test/translations.json" }]` to the web-client config json file.

### Further tweaking

If you have installed [OnlyOffice Document Server](https://helpcenter.onlyoffice.com/installation/docs-community-index.aspx) to [work](https://doc.owncloud.com/ocis/next/conf-examples/office/office-integration.html) with your oCIS instance, you may have noticed that some oCIS interface languages ​​are not inherited by OnlyOffice, although OnlyOffice supports these interface languages. This is true, for example, for Bulgarian and Dutch. You'll probably experience the same thing with your newly installed Russian language.

By delving deeper into the problem, we can see the immediate cause of this behavior. OnlyOffice receives the setting in the POST request as follows (Dutch example):

`POST https://documentserver.test/hosting/wopi/.../edit?UI_LLCC=nl&WOPISrc=https://wopiserver.test/wopi/files/...&lang=en&ui=nl`

Compare for Deutsch:

`POST https://documentserver.test/hosting/wopi/.../edit?UI_LLCC=de&WOPISrc=https://wopiserver.test/wopi/files/...&lang=de-DE&ui=de`

So, for some reason, for some languages ​​in oCIS, the `lang` variable gets set to default `en`, which causes OnlyOffice to display the English user interface and apply the `en-US` regional settings.

The following minor tweak will fix this.

Patch the OnlyOffice Document Server file `/var/www/onlyoffice/documentserver/web-apps/apps/api/wopi/editor-wopi.ejs`.

Change
```
"editorConfig": {
      ...
      "lang": queryParams.lang || queryParams.ui || "en-US",
      "region": queryParams.rs,
```
to
```
"editorConfig": {
      ...
      "lang": queryParams.ui || queryParams.lang || "en-US",
      "region": queryParams.ui || queryParams.rs,
```
to give the `ui` variable the precedence.

However, if you need to keep the 4-character regional language code for other languages in your environment when it is provided, you can use more complex code in `editor-wopi.ejs` to set the `region` variable. The above approach just gives you a quick workaround until the bug fixed somewhere upstream. Feel free to open a discussion if you know where to fix the root cause of the incorrectly set `lang` variable.

It's worth mentioning how the OnlyOffice external app is added to the oCIS web-client config json file:
```
"external_apps": [
    {
      "id": "onlyoffice",
      "path": "com.owncloud.api.app-provider-onlyoffice"
    }, ...
```
