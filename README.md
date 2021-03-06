# Kloudless File Explorer

**Sign up for a free account at [https://kloudless.com](https://kloudless.com) to obtain
a Kloudless App ID to initialize the File Explorer with.** The File Explorer is built on
our unified Storage API abstraction layer ([full docs here](https://developers.kloudless.com/docs/latest/storage)).

The [Kloudless](https://kloudless.com) File Explorer is a JavaScript library
that allows your users to browse and select files and folders from the following
storage services:

<table>
  <thead>
    <tr><th colspan="4">Services</th></tr>
  </thead>
  <tbody>
    <tr><td>Adobe CQ5</td><td>Alfresco Cloud</td><td>Alfresco</td><td>Amazon S3</td></tr>
    <tr><td>Azure Storage</td><td>Box</td><td>CMIS</td><td>Citrix Sharefile</td></tr>
    <tr><td>Dropbox</td><td>Egnyte</td><td>Evernote</td><td>FTP</td></tr>
    <tr><td>Google Drive</td><td>HubSpot Files</td><td>Jive</td><td>OneDrive for Business</td></tr>
    <tr><td>OneDrive</td><td>SMB</td><td>Salesforce Files</td><td>SharePoint Online</td></tr>
    <tr><td>SugarSync</td><td>WebDAV</td><td></td><td></td></tr>
  </tbody>
</table>

### [Click here to visit our File Explorer demo!](https://output.jsbin.com/tuwodin/)

<p align="center">
  <img src="img/demo.gif" width="650" />
</p>

## Table of Contents

* [Usage](#usage)
  * [Import from script tag](#import-from-script-tag)
  * [Import from an ES6 module](#import-from-an-es6-module)
  * [Chooser](#chooser)
    * [Dropzone](#dropzone)
  * [Saver](#saver)
  * [Global Options](#global-options)
  * [Configuration](#configuration)
    * [Script Tag](#script-tag)
    * [Chooser and Saver](#chooser-and-saver)
    * [Chooser options](#chooser-options)
    * [Saver options](#saver-options)
  * [Events](#events)
  * [Methods](#methods)
  * [Example for script tag usage](#example-for-script-tag-usage)
  * [Dropzone](#dropzone-1)
    * [Configuration](#configuration-1)
    * [Methods](#methods-1)
    * [Example](#example)
* [File Explorer with Vue](#file-explorer-with-vue)
* [File Explorer with React](#file-explorer-with-react)
* [Browser Support](#browser-support)
  * [Work with mobile devices](#work-with-mobile-devices)
* [Migration Guide](#migration-guide)
  * [From v1.0.0 to v1.0.1](#from-v100-to-v101)
* [Contributing](#contributing)
  * [Building](#building)
    * [Install](#install)
    * [Build Explorer](#build-explorer)
    * [Build Loader](#build-loader)
  * [Testing](#testing)
* [Self-hosting](#self-hosting)
* [Misc. Development Notes](#misc-development-notes)
* [Security Vulnerabilities](#security-vulnerabilities)
* [Support](#support)

## Usage

### Import from script tag

Embedding the Kloudless File Explorer javascript library will expose a global
`Kloudless.fileExplorer` object. The JS file is currently hosted on S3 and can
be embedded in your page using this tag:

```html
<script type="text/javascript"
 src="https://static-cdn.kloudless.com/p/platform/sdk/kloudless.explorer.js"></script>
```

You can then create an element on the page to launch the explorer.

```html
<script type="text/javascript">
  var explorer = window.Kloudless.fileExplorer.explorer({
    // Explorer Initialization options here.
    app_id: "Your App ID", // Get your own at https://kloudless.com
  });
</script>
```

### Import from an ES6 module

Install from NPM:
```
npm install @kloudless/file-explorer
```

```javascript
import fileExplorer from '@kloudless/file-explorer';

const explorer = fileExplorer.explorer({
  app_id: "YOUR_APP_ID"
});
```


Be sure to serve the page via a web server, as pages opened via the
file URI scheme (`file://`) cannot receive messages sent via postMessage
due to security concerns.

The File Explorer can be configured to be either a file [chooser](#chooser) or
a file [saver](#saver).
An `iframe` will be created by the JS library to view the explorer.

### Chooser

The Chooser allows your application to prompt a user to select files or a folder,
and retrieves metadata about the files or folder selected.
It supports choosing files from the local machine as well as cloud storage.

##### Dropzone

The Dropzone is a variety of the Chooser that opens when files are dragged and
dropped into it rather than only when clicked. See the [Dropzone](#dropzone-1)
section for more information.

### Saver

The Saver allows your application to prompt a user to select a folder to save
files to. Metadata on the newly uploaded files will be returned to the developer.
URLs to the files to upload must be provided.

### Global Options

Global options are the options apply to all the explorer instances.

Here is the option that you can set:

- `explorerUrl`  
  The URL that serves the File Explorer.
  Defaults to `https://static-cdn.kloudless.com/p/platform/explorer/explorer.html`.

### Configuration

The File Explorer has the following configuration options:

#### Script Tag

The following attributes can be set on the `<script>` tag used to include the
File Explorer JavaScript on the page.

* `data-kloudless-object` : string

  _Optional_. By default, the `fileExplorer` object will be exposed to 
  `window.Kloudless.fileExplorer`.

  Specifies a different name for the `fileExplorer` object bound to `window`.
  e.g. `data-kloudless-object="fileExplorer2"` would make the fileExplorer
  object accessible via `window.fileExplorer2`. Do not use the `async` attribute
  on the script tag if this is used.

#### Chooser and Saver

* `app_id` : string

  Chooser: _Required_

  Saver: _Required_

  The application ID is specific to the developer's application and is located
  in the developer portal on the App Details page.
  
* `retrieve_token` : boolean

  Chooser: _Optional (default: false)_

  Saver: _Optional (default: false)_

  This option will include [Bearer Tokens](https://developers.kloudless.com/docs/latest/authentication)
  in addition to the other response data, to allow you to make further requests to the API
  or save the OAuth Tokens for future use.

  ```javascript
    // Example response with an OAuth Token in the metadata.
    [{
        ...
        bearer_token: {
          key: "the_token",
        },
        ...
     }]
  ```

  Only File Explorers launched from Trusted Domains can make use of this option.
  You can add a Trusted Domain on the App Details page.

  In addition, care should
  be taken to ensure no malicious JavaScript or XSS vulnerabilities are present
  on the page, as the Bearer Token provides complete access to that user's account.

* `computer` : boolean

  Chooser: _Optional (default: false)_

  Saver: _Optional (default: false)_ Coming Soon

  This option allows users to upload/download files directly from/to their computer.

  __Configuring the Chooser's Upload Location__

  The Chooser will upload files to the developer's Upload Location.
  The Upload Location can be set in the [developer portal](https://developers.kloudless.com)
  under 'App Details', by selecting a folder in a storage service such as Amazon S3.
  All local files from a user will be uploaded there.

  __Note on the Saver__

  The Computer can only be saved to if the Saver is provided a single file to save.
  Saving to the Computer is currently unavailable and will be coming soon.

* `persist` : string

  Chooser: "none", "local", "session" (default: "local")

  Saver: "none", "local", "session" (default: "local")

  This option specifies account persistence for the explorer in either localStorage,
  sessionStorage, or no storage. The explorer will always fall back to localStorage
  if an invalid option is given.

* `services` : array

  Chooser: _Optional (default: ['file_store'])_

  Saver: _Optional (default: ['file_store'])_

  This option specifies which services to allow a user to explore. You can enumerate
  the specific [services](https://developers.kloudless.com/docs/latest/core#introduction-supported-services) or
  use a service group listed below as a shortcut.
  The default is the `file_store` service group.  If you specify an empty array,
  no services will show up.
  
  Service groups:
  
  * `file_store`: All File Storage services.
  * `object_store`: All Object Storage services.
  * `construction`: All Construction services.
  * `all`: All storage services.

* `account_management` : boolean

  Chooser: _Optional (default: true)_

  Saver: _Optional (default: true)_

  If `false`, hides the Account menu entry in the dropdown. This can be
  combined with the `tokens` option to ensure a user only browses a pre-defined
  list of accounts and also does not disconnect any of the accounts. If account
  disconnection is permitted, set `services` to `[]` rather than disabling the
  `account_management` option.

* `display_backdrop` : boolean

  Chooser: _Optional (default: false)_

  Saver: _Optional (default: false)_

  If `true`, displays a shadow backdrop behind the File Explorer, and prevents the
  page body from scrolling.

* `custom_css` : string

  Chooser: _Optional (default: null)_

  Saver: _Optional (default: null)_

  An optional stylesheet URL to load to override existing styles.
  Supports `(http|https)://domain.com/path/to.css`, `//domain.com/path/to.css`, 
  and `/path/to.css` formats.

  The domain the File Explorer is launched from must be added to the application's list
  of [Trusted Domains](https://developers.kloudless.com/applications/*/details) for the
  `custom_css` property to be used.

* `create_folder` : boolean

  Chooser: _Optional (default: true)_

  Saver: _Optional (default: true)_

  If `true`, the user will be able to create folders in their cloud storage
  accounts.

* `account_key` : boolean

  This option is deprecated as OAuth 2.0 Tokens are used to authenticated accounts
  now instead of Account Keys. Please use the `retrieve_token` option below instead.
  Existing Account Keys can be converted to OAuth Tokens using
  [this endpoint](https://developers.kloudless.com/docs/v0/storage#account-keys-account-key-oauth-token).

* `keys` : array

  This option is deprecated as OAuth 2.0 Tokens are used to authenticated accounts
  now instead of Account Keys. Please use the `tokens` option below instead.
  Existing Account Keys can be converted to OAuth Tokens using
  [this endpoint](https://developers.kloudless.com/docs/v0/storage#account-keys-account-key-oauth-token).

* `tokens` : array

  Chooser: _Optional (default: [])_

  Saver: _Optional (default: [])_

  This option should list [OAuth 2.0 Tokens](https://developers.kloudless.com/docs/latest/authentication)
  for accounts the File Explorer should be initialized with. The File Explorer will make API
  requests for additional information on the accounts and display them
  in the list of accounts the user has connected.

  ```javascript
    // Example initialization with OAuth Tokens to import.
    explorer({
      ...
      tokens: ["abcdefghijklmn", "opqrstuvwxyz"]
      ...
    });
  ```
  
* `enable_logout` : boolean (default: true)

  If `true`, allows users to log out of their accounts, which deletes the account
  from Kloudless servers. API requests will no longer be possible to the account.


#### Chooser options

* `multiselect` : boolean

  Chooser: _Optional (default: false)_

  This option allows a user to select multiple files using the Chooser.  The Chooser
  will return a list of one or more entries.

* `link` : boolean

  Chooser: _Optional (default: true)_

  This option adds an additional parameter to the Chooser's response with a
  link to the file chosen in the file explorer.

  Use with the `copy_to_upload_location` to generate a link to the newly
  copied file that is returned in the callback.

  ```javascript
    // Response
    // [{
    //    ...
    //    "link":
    //    ...
    // }]
  ```

* `link_options` : Object

  Chooser: _Optional (default: {})_

  The `link_options` object provides parameters to use when the File Explorer
  creates links. See the
  [documentation for creating links](https://developers.kloudless.com/docs/latest/storage#links-create-a-link)
  for a full list of the possible options available.
  Note that links to selected files are only created if the `link`
  configuration option is set to `true`.

  For example:

  ```javascript
      ...,
      link_options: {
        direct: true,
        expiration: "2020-10-12T00:00:00"
      },
      ...
  ```

* `copy_to_upload_location` : string

  Chooser: _Optional (default: null)_

  If this option is set, it will copy any file selected by the user from cloud storage
  to the location that files uploaded from the user's computer are placed in.
  An Upload Location must be configured via the developer portal to make use of
  this option.

  This option accepts two values:
  * `'async'`: Triggers an asynchronous copy and immediately returns a Task ID
    in the `success` event callback that can be checked using the
    [Kloudless Task API](https://developers.kloudless.com/docs/latest/core#asynchronous-requests-and-the-task-api)
    for the copied file's metadata.
  * `'sync'`: Triggers a synchronous API request to copy the file and polls
    till the copying is complete. The `success` event callback receives the
    newly uploaded file's metadata.

  The deprecated `true` option returns the new file's hypothetical metadata
  even though the copy has not yet completed. Please migrate to using
  `'sync'` instead for similar behavior with a guarantee that the copy is
  successful.

* `upload_location_account` : string

  Chooser: _Optional (default: null)_

  If multiple Upload Locations are configured via the developer portal, this
  specifies the Account ID of the Upload Location to use. This option is not
  required if only one Upload Location is configured. The Account ID is a number
  that can be found in the File Explorer Upload Locations section of the
  [App Details](http://developers.kloudless.com/applications/*/details) page.
  `upload_location_folder` must also be provided.

* `upload_location_folder` : string

  Chooser: _Optional (default: null)_

  If multiple Upload Locations are configured via the developer portal, this
  specifies the Folder ID of the Upload Location to use. This option is not required
  if only one Upload Location is configured. The Folder ID is an encoded string that
  can be found on the File Explorer Upload Locations section of the
  [App Details](http://developers.kloudless.com/applications/*/details) page.
  `upload_location_account` must also be provided.

* `uploads_pause_on_error` : boolean

  Chooser: _Optional (default: true)_

  If `true`, uploads using the Computer option will pause if errors are encountered,
  giving the user the option to retry the upload by clicking 'Resume'. The user could
  also remove the file that is failing to upload from the queue.
  If `false`, the file that encounters errors will be skipped and excluded from the
  list of files returned in the `success` callback.

* `types` : array

  Chooser: _Default: ['all']_

  This option specifies which types of elements the explorer will show to the user.
  You can filter based on file extension or by the following categories:

  * `all` This configures the explorer to show all file types and folders. The
  user can select either files or folders. Do not combine with other options.

  * `folders` This configures the explorer to gray out files and show folders. The
  user can only select folders.

  * `files` This configures the explorer to show all file types. The user can only
  select files.

  * `text`

  * `documents`

  * `images`

  * `videos`

  * `audio`
  
To filter by file extension, include the extension in the array without the period (`.`) prefix.
For example, `['pdf', 'jpg', 'jpeg', 'png']`.

#### Saver options

* `files` : array

  Saver: _Optional (default: [])_

  This option should list files for the File Explorer to save. The format
  should be an array of Javascript Objects containing a file url and name.
  You can specify up to 100 files. Each file will be uploaded via the Kloudless
  API's [upload endpoint](https://developers.kloudless.com/docs/latest/storage#files-upload-a-file),
  using the `url` attribute.

  ```javascript
    // Example initialization with files to save.
    explorer({
      ...
      files: [{
        "url": "http://<your image url>",
        "name": "filename.extension"
      }, {
        "url": "http://<your image url>",
        "name": "filename.extension"
      }]
      ...
    });
  ```

### Events

* `success(files)`

  The success event handler will fire when the user's operation succeeds.
  `files` is an array of file/folder metadata. The File Explorer will be
  closed on success.

* `cancel()`

  Fired if the user decides to cancel an operation. The File Explorer will
  be closed on cancellation.

* `error(error)`

  Fired in the event of an unrecoverable error.

* `open()`

  Fired when the File Explorer is displayed to the user. This occurs when
  the Chooser or Saver are opened.

* `close()`

  Fired when the File Explorer is hidden from the user. This occurs when
  the File Explorer is closed. This could be due to either a user action,
  such as choosing files or cancelling, or due to the `close()` method
  being called (not to be confused with this `close` event handler).
  The `success()` or `cancel()` events will also trigger if appropriate.

* `selected(files)`

  The selected event handler is fired when the user selects files from the
  explorer, but before sending the list to the Kloudless API for additional
  processing. It fires before the `success` handler, and can allow you to
  perform an action while waiting to get the final list.

  If `copy_to_upload_location` and `link` are both false, then this event is
  equivalent to the `success` event (since nothing needs to happen on the
  server after the selections are made), so this event probably isn't useful
  for you.

  `files` is an array of files, formatted the same way as the array passed to
  `success`.

  This event is not fired when the user uses the Chooser's Computer option or
  the Saver. See `startFileUpload` and `finishFileUpload` for those.

* `addAccount(account)`

  Fired when a user successfully adds an account.
  `account` is an object containing the account id, name, and service.

* `deleteAccount(account)`

  Fired when a user successfully removes an account.
  `account` is an object containing the account id of the deleted account.

* `startFileUpload(file)`

  Fired when a file upload starts, or is requested to be retried by a user
  after encountering errors (see the `uploads_pause_on_error` option).
  This event is only fired when the user uploads a file via the Chooser's
  Computer option or the Saver. For the Chooser, `file` is an object containing
  keys `id`, `name`, `size` and `mime_type`. For the Saver, it contains `url`
  and `name`.
  This event is fired per file and not per chunk.

* `finishFileUpload(file)`

  Fired when a file upload completes successfully (once per file being
  uploaded). `file` contains the same information as in `startFileUpload`
  above, with an additional field `metadata` that contains the full metadata
  of the file provided by the Kloudless API in response to the upload. The
  `metadata` is the same as the file metadata provided via the `success`
  event for each file.

  This event is only fired when the user uploads a file via the
  Chooser's Computer option or the Saver.

* `logout()`

  Fired when the user clicks the logout link in the File Explorer, which
  clears the local state of accounts connected to the File Explorer.

* `drop()`

  Fired when drops files into Dropzone.

### Methods

* `fileExplorer.explorer(options)`

  You can initialize a Kloudless Explorer using the options mentioned above.

* `fileExplorer.getGlobalOptions()`

  Get global options.

* `fileExplorer.setGlobalOptions(globalOptions)`

  Set global options.

* `explorer.choose()`

  This method allows you to launch a Chooser.

* `explorer.choosify(element)`

  This method binds a click handler that launches the Chooser to the DOM element.

* `explorer.save(files)`

  This method allows you to launch a Saver. See *Saver Options* for more information
  on the format for `files`.

* `explorer.savify(element, files)`

  This method binds a click handler that launches the Saver for `files` to the
  DOM element.

* `explorer.on(event, callback)`

  Invokes a callback method on a particular event. See the `Events` section above
  for a list of event names, as well as the arguments the corresponding callback
  methods will be invoked with.

* `explorer.close()`

  This method closes the explorer window.


* `explorer.update(options)`

  Updates the configuration options the explorer was initialized with. The
  explorer will immediately begin using the new configuration.
  `options` is an Object with the new configuration. Not all options can be
  updated in this manner. The following are supported:

  * `link_options`
  * `upload_location_account`
  * `upload_location_folder`

### Example for script tag usage

To start using the File Explorer, simply include the JavaScript file in your
HTML file. You can then create an element on the page to launch the explorer.

```html
<body>
  <button id="file-explorer">Explore!</button>
  <script type="text/javascript" src="https://static-cdn.kloudless.com/p/platform/sdk/kloudless.explorer.js"></script>
  <script type="text/javascript">
      // Explorer initialization JS here.
  </script>
</body>
```

The next step is to customize the file explorer according to the developer's needs.
In this example, we've decided to disable multiselect, return links, and allow for
a few types of files.  All of this is contained within the initialization.

```javascript
// Explorer initialization JS
var explorer = window.Kloudless.fileExplorer.explorer({
  app_id: 'YOUR_APP_ID',
  multiselect: false,
  link: true,
  types: ['images', 'documents', 'text']
});
```

The final step is to launch the explorer and handle the events returned from the
explorer based on a user's actions.

```javascript
// When a user successfully selects or saves a file
explorer.on('success', function(files) {
  // files is an array of JS objects that contain file metadata.
  console.log('Successfully selected files: ', files);
});

// When a user cancels the explorer
explorer.on('cancel', function() {
  console.log('File selection cancelled.');
});

// Launching the explorer to choose when a user clicks the Explore! button
explorer.choosify(document.getElementById('file-explorer-chooser'));

// In addition, you can launch the explorer programmatically with choose()
explorer.choose();

// Launching the explorer to save when a user clicks the Explore! button
// Note: you can pass in an array of files instead of using the configuration option
var files = [{
  "url": "http://<your image url>",
  "name": "filename.extension"
}, {
  "url": "http://<your image url>",
  "name": "filename.extension"
}];

explorer.savify(document.getElementById('file-explorer-saver'), files);

// In addition, you can launch the explorer programmatically with save()
var files = [{
  "url": "http://<your image url>",
  "name": "filename.extension"
}, {
  "url": "http://<your image url>",
  "name": "filename.extension"
}];

explorer.save(files);
```

[Visit our JSBin example of the File Explorer!](https://output.jsbin.com/tuwodin/)

### Dropzone

The Dropzone is a variety of the Chooser that allows users to drop files into
it from their Computer rather than only click on it to launch the File
Explorer. It opens in place when files are dropped into it.

#### Configuration

The configuration is similar to the Chooser's, but with an extra option `elementId`
as described below. The `computer` option is always set to `true`, and an
Upload Location must be configured as described for the Chooser.

* `elementId` : string

  Chooser: _Required_

  The ID of the DOM element to bind the drop zone to. The drop zone will occupy
  the entire height and width of the element and provide an area for the user to
  drag and drop files in.

#### Methods

* `fileExplorer.dropzone(options)`

  Initialize a Dropzone using the options mentioned above.

* `dropzone.on(event, callback)`

  See `explorer.on(event, callback)` for more information.

* `dropzone.close()`

  See `explorer.close()` for more information.

#### Example

[Visit our JSBin example of the Dropzone!](https://output.jsbin.com/vakura/)

HTML

```html
<div id="dropzone" style="width:600px; height:100px"></div>
```

JavaScript

```javascript
var dropzone = window.Kloudless.fileExplorer.dropzone({
    app_id: "Your App ID",
    elementId: 'dropzone',
    multiselect: true, // To upload more than 1 file.

    // Chooser options below:
    computer: true,
    link: true,
    services: ['all'],
    types: ['all'],
});

// All of the Chooser's events are supported.
// For example:
dropzone.on('success', function(files) {
    console.log('Successfully selected files: ', files);
});

// See the File Explorer's Example section for other events.
```

## File Explorer with Vue

See [File Explorer with Vue](./README.vue.md).

## File Explorer with React

See [File Explorer with React](./README.react.md).

## Browser Support

* Chrome 56+
* Firefox 56+
* IE 11+
* Edge 12+
* Safari 8+
* Opera 49+

### Work with mobile devices
To use the Kloudless File Explorer on devices with narrow screens, please add a
[Viewport meta tag](https://developer.mozilla.org/en-US/docs/Mozilla/Mobile/Viewport_meta_tag)
to the web page launching the File Explorer. This ensures the File Explorer uses styling
appropriate for mobile screen sizes.

The meta tag assumes that your entire web page renders well in narrow screens 
since it applies to your page as a whole and not just the File Explorer.
Please test out your page's usability if you are adding the meta tag for the
first time.

Here is an example meta tag:

    <meta name="viewport" content="width=device-width, initial-scale=1">

If your page benefits from preventing some devices such as iPhones from
zooming in on input fields, you can also add in the `user-scalable=no` option 
as shown below. Input fields such as `<input>` and `<select>` are used in the
File Explorer as well.

    <meta name="viewport" content="width=device-width, initial-scale=1, user-scalable=no">


## Migration Guide

### From v1.0.0 to v1.0.1

For script tag usage, we change exposing target from `window.Kloudless` to
`window.Kloudless.fileExplorer` to better scope our UI tools.
All the exports under `window.Kloudless` are deprecated and moved to
`window.Kloudless.fileExplorer`.

## Contributing

Contributions are welcome and appreciated. We'd love to discuss any ideas you
have to improve or extend the File Explorer. It is strongly recommended to
discuss ideas with Kloudless first for major modifications that you would like
merged in so we can offer feedback on its implementation.

### Building

#### Install

Install Node.js (`sudo apt-get install nodejs` or equivalent)
Make sure you have nodejs >= 11 and npm >= 6.7.0.
Run the following commands:

    npm install
    npm run install-deps

#### Build Explorer

```
npm run build:explorer
```

Here are a few of the more useful [Grunt](http://gruntjs.com/) tasks available:

* `npm run build:explorer-only`
  This can be used instead of `npm run build:explorer` if only non-library
  files have been changed. Especially useful during development.

* `npm run build:explorer`
  During development, this will build all non-minified versions of files for
  easier debugging purposes.

* `npm run build:explorer-deploy`
  For deployment purposes, use `npm run build:explorer-deploy` to build
  minified versions of the files needed.

All output files are placed in the `dist/explorer` directory. 
Included in that directory are JS, CSS and HTML files that compose the 
File Explorer.

#### Build Loader

Run following command would output a non-minified bundle file and a minified
bundle file into `dist/loader` directory.

```
npm run build:loader
```

By default, the loader loads File Explorer from
`https://static-cdn.kloudless.com/p/platform/explorer/explorer.html`.  
You can change it by setting `explorerUrl` environment variable:
```
explorerUrl=https://mysite.com/file-explorer npm run build:loader
```
This is useful for self-hosting the File Explorer.

### Testing

Automated testing is not present. For now, you can manually confirm that the
library works by running the test server.

    $ npm run dev
    $ cd test
    $ npm install # only needed the first time to install dependencies
    $ KLOUDLESS_APP_ID=app_id npm start

where 'app_id' above is a Kloudless App ID specifying which app to connect the
accounts to. You can create an application in the Developer Portal for testing
purposes.

Since the webserver is running at `localhost:3000`, add `localhost:3000` to your
App's list of Trusted Domains to allow it to receive Account Keys to make API
requests with. Be careful to only do this with an app you are using for
development purposes so that there is no security risk.

Then navigate to `localhost:3000` and click the buttons to test.

## Self-hosting

* Modify the JS and CSS links at `example/explorer.html` to point to where you will
  be hosting the compiled JS and CSS files for the explorer.
* Run `explorerUrl=$URL npm run build:loader` to build loader and
  `npm run build:explorer` to build file explorer, where `$URL` is the URL that
  will serve the File Explorer.
* Place `dist/explorer/js/explorer.js` and `dist/explorer/css/explorer.css`
  at the locations you specified in `example/explorer.html`.
* Make `dist/explorer/explorer.html` available at `$URL`.
  Feel free to customize the way assets are loaded/delivered.
  The important part is that the JS, HTML and CSS must all be included on that
  page.
* Ensure the domain of `$URL` is added to your Kloudless App's list of
  Trusted Domains. This can be done on the App Details page in the [Developer
  Portal](https://kloudless.com). This is necessary because Account Keys will
  be sent via postMessage from the Kloudless API server's authentication popup
  to the File Explorer hosted on your domain and the API server should first
  confirm that it can trust your domain for your app.
* Include the loader JS file from `dist/loader/js/loader.js`
  in any page you would like the File Explorer functionality to be made
  available at, and follow the Usage notes above.
* Navigate to the page you included the loader at and use the File Explorer!

## Misc. Development Notes

* Need to implement automated testing!!!
* In order to switch folders, we change the current() pointer and then
  refresh(). refresh() will abort any currently active requests to prevent race
  conditions
* Put buttons that can be clicked repeatedly into data-bind clicks instead of
  the router, because the hash will prevent the route from being fired twice
  consecutively. Use the hash to switch between pages of the application.

## Security Vulnerabilities

If you have discovered a security vulnerability with this library or any other
part of Kloudless, we appreciate your help in disclosing it to us privately by
emailing security@kloudless.com.

## Support

Feel free to contact us at support@kloudless.com with any feedback or
questions. Other methods to contact us are listed
[here](https://developers.kloudless.com/docs/v1/core#getting-help).
