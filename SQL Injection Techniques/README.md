# Welcome
Welcome to **SQL Injection Techniques**.

## Instructions
**Note**: Throughout this live hacking lab, please feel free to raise your hand for assitance. A lab technician or presenter would be happy to assist.
1. Please connect to the **Human** VM in Hyper-V.
2. The password for bob is `demoAdmin!`
3. Navigate to `http://localhost:3000`
    1. It is also bookmarked on Edge as "OWASP Juice Shop". It may take a few minutes for this to start up.
4. In a new tab, open ZTW26 GitHub.
    1. It is also bookmarked on Edge as "ZTW26 GitHub".
5. We will be using a self-hosted version of OWASP Juice Shop. On your own time, you can use online versions such as [this](https://juice-shop.herokuapp.com/#/) or [this](https://demo.owasp-juice.shop/#/).
    1. For now, please make sure to use the self-hosted version as the online one will not be able to sustain the load of everyone using it.

## Relevant facts
1. [MITRE](https://cwe.mitre.org/top25/archive/2025/2025_cwe_top25.html) has CWE-89 (SQL Injection) as the **2nd most dangerous** software weakness of 2025.
2. There are [~20,000 CVEs](https://cvedaily.com/pages/tags/sql.html) for SQLi.
3. Many major breaches occured due to SQLi, including [2008 Heartland Payment Systems](https://www.twingate.com/blog/tips/Heartland%20Payment%20Systems-data-breach), [2011 Sony Pictures](https://www.bbc.com/news/business-13636704), [2023 MOVEit Transfer](https://www.cisa.gov/news-events/cybersecurity-advisories/aa23-158a), and many more.

## Resetting the application (if required)
**Note**: Feel free to follow these steps or request assistance if your application is not working for any reason. You *shouldn't* need to do this, but the steps are included in case of an emergency.
1. Close/stop `Node.js` in Task Manager.
2. Delete `C:\users\bob\downloads\juice\shop\data\juiceshop.sqlite`
3. In the `shop` folder, run `npm start`
4. Open the Juice Shop and press the `Delete cookie to clear hacking progress` button at the top.
5. Repeat step 3.

### Friendly reminder: This lab is for hands-on red team training in approved environments only. Make sure you have permission before using these techniques anywhere else.