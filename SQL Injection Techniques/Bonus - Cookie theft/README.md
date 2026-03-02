## Instructions:

### Steal the cookies of a user
Note: This is not an SQLi attack, but it is *highly* relevant in today's world.
1. Log in as any user.
    1. This will be the **victim**.
2. Open Developer Tools and navigate to the `Application` tab.
    1. (Ctrl + shift + i) or (Right click -> Inspect).
3. On the left pane, select `Storage` -> `Cookies` -> `http://localhost:3000` -> `token`.
4. Open an incognito window and log in as a *different* account (either through the techniques from earlier or by making a new one).
    1. This will be the **attacker**.
    2. (Ctrl + shift + n) or (Edge's Options menu -> New InPrivate Window).
5. Repeat steps 2–3 in this new window.
6. On the **victim**, copy the value of the **token**.
7. On the **attacker**, overwrite your token value with the victim's copied token.
8. Refresh the page.
    1. You are now logged in as the victim! No password, no MFA.
For more information on this, see ZTW25 hacking lab found [here](https://github.com/ztwAdmin/ZTW-2025/tree/main/Advanced%20Cookie%20Theft).