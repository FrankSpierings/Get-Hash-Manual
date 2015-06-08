# Get-Hash-Manual
Instructions for obtaining password hashes.

### Obtaining Kerberos v5 - Type 18
- Example capture: http://openwall.info/wiki/_media/john/krbv5-pcaps.tar.gz
- Example hash (password =`password@12345678`):
`$krb5pa$18$aduser$AD.EXAMPLE.COM$AD.EXAMPLE.COMaduser$f94f755a8b4493d925094a4eb1cec630ac40411a14c9733a853516fe426637d9daefdedc0567e2bb5a83d4f89a0ad1a4b178662b6106c0ff`
- Wireshark filter: `kerberos`
- Stage 1
 - Get the first reply of the KDC. This should be a `KRB Error` packet type. Error `KRB5KDC_ERR_PREAUTH_REQUIRED`. 
 - Realm = `realm`
 - Salt  = `eTYPE-AES256-CTS-HMAC-SHA1-96` `salt`
 - If the Salt is not provided, construct the salt as follows, Salt = `Realm``User`
- Stage 2
 - Get the second request from the client to the KDC. This is an AS-REQ type packet.
 - User  = `KRB5-NT-PRINCIPAL`
 - In the packet select the field: `PA-DATA PA-ENC-TIMESTAMP`
 - Check the padata-value, etype. This should be 18. `eTYPE-AES256-CTS-HMAC-SHA1-96`.
 - Hexhash = The cipher in hex format. (This is a 112 character string, encoding 56 bytes.)
- John hash = `User:$krb5pa$18$User$Realm$Salt$Hexhash`
 

### Obtaining Kerberos v5 - Type 23
- Wireshark filter: `kerberos`
- Stage 1
 - Get the first reply of the KDC. This should be a `KRB Error` packet type. Error `KRB5KDC_ERR_PREAUTH_REQUIRED`.
 - Realm = realm
 - Salt  = `eTYPE-ARCFOUR-HMAC-MD5` salt (should be empty)
- Stage 2		
 - Get the second request from the client to the KDC. This is an `AS-REQ` type packet.
 - User  = `KRB5-NT-PRINCIPAL`
 - In the packet select the field: `PA-DATA PA-ENC-TIMESTAMP`
 - Check the `padata-value`, `etype`. This should be 23. `eTYPE-ARCFOUR-HMAC-MD5`.
 - Hexhash = The cipher in hex format. (This is a 104 character string, encoding 52 bytes.)
 - Move the first 32 hex hash characters to the end of the HexHash string.
 - SwitchHash = HexHash[32:] HexHash[0:32]
- John hash = `User:$krb5pa$23$User$Realm$Salt$SwitchHash`
