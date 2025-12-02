# iam-leaks
A security research project documenting real-world AWS IAM misconfigurations, leaked policies, overly permissive roles, and common exposure patterns. The goal is to help defenders understand and prevent IAM-related risks through responsible analysis.

## History of AWS IAM Leaks
| # | Entity / Victim | Incident Date | Impact | Primary Cause | Reference |
|---|------------------|----------------|---------|----------------|-----------|
| 1 | Uber (2014) | May 2014 | 50k driver records | Hardcoded AWS creds in GitHub | https://www.reuters.com/... |
| 2 | Code Spaces | Jun 2014 | Entire AWS infra wiped | Attacker-created IAM keys | https://threatpost.com/... |
| 3 | BrowserStack | Nov 2014 | User data stolen | AWS keys on compromised server | https://threats.wiz.io/... |
| 4 | DataDog | Jun 2016 | EC2 & S3 breached | Compromised AWS + SSH keys | https://www.clouddefense.ai/... |
| 5 | Uber (2016) | Late 2016 | 57M records stolen | GitHub AWS key exposure | https://www.huntress.com/... |
| 6 | Lynda.com | Dec 2016 | 9.5M user records | AWS key exposed on GitHub | https://www.forbes.com/... |
| 7 | Imperva | Oct 2018 | Customer data exposed | AWS API keys on public server | https://www.crn.com/... |
| 8 | Drizly | 2018/2020 | Crypto mining | Long-exposed GitHub AWS key | https://www.akingump.com/... |
| 9 | Chtrbox | 2019 | 49M influencer records | Public S3 bucket | https://www.darkreading.com/... |
| 10 | Nature’s Basket | 2019 | Full account compromise | Hardcoded AWS keys in bucket | https://www.resourcely.io/... |
| 11 | Starbucks | 2019 | Configs exposed | Hardcoded keys in GitHub repo | https://hackerone.com/... |
| 12 | Capital One | Jul 2019 | 100M+ records | SSRF → IMDSv1 escalation | https://dl.acm.org/... |
| 13 | Prestige Software | 2020 | 10M travel records | Open S3 credentials | https://www.idtheftcenter.org/... |
| 14 | Wholee | May 2021 | AWS backend exposed | Hardcoded AWS keys | https://thehackernews.com/... |
| 15 | Oven Story Pizza | May 2021 | Backend AWS access | Hardcoded AWS keys | https://thehackernews.com/... |
| 16 | Ubiquiti | Apr 2021 | Insider data theft | Excessive AWS admin access | https://www.justice.gov/... |
| 17 | LogicGate | Jun 2021 | 40k records | S3 backup credential leak | https://www.hipaajournal.com/... |
| 18 | FlexBooker | 2021 | 3.7M users | S3 misconfiguration | https://haveibeenpwned.com/... |
| 19 | Juspay | 2021 | Card + PII exposed | Old AWS key not rotated | https://www.linkedin.com/... |
| 20 | GoDaddy | 2021/2018 | SSL private keys exposed | Unsecured S3 bucket | https://www.appviewx.com/... |
| 21 | Apache Airflow | Oct 2021 | AWS creds leaked | Misconfigured Airflow | https://www.zdnet.com/... |
| 22 | Twitch | Oct 2021 | 125GB source leaked | Misconfigured server | https://solutionsreview.com/... |
| 23 | Codecov | 2021 | 29k env vars leaked | Malicious supply-chain injection | https://www.cisa.gov/... |
| 24 | PyPI ctx / PHP phpass | May 2022 | AWS key exfiltration | Malicious packages | https://thehackernews.com/... |
| 25 | Nvidia | Feb 2022 | 1TB of data stolen | Hardcoded keys in repo | https://www.deepwatch.com/... |
| 26 | Glovo (Vendor) | May 2022 | AWS/MySQL access | Hardcoded credentials | https://hackerone.com/... |
| 27 | Developers via ctx/phpass | May 2022 | Thousands of keys stolen | Package exfiltration | https://thehackernews.com/... |
| 28 | Travis CI | 2022 | 73k tokens exposed | Secrets printed in logs | https://www.aquasec.com/... |
| 29 | LastPass | Aug–Oct 2022 | S3 backups accessed | DevOps credential theft | https://www.cybersecuritydive.com/... |
| 30 | SCARLETEEL | Feb 2023 | Cross-account movement | IMDSv1 + exposed IAM keys | https://cystack.net/... |
| 31 | Legion Malware | Apr 2023 | AWS/DynamoDB creds stolen | .env exposure | https://thehackernews.com/... |
| 32 | American Vision Partners | Mar 2024 | 2.35M medical records | Exposed AWS keys | https://topclassactions.com/... |
| 33 | HPE Aruba | 2024 | Telemetry compromise | AWS firmware keys exposed | https://arcticwolf.com/... |
| 34 | Pic Stitch | 2024 | Backend access | Hardcoded AWS keys | https://www.heise.de/... |
| 35 | Crumbl | 2024 | IoT backend exposure | Hardcoded AWS keys | https://cyberpress.org/... |
| 36 | Mozilla Common Voice | 2024 | AWS/db access | Docker config leak | https://hackerone.com/... |
| 37 | Clubfactory | 2021 | TBs of S3 data exposed | Hardcoded AWS keys | https://www.techtarget.com/... |
| 38 | Banking Apps (SDK) | 2021–2022 | 300k biometrics leaked | Hardcoded SDK creds | https://danaepp.com/... |
| 39 | Expel Case Study 4 | Jan 2023 | SES abuse | Exposed Postman server key | https://cystack.net/... |
| 40 | Videoshop (App #1) | Oct 2024 | Backend access risk | Unencrypted AWS creds | https://www.theregister.com/... |
| 41 | Eureka | Oct 2024 | Unauthorized access | Hardcoded AWS keys | https://www.theregister.com/... |
| 42 | Solitaire Clash | Oct 2024 | Backend access | Hardcoded AWS keys | https://www.bleepingcomputer.com/... |
| 43 | Videoshop (App #2) | 2024 | Backend access | Hardcoded AWS keys | — |
| 44 | Meru Cabs | Oct 2024 | Storage exposed | Hardcoded cloud creds | https://www.scworld.com/... |
| 45 | Pearson | Mar 2025 | Legacy student data stolen | AWS creds in source code | https://www.bleepingcomputer.com/... |
| 46 | Salesforce (via Drift) | 2025 | Keys from 700+ orgs | OAuth token abuse | https://www.valencesecurity.com/... |
| 47 | Badshah SaaS Company | 2025 | Full infra deletion | Leaked AdminAccess IAM key | https://threats.wiz.io/... |
| 48 | Uber (2022) | 2022 | Internal lateral movement | Hardcoded keys + Lapsus$ | https://ironscales.com/... |
| 49 | Tata Motors | Oct 2025 | Vehicle infra exposure | Hardcoded AWS keys | https://techcrunch.com/... |
| 50 | Onus | Dec 2021 | 2M records leaked | AWS creds in config file | https://cystack.net/... |

