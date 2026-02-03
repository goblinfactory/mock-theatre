# What's the difference between a component test and an Integration Test?

Discuss

-
-

---

# Traditional Test Pyramid

<img src="/img/test-pyramid.png" class="h-80 mx-auto" />

---

# Where are the executable specifications?

<div class="grid grid-cols-2 gap-6">
<div>
<img src="/img/test-pyramid.png" class="h-80 mx-auto" />

</div>
<div>

<v-clicks>

- If we use a test pyramid then and you say, well, they are at the top, then that only gives you happy path testing.
- It doesnt make grey areas unambiguous with worked examples
- UX tests get created (most often) far too long after the work is done, and often by an entirely different team.  

</v-clicks>

</div>
</div>

---

# Testing diamond (fat middle) instead of a testing Pyramid?

 Discuss

-
- 

---

# Test Diamond

```
           /\
          /  \
         / UI \           ← Few, slow
        /______\
       /        \
      /          \
     /  Service   \       ← Many, BDD/API tests
    /    (Fat      \         (Reqnroll, SpecFlow)
   /     Middle)     \
  /__________________ \
  \                  /
   \   Unit Tests   /     ← Some, fast
    \______________/

```

---

# BDD Test libraries

- Reqnroll
- Specflow et

---

# Who should create mocks of services?

If you're dependant on an API from another team in your org, who you can talk to, who should create the service mock? (and why)

---

# Where is your team's biggest testing gaps?

(spend the bulk of the time here)

discuss, let subject matter experts share.

-
-
