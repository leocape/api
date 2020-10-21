# Errors

Error Code | Meaning
---------- | -------
4XX | returned for malformed requests - the issue is on the sender's side.
403 | returned when the WAF Limit (Web Application Firewall) has been violated.
429 | returned when breaking a request rate limit.
5XX | returned for internal errors; the issue is on deployment side. It is important to NOT treat this as a failure operation; the execution status is UNKNOWN and could have been a success.
