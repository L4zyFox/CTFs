## BugBountyHub

### Exception Excavation  

Designed By: MikeHacksThings - Maltek Solutions  
Points: 50 Create Task

BugBountyHub is a new platform where security researchers can report vulnerabilities. The platform looks polished, but sometimes appearances can be deceiving. Can you find a way to extract hidden information from the system?

Target URL: [https://bug-bounty-hub.ac25.apisecuniversity.com/](https://bug-bounty-hub.ac25.apisecuniversity.com/)

Solution:

A BugBounty report submission application was presented.

![](imagens/2025-05-22_21-40.png)

I created an account.

![](imagens/2025-05-22_21-41.png)

This way I got access to the application.

![](imagens/2025-05-22_21-42.png)

As the prompt already hinted, the challenge objective is to get some kind of system information through the vulnerability report.

I started using the platform, creating and viewing reports.

![](imagens/2025-05-22_21-45.png)

After using the application extensively while intercepting requests, I decided to analyze the captures.

![](imagens/2025-05-22_21-46.png)

I noticed the reports were identified by sequential numbers. When trying to access a report that did not belong to my user, a permission denied message was received.

![](imagens/2025-05-22_21-47.png)

But when trying to retrieve a report with a non-existent id, the application generated an error likely shown because the app was in debug mode. The error revealed a lot of system information: directory structure, technology versions, and, of course, the flag.

![](imagens/2025-05-22_21-49.png)

```json
flag{st4ck_tr4c3s_r3v34l_s3cr3ts}
```

### Render Bender  

Designed By: MikeHacksThings - Maltek Solutions  
Points: 150 Create Task

BugBountyHub has just launched an exciting new feature allowing researchers to customize how their vulnerability reports look using a flexible markdown template system. The developers are proud of this new rendering engine, claiming it's both powerful and secure. Can you bend the renderer to your will and make it reveal secrets it was never meant to show?

Target URL: [https://bug-bounty-hub.ac25.apisecuniversity.com/](https://bug-bounty-hub.ac25.apisecuniversity.com/)

Solution:

Here the prompt hints that users can customize their report templates with markdown.

![](imagens/2025-05-22_21-57.png)

I first tried inserting simple python payloads to check if they were actually executed.

```
{{7*7}}
```

![](imagens/2025-05-22_21-59.png)

You can see that the payload was executed.

![](imagens/2025-05-22_22-00.png)

With that, I used a payload to try to retrieve environment information from the application.

```python
{{ cycler.__init__.__globals__['os'].environ }}
```

This made it possible to view many application details, including the flag.

![](imagens/2025-05-22_22-06.png)

```json
flag{t3mpl4t3_1nj3ct10n_ftw}
```

### Secret Hunter  

Designed By: MikeHacksThings - Maltek Solutions  
Points: 250 Create Task

The BugBountyHub developers have implemented a new security feature - they've moved their most sensitive secrets to a separate location, accessible only with the right credentials. Your previous exploits revealed some interesting information, but can you leverage what you've learned to access the ultimate secret?

Target URL: [https://bug-bounty-hub.ac25.apisecuniversity.com/](https://bug-bounty-hub.ac25.apisecuniversity.com/)

Solution:

The prompt comments on information revealed in the previous challenge, so I tried to check what interesting environment parameters the system had.

I found 3 interesting parameters:

- endpoint: /api/local/secrets
- api_key: api_key_8675309
- api_token: ``eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJzdWIiOiJhZG1pbiIsImlhdCI6MTY3NjQ4MjQ5MH0.8K-ojK5VQiwpMNu_hRXYQTlW3WVH1Zq9U7ueznPvTq4``

When trying to access the identified endpoint, an error was shown informing that api_key and token were required.

![](imagens/2025-05-22_22-10.png)

POST method is not accepted, only GET, so I assumed these parameters needed to be passed via URL.

```
GET /api/local/secrets?token=eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJzdWIiOiJhZG1pbiIsImlhdCI6MTY3NjQ4MjQ5MH0.8K-ojK5VQiwpMNu_hRXYQTlW3WVH1Zq9U7ueznPvTq4&api_key=api_key_8675309 HTTP/2
```

With this, the flag was displayed.

![](imagens/2025-05-22_22-15.png)

```json
flag{ch41n1ng_vuln3r4b1l1t13s_f0r_th3_w1n}
```
