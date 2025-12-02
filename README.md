# iam-leaks
A security research project documenting real-world AWS IAM misconfigurations, leaked policies, overly permissive roles, and common exposure patterns. The goal is to help defenders understand and prevent IAM-related risks through responsible analysis.

## History of AWS IAM Leaks
| # | Entity / Victim | Incident Date | Impact | Primary Cause | Reference |
|---|------------------|----------------|---------|----------------|-----------|
| 1 | Uber (2014) | May 2014 | 50k driver records | Hardcoded AWS creds in GitHub | https://www.reuters.com/article/uk-uber-tech-lyft-hacking-exclusive/exclusive-in-lawsuit-over-hacking-uber-probes-ip-address-assigned-to-lyft-exec-sources-idUKKCN0S20D020151008/ |
| 2 | Code Spaces | Jun 2014 | Entire AWS infra wiped | Attacker-created IAM keys | https://threatpost.com/hacker-puts-hosting-service-code-spaces-out-of-business/106761/ |
| 3 | BrowserStack | Nov 2014 | User data stolen | AWS keys on compromised server | https://threats.wiz.io/all-incidents/browserstack-data-breach |
| 4 | DataDog | Jun 2016 | EC2 & S3 breached | Compromised AWS + SSH keys | https://www.clouddefense.ai/breaches/2016/datadog-2016#google_vignette |
| 5 | Uber (2016) | Late 2016 | 57M records stolen | GitHub AWS key exposure | https://www.huntress.com/threat-library/data-breach/uber-data-breach |
| 6 | Lynda.com | Dec 2016 | 9.5M user records | AWS key exposed on GitHub | https://www.forbes.com/sites/leemathews/2016/12/19/9-5-million-users-warned-after-lynda-com-breach/ |
| 7 | Imperva | Oct 2018 | Customer data exposed | AWS API keys on public server | https://www.crn.com/news/security/imperva-breach-exposed-api-keys-ssl-certs-for-some-firewall-users |
| 8 | Drizly | 2018 / 2020 | Crypto mining | Long-exposed GitHub AWS key | https://www.akingump.com/en/insights/blogs/ag-data-dive/ftc-takes-rare-step-in-bringing-an-enforcement-action-against-drizly-and-its-ceo |
| 9 | Chtrbox | 2019 | 49M influencer records | Public S3 bucket | https://www.darkreading.com/cloud-security/49-million-instagram-influencer-records-exposed-in-open-database |
| 10 | Nature’s Basket | 2019 | Full account compromise | Hardcoded AWS keys in bucket | https://www.resourcely.io/post/incident-review-misconfigured-aws-infra |
| 11 | Starbucks | 2019 | Configs exposed | Hardcoded keys in GitHub repo | https://hackerone.com/reports/716292 |
| 12 | Capital One | Jul 2019 | 100M+ records | SSRF → IMDSv1 escalation | https://dl.acm.org/doi/full/10.1145/3546068 |
| 13 | Prestige Software | 2020 | 10M travel records | Open S3 credentials | https://www.idtheftcenter.org/post/prestige-softwares-unsecured-database-exposes-10-million-travel-website-accounts/ |
| 14 | Wholee | May 2021 | AWS backend exposed | Hardcoded AWS keys | https://thehackernews.com/2021/05/over-40-apps-with-more-than-100-million.html |
| 15 | Oven Story Pizza | May 2021 | Backend AWS access | Hardcoded AWS keys | https://thehackernews.com/2021/05/over-40-apps-with-more-than-100-million.html |
| 16 | Ubiquiti | Apr 2021 | Insider data theft | Excessive AWS admin access | https://www.justice.gov/usao-sdny/pr/former-employee-technology-company-charged-stealing-confidential-data-and-extorting |
| 17 | LogicGate | Jun 2021 | 40k records | S3 backup credential leak | https://www.hipaajournal.com/risk-and-compliance-firm-reports-breach-of-47035-records/ |
| 18 | FlexBooker | 2021 | 3.7M users | S3 misconfiguration | https://haveibeenpwned.com/Breach/FlexBooker |
| 19 | Juspay | 2021 | Card + PII exposed | Old AWS key not rotated | https://www.linkedin.com/news/story/juspay-hit-by-massive-data-breach-4290153/ |
| 20 | GoDaddy | 2021 / 2018 | SSL private keys exposed | Unsecured S3 bucket | https://www.appviewx.com/blogs/go-daddy-data-breach-exposes-data-of-1-2-million-customers/ |
| 21 | Apache Airflow | Oct 2021 | AWS creds leaked | Misconfigured Airflow | https://www.zdnet.com/article/misconfigured-old-airflow-instances-leak-slack-aws-credentials/ |
| 22 | Twitch | Oct 2021 | 125GB source leaked | Misconfigured server | https://solutionsreview.com/security-information-event-management/twitch-suffers-massive-data-leak-entire-source-code-reportedly-stolen/ |
| 23 | Codecov | 2021 | 29k env vars leaked | Malicious supply-chain injection | https://www.cisa.gov/news-events/alerts/2021/04/30/codecov-releases-new-detections-supply-chain-compromise |
| 24 | PyPI ctx / PHP phpass | May 2022 | AWS key exfiltration | Malicious packages | https://thehackernews.com/2022/05/malicious-pypi-and-php-packages.html |
| 25 | Nvidia | Feb 2022 | 1TB of data stolen | Hardcoded keys in repo | https://www.deepwatch.com/labs/nvidia-confirms-data-was-stolen-lapsus-takes-credit/ |
| 26 | Glovo (3rd party) | May 2022 | AWS/MySQL access possible | Hardcoded creds in vendor files | https://hackerone.com/reports/1580567 |
| 27 | Developers via ctx/phpass | May 2022 | Thousands of keys stolen | Package exfiltration | https://thehackernews.com/2022/05/malicious-pypi-and-php-packages.html |
| 28 | Travis CI | 2022 | 73,000+ tokens exposed | Secrets printed in logs | https://www.aquasec.com/blog/travis-ci-security/ |
| 29 | LastPass | Aug–Oct 2022 | S3 vault backups accessed | DevOps credential theft | https://www.cybersecuritydive.com/news/lastpass-cyberattack-timeline/643958/ |
| 30 | SCARLETEEL | Feb 2023 | Cross-account movement | IMDSv1 + exposed IAM keys | https://cystack.net/research/the-attack-on-onus-a-real-life-case-of-the-log4shell-vulnerability |
| 31 | Legion Malware | Apr 2023 | AWS/DynamoDB creds stolen | .env exposure | https://thehackernews.com/2023/04/new-legion-malware-version-targets-ssh.html |
| 32 | American Vision Partners | Mar 2024 | 2.35M medical records | Exposed AWS creds | https://topclassactions.com/lawsuit-settlements/privacy/data-breach/consumers-file-class-actions-over-american-vision-partners-data-breach/ |
| 33 | HPE Aruba | 2024 | Telemetry compromise | Hardcoded AWS firmware keys | https://arcticwolf.com/resources/blog/cve-2024-42509-cve-2024-47460/ |
| 34 | Pic Stitch | 2024 | Backend access | Hardcoded AWS keys | https://www.heise.de/en/news/Millions-of-installed-apps-contain-hard-coded-cloud-accesses-9993330.html |
| 35 | Crumbl | 2024 | IoT backend risk | Hardcoded AWS keys | https://cyberpress.org/hardcoded-credentials-found-in-top-android-ios-apps/ |
| 36 | Mozilla Common Voice | 2024 | Unauthorized AWS/db access | Docker config leak | https://hackerone.com/reports/2401648 |
| 37 | Clubfactory | 2021 | TBs of exposed S3 data | Hardcoded AWS keys | https://www.techtarget.com/searchsecurity/news/252500361/Popular-mobile-apps-leaking-AWS-keys-exposing-user-data |
| 38 | Banking Apps (SDK) | 2021–2022 | 300k biometric records leaked | Hardcoded SDK credentials | https://danaepp.com/hardcoded-cloud-creds-prove-its-easy-for-api-hackers-to-win |
| 39 | Expel Case Study 4 | Jan 2023 | SES abuse | Exposed Postman server AWS keys | https://cystack.net/research/the-attack-on-onus-a-real-life-case-of-the-log4shell-vulnerability |
| 40 | Videoshop (App #1) | Oct 2024 | Backend access risk | Unencrypted AWS creds | https://www.theregister.com/2024/10/23/android_ios_security/ |
| 41 | Eureka | Oct 2024 | Unauthorized access | Hardcoded AWS keys | https://www.theregister.com/2024/10/23/android_ios_security/ |
| 42 | Solitaire Clash | Oct 2024 | Backend access | Hardcoded AWS keys | https://www.bleepingcomputer.com/news/security/aws-azure-auth-keys-found-in-android-and-ios-apps-used-by-millions/ |
| 43 | Videoshop (App #2) | 2024 | Backend access | Hardcoded AWS keys | (no direct link provided) |
| 44 | Meru Cabs | Oct 2024 | Storage exposed | Hardcoded cloud creds | https://www.scworld.com/brief/hardcoded-cloud-credential-exposure-prevalent-in-android-ios-apps |
| 45 | Pearson | Mar 2025 | Legacy student data stolen | AWS creds in source code | https://www.bleepingcomputer.com/news/security/education-giant-pearson-hit-by-cyberattack-exposing-customer-data/ |
| 46 | Salesforce (via Drift) | 2025 | AWS keys from 700+ orgs | OAuth token abuse / SOQL mining | https://www.valencesecurity.com/resources/blogs/salesforce-oauth-token-breach-what-every-security-team-must-know |
| 47 | Badshah SaaS Company | 2025 | Full infra deletion | Leaked AdminAccess IAM key | https://threats.wiz.io/all-incidents/aws-breach-at-a-saas-company |
| 48 | Uber (2022) | 2022 | Internal lateral movement | Hardcoded keys + Lapsus$ social engineering | https://ironscales.com/blog/ransomware-gangs-lapsus |
| 49 | Tata Motors | Oct 2025 | Vehicle infra exposure | Hardcoded AWS keys | https://techcrunch.com/2025/10/28/tata-motors-confirms-it-fixed-security-flaws-that-exposed-company-and-customer-data/ |
| 50 | Onus | Dec 2021 | 2M records leaked | AWS creds in config file | https://cystack.net/research/the-attack-on-onus-a-real-life-case-of-the-log4shell-vulnerability |
