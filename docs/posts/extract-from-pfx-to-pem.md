---
title: Extract from PFX to PEM
date: 2024-01-17
authors:
    - jorge
categories:
    - Snippets
tags:
    - linux
    - openssl
    - pfx
    - pem
    - extract
    - certificates
---

Multiple times I've had to extract certificates from PFX files, and have them
converted to PEM. This little script does everything:


```bash
#!/bin/bash
# Script to extract certificate files from PFX
# Jorge Enrique Barrera <jorge@fbarr.net>

FILENAME=$1

if [ -f "$1" ]
    then
      echo -n "Domain: "
      read DOMAIN

      echo -n "Password: "
      read -s PASSWORD

      # Extract the key from the PFX file
      openssl pkcs12 -in $FILENAME -nocerts -nodes -password pass:$PASSWORD | sed -ne '/-BEGIN PRIVATE KEY-/,/-END PRIVATE KEY-/p' > $DOMAIN-key.pem

      # Extract the certificate from the PFX file
      openssl pkcs12 -in $FILENAME -clcerts -nokeys -password pass:$PASSWORD | sed -ne '/-BEGIN CERTIFICATE-/,/-END CERTIFICATE-/p' > $DOMAIN-cer.pem

      # Extract CA from PFX file
      openssl pkcs12 -in $FILENAME -cacerts -nokeys -chain -password pass:$PASSWORD | sed -ne '/-BEGIN CERTIFICATE-/,/-END CERTIFICATE-/p' > $DOMAIN-ca.pem

      echo ""
    else
      echo "File name not provided"
fi        
```
