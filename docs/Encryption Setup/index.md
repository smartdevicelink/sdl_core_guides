## Generate Self Signed Certificate

Create private key:
```
openssl genrsa -out client.key 2048
```

Create CSR:
```
openssl req -new -key client.key -days 10000 -out client.req -subj '/C=US/ST=MI/L=Detroit/O=SDL/OU=HeadUnit/CN=client/emailAddress=sample@sdl.com'
```

Create Public Certificate:
```
openssl x509 -hash -req -in client.req -signkey client.key -out client.cert -days 10000
```

## Configure SDL Core

Copy client.key and client.cert into your SDL Core build/bin directory. Delete any existing keys, certs, or pem files.

In your build/bin directory run:
```
c_rehash .
```

Open the config file smartDeviceLink.ini.

If you are using self signed certificates set verifyPeer to false.
```
; Verify Mobile app certificate (could be used in both SSLMode Server and Client)
VerifyPeer  = false
```

Update the names of your certificate and private key in the config file
```
; Certificate and key path to pem file
CertificatePath = client.cert
KeyPath         = client.key
```

## RPC Message Encryption

Below is a possible policy table configuration for requiring a functional group of RPCs to require encryption. Update the sdl_preloaded_pt.json with values similar to these:

```
...
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
            }
```

