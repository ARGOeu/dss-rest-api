# Keys/Certificate Management

## GET - Retrieve all available certificates/keys

### Request

<b>GET</b> @ <i>/services/rest/server-signing/keys</i>

### Response body

```json
[
    {
        "alias": "certificate",
        "encryptionAlgo": "RSA",
        "certificate": {
            "encodedCertificate": "MIIC6jCC.."
        },
        "certificateChain": [
            {
                "encodedCertificate": "MIIC6jC..."
            }
        ]
    }
]
```

## GET - Retrieve a specific certificates/keys

### Request

`{alias}` example value : `certificate`

<b>GET</b> @ <i>/services/rest/server-signing/keys/{alias}</i>

### Response body

```json
{
    "alias": "certificate",
    "encryptionAlgo": "RSA",
    "certificate": {
        "encodedCertificate": "MIIC6jCCA..."
    },
    "certificateChain": [
        {
            "encodedCertificate": "MIIC6jCC..."
        }
    ]
}
```