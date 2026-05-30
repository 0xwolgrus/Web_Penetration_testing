# Authentication vulnerabilities
## Vulnerabilities in passwod based login
### Username enumeration
#### Simple Idea
The website needs to know whether the username you entered exists.
#### Attacker Point of view
if the site behaves differently when you guess a real username versus a fake one, an attacker can use that difference to figure out valid usernames.
</br>
That is username enumeration.
</br>
In very simple words "The website accidently reveals which username are real."
### Why this matters
Once an attacker know a username is valid they have a target.Without that knowledge about username validity brute force attack is mostly blind guessing.
With that knowledge attackers can :
- focus on real accounts only
- reduce noise
- speed up password attacks
- improve credential stuffing
- avoide wasting time on fake users
### The Core Concept
A login form usually have two checks.
- does username exists
- is password is correct
A secure system should respond in such a way that does not reveal which of those two checks failed.
How the leak happens
### Different response timing
Sometimes the site says the same thing for every failure:
> Invalid login credentials
That sounds safe. But if the site takes longer for a valid username than for an invalid one, attackers can still infer validity.
**Why?**
Because the server might:
1. Check whether the username exists.
2. Then compare the password hash only if the username is real.

That extra work creates a timing difference.me
- Incorrect password
</br>
That instantly tells the attacker:
“this username does not exist”
or “this username does exist, but the password was wrong”
That is a classic username enumeration leak.
