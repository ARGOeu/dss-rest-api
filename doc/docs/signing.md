# PDF Signing API Calls

Signing a PDF requires to execute the 3 following API Calls.

## POST - Compute the digest to be signed

### Request

<b>POST</b> @ <i>/services/rest/signature/one-document/getDataToSign</i>

### Request Body without timestamp

- `signingCertificate.encodedCertificate` :  Embed the signing certificate 
and its chain to the signature document.
This should match one of the key entries from [keys](keys.md)
- `toSignDocument.bytes` : PDF document to be signed in base64 encoded format

```json
{
  "parameters" : {
    "signatureLevel" : "PAdES_BASELINE_B",
    "signaturePackaging" : "ENVELOPED",
    "signatureAlgorithm" : "RSA_SHA256",
	"encryptionAlgorithm" : "RSA",
    "digestAlgorithm" : "SHA256"
    ,
    "signingCertificate" : {
      "encodedCertificate" : "MIIC6j...."
    }
  },
  "toSignDocument" : {
    "bytes" : "JVBERi0xLjM...",
    "name" : "important.pdf"
  }
}
```

### Request Body with timestamp

<b><i>In case we also need to provide a timestamp within the signature, we need to 
provide the  following extra parameters.</i></b>

- `signatureLevel` becomes `PAdES_BASELINE_T`
-  we need the extra JSON objects of `contentTimestampParameters` and `signatureTimestampParameters`.

```json
{
  "parameters" : {
    "signatureLevel" : "PAdES_BASELINE_T",
    "signaturePackaging" : "ENVELOPED",
    "signatureAlgorithm" : "RSA_SHA256",
	"encryptionAlgorithm" : "RSA",
    "digestAlgorithm" : "SHA256",
    "contentTimestampParameters" : {
      "digestAlgorithm" : "SHA256",
      "canonicalizationMethod" : "http://www.w3.org/2001/10/xml-exc-c14n#"
    },
    "signatureTimestampParameters" : {
      "digestAlgorithm" : "SHA256",
      "canonicalizationMethod" : "http://www.w3.org/2001/10/xml-exc-c14n#"
    },
    "signingCertificate" : {
      "encodedCertificate" : "MIIC6j...."
    }
  },
  "toSignDocument" : {
    "bytes" : "JVBERi0xLjM...",
    "name" : "important.pdf"
  }
}
```

### Response Body

The response contains the digest that will later be signed.

```json
{
    "bytes": "MYHXM...."
}
```

## POST - Sign the computed Digest

### Request

- `{key-alias}` example value: `certificate`
- `{algorithm}` example value: `SHA256`

<b>POST</b> @ <i>/services/rest/server-signing/sign/{key-alias}/{algorithm}</i>

### Request Body

`bytes`: The computed digest

```json
{
    "bytes": "MYHXM...."
}    
```

### Response Body

`value`: The signed digest

```json
{
    "algorithm": "RSA_SHA256",
    "value": "IWbu5V.."
}
```

## POST - Sign the Document

### Request Body without timestamp

<b>POST</b> @ <i>/services/rest/signature/one-document/signDocument</i>

### Request Body
- `signingCertificate.encodedCertificate` :  Embed the signing certificate 
and its chain to the signature document.
This should match one of the key entries from [keys](keys.md)
- `signatureValue.value` : The signed computed digest
- `toSignDocument`: The original PDF Document in base64 encoded format

```json
{
  "parameters" : {
    "signingCertificate" : {
      "encodedCertificate" : "MIIC6..."
    },
    "signatureLevel" : "PAdES_BASELINE_B",
    "signaturePackaging" : "ENVELOPED",
    "signatureAlgorithm" : "RSA_SHA256",
    "digestAlgorithm" : "SHA256",
    "encryptionAlgorithm" : "RSA"
  },
  "signatureValue" : {
    "algorithm" : "RSA_SHA256",
    "value" : "IWbu5..."
  },
  "toSignDocument" : {
    "bytes" : "JVBER....",
    "name" : "important.pdf"
  }
}
```

### Request Body with timestamp

<b><i>In case we also need to provide a timestamp within the signature, we need to 
provide the  following extra parameters.</i></b>

- `signatureLevel` becomes `PAdES_BASELINE_T`
-  we need the extra JSON objects of `contentTimestampParameters` and `signatureTimestampParameters`.

```json
{
  "parameters" : {
    "signingCertificate" : {
      "encodedCertificate" : "MIIC6..."
    },
    "signatureLevel" : "PAdES_BASELINE_T",
    "signaturePackaging" : "ENVELOPED",
    "signatureAlgorithm" : "RSA_SHA256",
    "digestAlgorithm" : "SHA256",
    "encryptionAlgorithm" : "RSA",
    "contentTimestampParameters" : {
      "digestAlgorithm" : "SHA256",
      "canonicalizationMethod" : "http://www.w3.org/2001/10/xml-exc-c14n#"
    },
    "signatureTimestampParameters" : {
      "digestAlgorithm" : "SHA256",
      "canonicalizationMethod" : "http://www.w3.org/2001/10/xml-exc-c14n#"
    }
  },
  "signatureValue" : {
    "algorithm" : "RSA_SHA256",
    "value" : "IWbu5..."
  },
  "toSignDocument" : {
    "bytes" : "JVBER....",
    "name" : "important.pdf"
  }
}
```

### Response Body

```json
{
    "bytes": "JVBERi...",
    "name": "important-signed-pades-baseline-b.pdf"
}
```