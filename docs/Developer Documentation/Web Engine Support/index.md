## Web Engine Application Guide

- What is a web engine app?
- What is the new transport that web engine apps use?

### Web Engine Apps

A web engine app is a web application that runs within the vehicle. This is made possible by an OEM hosted "app store" which distributes approved "app bundles." The HMI will decompress these app bundles and launch the entrypoint which will use the SDL JavaScript library to interact with SDL Core.

App bundles are zip compressed archives containing
- manifest.js
    - a javascript file exporting properties about the application
        - entrypoint
            - a relative path within the bundle to the html file that will be launched by the HMI
            - this HTML file must include the manifest.js file as a script
        - appIcon
            - a relative path to the app icon within the app bundle
        - appId
            - the `policyAppId` of this application
        - appName
            - the app name that should be displayed in the app store or in the app list
        - category
            - the primary `appHMIType` of the web engine application
        - additionalCategories
            - additional `appHMIType`s of the web engine application
        - locales
            - a map of other languages to alternate names and icons
        - appVersion
            - the current version of the application
        - minRpcVersion
            - the minimum supported RPC spec version
        - minProtocolVersion
            - the minimum supported protocol spec version
- sdl.js
    - the SDL JS library used to interact with SDL Core
- Any HTML / JS files used to run the application

To see an example web engine application, take a look at [the example in the javascript suite on GitHub](https://github.com/smartdevicelink/sdl_javascript_suite/tree/develop/examples/webengine/hello-sdl).

Once the HMI has the app bundle on it's local file system, the HMI will use information provided by the app store, as well as information defined in the manifest file in order to call `SetAppProperties` and enter the app into Core's policy table. Then, when a user selects the newly installed application, the HMI will use information from the same two sources to launch the entrypoint HTML file in an invisible iframe. Here, the app will begin execution on the head unit and eventually call `RegisterAppInterface`. When the HMI receives an `OnAppRegistered` notification signalling that the web engine app has successfully registered, it will then send Core an `ActivateApp` request.

### WebSocket Server Transport

Before proposal 0240, SDL Core supported websocket transport with the application as the websocket server. With the addition of web engine applications, Core needed to support websocket transport with Core as the websocket server. This change will shift the burden of knowing where the server is from Core to the applications. When the HMI launches one of these web engine applications, it will provide Core's hostname and port as query parameters to the entrypoint of the web engine application. This transport supports both secure and non-secure websocket communication; which is used is also determined by a query parameter passed to the entrypoint html file of the web engine application.

Websocket server transport will only run if either all three of these are valid or if none are provided:
- WSServerCertificatePath (path to websocket server certificate)
- WSServerKeyPath (path to websocket server private key path)
- WSServerCACertificatePath (path to CA certificate)

If all three are provided, SDL Core will use WebSocket Secure, otherwise, Core will use regular WebSocket communication.
