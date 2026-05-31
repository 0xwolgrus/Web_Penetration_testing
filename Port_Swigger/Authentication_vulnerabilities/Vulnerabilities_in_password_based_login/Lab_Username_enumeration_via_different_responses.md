# PortSwigger Web Security Academy - Username Enumeration via Different Responses

## Lab Information

**Lab:** Username Enumeration via Different Responses
**Difficulty:** Apprentice
**Category:** Authentication Vulnerabilities

## Objective

The goal of this lab is to:

1. Enumerate a valid username.
2. Brute-force the corresponding password.
3. Successfully log in and access the user's account page.

---

## Vulnerability Overview

This lab is vulnerable to **username enumeration** and **password brute-force attacks**.

The application returns different responses depending on whether:

* The username is invalid.
* The username is valid but the password is incorrect.

This behavioral difference allows an attacker to identify valid usernames before attempting a password brute-force attack.

---

## Reconnaissance

After intercepting the login request using Burp Suite, the login form was identified as accepting the following parameters:

```http
POST /login HTTP/1.1

username=<username>
password=<password>
```

The lab provided two wordlists:

* `Authentication_lab_usernames.txt`
* `Authentication_lab_passwords.txt`

These files were downloaded and prepared for use in Burp Intruder.

---

## Phase 1: Username Enumeration

### Intruder Configuration

**Attack Type:** Cluster Bomb

Although Cluster Bomb supports multiple payload positions, only the username payload was actively used during this phase.

### Payload Setup

#### Payload Position 1

* Parameter: `username`
* Payload Source: `Authentication_lab_usernames.txt`

#### Payload Position 2

* Parameter: `password`
* Fixed Value:

```text
12345678
```

### Resource Pool Configuration

```text
Maximum Concurrent Requests: 50
```

### Response Analysis

Under:

```text
Intruder → Settings → Grep - Match
```

All default entries were removed and the following string was added:

```text
Invalid username
```

The attack was launched against the username wordlist.

### Result

Most responses contained:

```text
Invalid username
```

However, one response differed and did not contain this string.

Valid username identified:

```text
arizona
```

Found at:

```text
Index 84
```

This confirmed that the application behaved differently when a valid username was supplied.

---

## Phase 2: Password Brute Force

After identifying a valid username, a second Intruder attack was performed.

### Payload Setup

#### Username

Fixed value:

```text
arizona
```

#### Password

Payload Source:

```text
Authentication_lab_passwords.txt
```

### Configuration

The same Intruder settings were retained:

```text
Maximum Concurrent Requests: 50
```

Grep-Match configuration remained unchanged.

### Result

A response with different behavior was observed.

Valid credentials discovered:

```text
Username: arizona
Password: football
```

Password located at:

```text
Index 14
```

---

## Successful Authentication

Using the discovered credentials:

```text
Username: arizona
Password: football
```

Login was successful and access to the account page was obtained, solving the lab.

---

## Key Learning Points

### Username Enumeration

Different application responses can reveal whether a username exists.

Common indicators include:

* Different error messages
* Different status codes
* Different response lengths
* Different response timings

### Brute Force Efficiency

Enumerating valid usernames significantly reduces the attack surface because password guessing can be focused only on confirmed accounts.

### Burp Suite Techniques Used

* Burp Intruder
* Payload Positions
* Cluster Bomb Attack Type
* Grep-Match Response Analysis
* Resource Pool Optimization

---

## Final Credentials

```text
Username: arizona
Password: football
```

## Lab Status

✅ Solved
