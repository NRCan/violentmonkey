

# Violentmonkey
> :warning: This is a fork to hide/disable some features from our clients that will be using this extension to solely load accessibility scripts.

Violentmonkey provides userscripts support for browsers.
It works on browsers with [WebExtensions](https://developer.mozilla.org/en-US/Add-ons/WebExtensions) support.

More details can be found [here](https://violentmonkey.github.io/).

## Environment Variables

The following environment variables will be injected at compilation time for some features to work:

- `SYNC_GOOGLE_CLIENT_ID` / `SYNC_GOOGLE_CLIENT_SECRET` - Google sync service
- `SYNC_ONEDRIVE_CLIENT_ID` / `SYNC_ONEDRIVE_CLIENT_SECRET` - OneDrive sync service

## Workflows

### Development

Install [Node.js](https://nodejs.org/) and Yarn v1.x.  
The version of Node.js should match `"node"` key in `package.json`.

``` sh
# Install dependencies
$ yarn

# Watch and compile
$ yarn dev
```

Then load the extension from 'dist/'.

### Build

To release a new version, we must build the assets and upload them to web stores.

``` sh
# Build for normal releases
$ yarn build

# Build for self-hosted release that has an update_url
$ yarn build:selfHosted
```

### Release

See [RELEASE](RELEASE.md) for the release flow.

## Related Projects

- [Violentmonkey for Opera Presto](https://github.com/violentmonkey/violentmonkey-oex)
- [Violentmonkey for Maxthon](https://github.com/violentmonkey/violentmonkey-mx)
