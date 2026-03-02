## Instructions:

### Log in as the admin
1. Create an account. Do **NOT** use a sensitive password. This is an intentionally insecure web application.
2. Open Developer Tools and navigate to the `Network` tab.
    1. (Ctrl + shift + i) or (Right click -> Inspect).
3. Log in as the newly created user.
4. Select the `login` entry and navigate to the `Payload` tab.
    1. Note that the credentials are sent in plaintext. This is both *very* insecure and indicative of being weak to SQLi attacks.
5. Log out as the user and navigate to the login page. 
    1. For ease of use, you can select `Clear network log` at the top left of the Developer Tools. It's a circle with a line through it, to the right of the red stop.
6. Try to log in again as your user, but put a `'` at the end of your email.
7. Find the new login log and navigate to the `Preview` tab.
8. (Expand the logs as necessary) We can see the SQL response and can confirm that our input is not being sanitized.
    1. For example, mine is `SELECT * FROM Users WHERE email = 'Kieran.Human@threatlocker.com'' AND password = '87ee6e7066409c68016963f2dcb0b388' AND deletedAt IS NULL`
9. Now we just need to modify the SQL to log us in as the first user.
10. 
    <details>
    <summary><strong>Click here to view the solution.</strong></summary>

    1. Enter `' OR 1=1--` into the email field:
    2. The password field can contain anything.

    This makes the `WHERE` to evaluate as `TRUE` and comments out the rest of the query. Meaning that it will return the first user in the database (which just so happens to have a UserId as 1).
    </details>
11. Navigate to the [administration page](http://localhost:3000/#/administration).
    1. Something as simple as this really happens. See [540 Million Facebook User Records Found On Unprotected Amazon Servers](https://thehackernews.com/2019/04/facebook-app-database.html). Including *plaintext* passwords.
    2. Side note: Go ahead and delete the bad reviews while you're at it. This is one reason to never blindly trust reviews on a website always verify them with a third party site, like [this](https://www.g2.com/products/threatlocker-platform/reviews).
12. Proceed to *Lateral movement*.



