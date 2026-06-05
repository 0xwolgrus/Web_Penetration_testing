# PortSwigger Lab Writeup: Username Enumeration via Subtly Different Responses

## Overview

This PortSwigger lab demonstrates how a small difference in application responses can expose valid usernames and enable password brute-force attacks.

The task was to:

1. Enumerate a valid username.
2. Brute-force the password for that username.
3. Log in to the account.
4. Access the account page and complete the lab.

---

## Vulnerability Type

This lab is vulnerable to:

* **Username Enumeration**
* **Authentication Weakness**
* **Brute-Force Attack**

The weakness exists because the application does **not** handle all failed login attempts in the same way. A valid username produces a different response pattern than an invalid one.

In this lab, the most useful indicator was **response length**.

---

## What Was the Hint?

The subtle difference was not a loud error message. The hint was in the **length of the server response**.

During the password attack:

* A **correct password** produced a response length of about **188**
* An **incorrect password** produced a response length of about **3000+**

That difference made it easy to identify the successful login attempt.

This is the key weakness: the application leaked information through response size.

---

## Tools Used

* **Burp Suite Intruder**
* Username wordlist
* Password wordlist

---

## Attack Strategy

I solved the lab in two separate phases:

1. **Find the valid username**
2. **Find the correct password for that username**

I used **Sniper** attack mode in Burp Suite because I was testing one input field at a time.

---

## Sniper vs Cluster Bomb

### Sniper

**Sniper** attacks one payload position at a time.

It is useful when:

* only one field needs to be tested
* you want to replace a single parameter with a wordlist
* you are checking one input for unusual behavior

**In this lab:**

* first, I used Sniper on the **username** field
* then, I used Sniper on the **password** field

### Cluster Bomb

**Cluster Bomb** tests multiple payload positions together.

It is useful when:

* two or more fields must be tested at the same time
* you want Burp to try every combination of values

**Simple difference:**

* **Sniper = one field at a time**
* **Cluster Bomb = many fields together in combinations**

For this lab, Sniper was the best choice because the username and password were tested in two separate stages.

---

## Step 1: Prepare the Wordlists

The lab provided two wordlists:

* Candidate usernames
* Candidate passwords

I downloaded both lists and saved them as `.txt` files so they could be loaded into Burp Suite Intruder.

---

## Step 2: Enumerate the Username

I opened the login request in **Burp Suite Intruder** and set the **username** parameter as the payload position.

Then I loaded the username list and started the attack.

Burp Suite showed a list of responses for each username attempt. Most responses looked similar, but one entry stood out because its response behavior was different.

The valid username was:

```text
alaska
```

This happened because the application treated the valid username differently from invalid ones.

---

## Step 3: Brute-Force the Password

After finding the valid username, I performed a second Intruder attack.

This time I:

* fixed the username to `alaska`
* loaded the password wordlist as the payload list

Burp Suite then tested each password against the account.

The successful password was identified by the **response length**:

* **Correct password:** about `188`
* **Incorrect password:** about `3000+`

That size difference made the successful request easy to spot.

The correct password was:

```text
1234567
```

---

## Step 4: Log In

Using the discovered credentials, I logged in successfully:

* **Username:** `alaska`
* **Password:** `1234567`

After login, I reached the account page.

---

## Step 5: Update the Email Address

On the account page, there was an option to update the email address.

The page also showed the previous email address:

```text
alaska@normal-user.net
```

I entered my own email address:

```text
example@student.uet.edu.pk
```

After submitting the form, the email was replaced successfully.

This confirmed full access to the account and completed the lab.

---

## Why This Attack Worked

This attack worked because the application leaked information through **subtle response differences**.

Instead of returning the same response for every failed login, it revealed:

* whether a username exists
* whether a password attempt is closer to success
* which request produced a smaller response size

That small leak was enough to:

1. identify a valid username
2. focus brute-force attempts on one account
3. detect the correct password using response length

---

## Important Security Lesson

Authentication systems should not reveal whether:

* a username is valid
* a password is close to correct
* a login attempt failed for a different reason

They should return a generic response for all failures.

A secure login form should avoid differences in:

* message text
* response length
* response timing
* status code
* page layout

Even a tiny difference can be used for enumeration.

---

## Final Result

I successfully:

* enumerated the username `alaska`
* brute-forced the password `1234567`
* logged into the account
* updated the email address
* completed the lab

---

## Conclusion

This lab showed how a very small response difference can become a serious authentication flaw.

The attack was possible because the application leaked useful information through **response length**, which allowed easy identification of the correct password.

That is why login systems must be designed to respond consistently and reveal as little as possible.
