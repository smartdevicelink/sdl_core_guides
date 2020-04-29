# Encryption Setup

## Generate Self Signed Certificate

- Create private key:
```bash
openssl genrsa -out client.key 2048
```

- Create CSR:
```bash
openssl req -new -key client.key -out client.req -subj '/C=US/ST=MI/L=Detroit/O=SDL/OU=HeadUnit/CN=client/emailAddress=sample@sdl.com'
```

- Create Public Certificate:
```bash
openssl x509 -hash -req -in client.req -signkey client.key -out client.cert -days 10000
```

## Configure SDL Core

### INI file modifications

- Copy client.key and client.cert into your SDL Core `build/bin` directory. Delete any existing key, cert/crt, or pem files.

In your build/bin directory run:
```bash
c_rehash .
```

- Set the certificate and key file path for SDL in `smartDeviceLink.ini`. The [INI Configuration](https://smartdevicelink.com/en/guides/core/getting-started/ini-configuration/) has more information about the properties in the INI file.

```ini
; Certificate and key path to pem file
CertificatePath = client.cert
KeyPath         = client.key
```

- If you are using self signed certificates set `VerifyPeer` to false.
```ini
; Verify Mobile app certificate (could be used in both SSLMode Server and Client)
VerifyPeer  = false
```

### Policy table modifications

These modifications can be made in your `sdl_preloaded_pt.json` before launching Core or by updating the policy table while Core is running via a [PTU](https://smartdevicelink.com/en/guides/sdl-overview-guides/policies/overview/#policy-table-updates)

- Add `"encryption_required": true` to a functional group in the `functional_groupings` section

```json
...
    "functional_groupings": {
        ...
        "EncryptedRPCs": {
            "encryption_required" : true,
            "rpcs":{
                "AddCommand": {
                    "hmi_levels": ["BACKGROUND",
                    "FULL",
                    "LIMITED"]
                },
                "Alert": {
                    "hmi_levels": ["BACKGROUND", 
                        "FULL", 
                        "LIMITED"]
                },
                ...
            }
        },
        ...
    }
...
``` 

- Add `"encryption_required": true` to an application in the `app_policies` section

```json
...
    "app_policies": {
        ...
        "appId": {
            "keep_context": false,
            "steal_focus": false,
            "priority": "NONE",
            "default_hmi": "NONE",
            "groups": ["Base-4", "EncryptedRPCs"],
            "RequestType": [],
            "RequestSubType": [],
            "encryption_required": true
        },
        ...
    }
...
```

#### JSON Example

Below is a possible policy table configuration requiring an app to use encryption for a specific functional group.

```json
        "functional_groupings": {
            "EncryptedAddCommand": {
                "encryption_required" : true,
                "rpcs":{
                    "AddCommand": {
                        "hmi_levels": ["BACKGROUND",
                        "FULL",
                        "LIMITED"]
                    }
                }
            },
            ...
        },
        ...
        "app_policies": {
            "<PUT_APP_ID_HERE>": {
                "keep_context": false,
                "steal_focus": false,
                "priority": "NONE",
                "default_hmi": "NONE",
                "groups": ["Base-4", "EncryptedAddCommand"],
                "RequestType": [],
                "RequestSubType": [],
                "encryption_required": true
            },
            ...
        }
```

## Additional Resources

- [Android Encryption Guide](https://smartdevicelink.com/en/guides/android/other-sdl-features/encryption/)
- [iOS Encryption Guide](https://smartdevicelink.com/en/guides/ios/other-sdl-features/encryption/)
