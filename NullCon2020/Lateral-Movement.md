## Owner:

> CaptainFreak

#### Solved?

 - No

#### Tried:

 > No, less on hacker numbers, we are recruiting :)

#### Solutions:

The challenge was to exlpoit SSRF in A custom HTTP header. Exploit to get metadata, Move laterraly in AWS cloud and get the flag from S3 bucket.

Steps to solve the challenge:

1. Get the endpoint and required Header and body from `script.js - buildActionRequestfunction`
2. Verify the SSRF in `X-Tunnel-To` header by loopback request.
3. Try getting AWS metadata - Bypass the blacklist with any SSRF bypass trick like redirection.
3. Get the AWS credentials.
4. Use pacu(RhinoSecurityLabs/pacu) AWS exploitation framework to exploit with creds.
5. Enumerate AWS users on the account. `pooruser` 
6. Creds have policy to change user login password.
7. Change password using `iam__privesc_scan` of pacu.
8. Login to the user with new password on aws web console.
9. Find out that user has s3 related access.
10. Find the `flag.txt` in `nullcon2020` S3 bucket.

In a nutshell:

Lateral movement in the AWS cloud account.
