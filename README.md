# Get-Hash-Manual
Instructions for obtaining password hashes.

# Kerberos
Kerberos v5:
	- Type 18:
		- Wireshark filter: kerberos
		- Stage 1
			- Get the first reply of the KDC. This should be a KRB Error packet type. Error KRB5KDC_ERR_PREAUTH_REQUIRED.
			- Realm = realm
			- Salt  = eTYPE-AES256-CTS-HMAC-SHA1-96 salt
		- Stage 2		
			- Get the second request from the client to the KDC. This is an AS-REQ type packet.
			- User  = KRB5-NT-PRINCIPAL
			- In the packet select the field: PA-DATA PA-ENC-TIMESTAMP
			- Check the padata-value, etype. This should be 18. eTYPE-AES256-CTS-HMAC-SHA1-96.
			- Hexhash = The cipher in hex format. (This is a 112 character string, encoding 56 bytes.)
			- John hash = User:$krb5pa$18$User$Realm$Salt$Hexhash

	- Type 23:
		- Wireshark filter: kerberos
		- Stage 1
			- Get the first reply of the KDC. This should be a KRB Error packet type. Error KRB5KDC_ERR_PREAUTH_REQUIRED.
			- Realm = realm
			- Salt  = eTYPE-ARCFOUR-HMAC-MD5 salt (should be empty)
		- Stage 2		
			- Get the second request from the client to the KDC. This is an AS-REQ type packet.
			- User  = KRB5-NT-PRINCIPAL
			- In the packet select the field: PA-DATA PA-ENC-TIMESTAMP
			- Check the padata-value, etype. This should be 23. eTYPE-ARCFOUR-HMAC-MD5.
			- Hexhash = The cipher in hex format. (This is a 104 character string, encoding 52 bytes.)
			- Move the first 32 hex hash characters to the end of the HexHash string.
			- SwitchHash = HexHash[32:] HexHash[0:32]
			- John hash = User:$krb5pa$23$User$Realm$Salt$SwitchHash
