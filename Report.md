# Security1-Miniproject

## Vulnerabilities

### SQL-Injection

* Admin Authentication and Authorization
  * Login: `' OR '1'='1`
  * Password: `' OR '1'='1`

### Brute force

- Expose less information about a user when trying to sign up. Only enforce unique usernames, and not unique passwords.
The application should NOT tell the user wether or not the password is in use..
  - Attacker knows if username is taken.
  - Attacker knows if password exists in database.
