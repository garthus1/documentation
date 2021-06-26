# Generating SSL Keys

# Prerequisites

* - A workstation and a server running Rocky Linux with:
* - _OpenSSL_ installed on the machine that you will be generating the private key and CSR, as well as on the server where you will eventually be installing your key and certificates.
* - Ability to run commands comfortably from the command line.
* - And knowledge of SSL and OpenSSL commands.



# Introduction

Nearly every website today _should_ be running with an SSL (secure socket layer) certificate. This procedure will guide you by generating the private key for your website and then, from this, generating the CSR (Certificate Signing Request) that you will use to purchase your new certificate.

## Generate The Private Key

SSL private keys can have different sizes, measured in bits, determining how hard they are to crack or break.

As of 2021, the recommended private key size for a website is still 2048 bits. You can go higher, but doubling the key size from 2048 bits to 4096 bits is only about 16% more secure, takes more space to store the key, and causes higher CPU loads when the key is processed. 

It slows down your website performance without gaining any significant security. Stick with the 2048 key size for now and always keep tabs on what the current recommendations are.

Install OpenSSL on both your workstation and server:

`dnf install openssl`

Your system will install openSSL and any needed dependencies.

Our example domain is ourownwiki.com. Keep in mind that you would need to purchase and register your domain ahead of time. You can buy domains through various "Registrars." 

If you are not running your DNS (Domain Name System), you can often use the same providers for DNS hosting. DNS translates your named domain to numbers (IP addresses, IPv4 or IPv6) that the Internet can understand. These IP addresses will be the website address or URL.

Now generate the key using openSSL:

`openssl genrsa -des3 -out ourownwiki.com.key.pass 2048`

Note that we named the key with a .pass extension. That's because as soon as we execute this command, it requests that you enter a passphrase. Enter a simple passphrase that you can remember as we are going to be removing this shortly:

```
Enter pass phrase for ourownwiki.com.key.pass:
Verifying - Enter pass phrase for ourownwiki.com.key.pass:
```

Next, let's remove that passphrase. The reason for this is that if you don't remove it, each time your web server restarts and loads up your key, you will need to enter that passphrase. 

You may not even be around to enter it, or worse, you might not have a console at the ready to enter it. Remove it now to avoid all of that:

`openssl rsa -in ourownwiki.com.key.pass -out ourownwiki.com.key`

A prompt will ask you to remove the passphrase from the key:

`Enter pass phrase for ourownwiki.com.key.pass:`

When you enter the PassPhrase a third time, it will be removed by the system from the Key file and saved as ourownwiki.com.key.

## Generate the CSR

Next, we need to generate the CSR (certificate signing request) that we will use to purchase our certificate. 

A prompt appears during the generation of the CSR for several pieces of information. These are the X.509 attributes of the certificate. 

OOne of the prompts will be for "Common Name (e.g., YOUR name)." This field should be filled in with the fully qualified domain name of the server to be protected by SSL. If the website to be covered will be https://www.ourownwiki.com, then enter www.ourownwiki.com at this prompt:

`openssl req -new -key ourownwiki.com.key -out ourownwiki.com.csr`

This opens up a dialog:

`Country Name (2 letter code) [XX]:` enter the two-character country code where your site resides, for example, "US."
`State or Province Name (full name) []:` enter the full official name of your state or province,  for example, "Nebraska."
`Locality Name (e.g., city) [Default City]:` enter the full city name, for example, "Omaha."
`Organization Name (e.g., company) [Default Company Ltd]:` If you want, you can enter an organization that this domain is a part of, or just hit 'Enter' to skip.
`Organizational Unit Name (e.g., section) []:` This would describe the division of your organization. Again, you can just hit 'Enter' to skip.
`Common Name (e.g., your name or your server's hostname) []:` Here, we have to enter our site hostname, example "www.ourownwiki.com"
`Email Address []:` This field is optional, you can decide to fill it out or just hit 'Enter' to skip.

A prompt appears to enter extra attributes, which can be skipped by hitting 'Enter' through both:

```
Please enter the following 'extra' attributes
to be sent with your certificate request
A challenge password []:
An optional company name []:
```

Now you should have generated your CSR. 

## Purchasing The Certificate

Each certificate vendor will have the same procedure. You purchase the SSL and term (1 or 2 years, etcetera), and then you submit your CSR. Now you will need to use the `more` Command and then Copy the contents of your CSR file. 

`more ourownwiki.com.csr`

Which will show you something like this:

```
-----BEGIN CERTIFICATE REQUEST-----
MIICrTCCAZUCAQAwaDELMAkGA1UEBhMCVVMxETAPBgNVBAgMCE5lYnJhc2thMQ4w
DAYDVQQHDAVPbWFoYTEcMBoGA1UECgwTRGVmYXVsdCBDb21wYW55IEx0ZDEYMBYG
A1UEAwwPd3d3Lm91cndpa2kuY29tMIIBIjANBgkqhkiG9w0BAQEFAAOCAQ8AMIIB
CgKCAQEAzwN02erkv9JDhpR8NsJ9eNSm/bLW/jNsZxlxOS3BSOOfQDdUkX0rAt4G
nFyBAHRAyxyRvxag13O1rVdKtxUv96E+v76KaEBtXTIZOEZgV1visZoih6U44xGr
wcrNnotMB5F/T92zYsK2+GG8F1p9zA8UxO5VrKRL7RL3DtcUwJ8GSbuudAnBhueT
nLlPk2LB6g6jCaYbSF7RcK9OL304varo6Uk0zSFprrg/Cze8lxNAxbFzfhOBIsTo
PafcA1E8f6y522L9Vaen21XsHyUuZBpooopNqXsG62dcpLy7sOXeBnta4LbHsTLb
hOmLrK8RummygUB8NKErpXz3RCEn6wIDAQABoAAwDQYJKoZIhvcNAQELBQADggEB
ABMLz/omVg8BbbKYNZRevsSZ80leyV8TXpmP+KaSAWhMcGm/bzx8aVAyqOMLR+rC
V7B68BqOdBtkj9g3u8IerKNRwv00pu2O/LOsOznphFrRQUaarQwAvKQKaNEG/UPL
gArmKdlDilXBcUFaC2WxBWgxXI6tsE40v4y1zJNZSWsCbjZj4Xj41SB7FemB4SAR
RhuaGAOwZnzJBjX60OVzDCZHsfokNobHiAZhRWldVNct0jfFmoRXb4EvWVcbLHnS
E5feDUgu+YQ6ThliTrj2VJRLOAv0Qsum5Yl1uF+FZF9x6/nU/SurUhoSYHQ6Co93
HFOltYOnfvz6tOEP39T/wMo=
-----END CERTIFICATE REQUEST-----
```

You want to copy everything, including the "BEGIN CERTIFICATE REQUEST" and "END CERTIFICATE REQUEST" lines. Then paste these into the CSR field on the website where you are purchasing the certificate.

You may have to perform other verification steps, depending on ownership of the domain, the registrar you are using, etc., before your certificate is issued. When it is Issued, it comes with an intermediate certificate from the provider, which you will use in the configuration.

# Conclusion

Generating all of the bits and pieces to purchase a website certificate is not difficult. The Systems Administrator or Website Administrator will do this following the above procedure.If you have any questions please see the Forums for help.

