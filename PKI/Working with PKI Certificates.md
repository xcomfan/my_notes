
# Getting the details of a certificate

`openssl x509 -noout -subject -enddate -serial -in mycert.pem`

# Verifying key works on certificate

```bash
openssl rsa -in privatekey.key -noout -modulus
openssl x509 -in certificate.crt -noout -modulus
```

# Generating a CSR

Create a Json file with the details of your CSR 

```json
{
	"hosts": [
				"alt-name-1.exmple.com",
				"alt-name-2.exmple.com",
				"alt-name-3.exmple.com",
				],
	"CN": "testing.example.com",
	"key": {
		"algo": "rsa",
		"size": 2048
	},
	"names": [
		{
			"C": "US",
			"L": "Palo Alto",
			"O": "Example",
			"OU": "Information Technology",
			"ST": "California"
		}
	]
}
```

From the json file above generate the CSR with the command:

```bash
cfssl genkey csr_content.json | cfssljson -bare
```

This will generate the `.csr` file as well as a `.key.pem` file. If you need to encrypt the key file with a passphrase use the command.

```bash
openssl rsa -aes256 -in my_cert.key.pem -out my_cert.key.enc.pem
```

