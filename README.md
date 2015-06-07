# Get-Hash-Manual
Instructions for obtaining password hashes.

# Kerberos
Kerberos v5:
	* Type 18:
		* Wireshark filter: kerberos
		* Stage 1
			* Get the first reply of the KDC. This should be a KRB Error packet type. Error `KRB5KDC_ERR_PREAUTH_REQUIRED`.
