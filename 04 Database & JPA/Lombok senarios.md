**How does default value work for `List<Address> addresses = new ArrayList<>();` when using `@Builder.Default`?**

Let’s break it slowly.

---

## Your code

@OneToMany(mappedBy = "user")  
@Builder.Default  
private List<Address> addresses = new ArrayList<>();

This line actually has **two responsibilities**:

1️⃣ JPA relationship  
2️⃣ Default value when using Lombok Builder

We focus on the default part 👇

---

## First understand the BIG problem 🚨

When Lombok generates a Builder, it **ignores field initialization**.

Without `@Builder.Default`, the builder internally does this:

User user = User.builder().build();

Internally becomes:

new User(null, null, null, null, null);

So this field becomes:

addresses = null ❌

Now what happens when you call:

user.address(new Address());

Inside your method:

addresses.add(address);

Boom 💥 **NullPointerException**

Because:

addresses == null

This is exactly why `@Builder.Default` is VERY important for collections.

---

## What `@Builder.Default` actually generates internally

Your code:

@Builder.Default  
private List<Address> addresses = new ArrayList<>();

Lombok secretly generates something like this:

public User build() {  
    List<Address> addresses =  
        this.addresses != null ? this.addresses : new ArrayList<>();  
    return new User(addresses);  
}

So when builder does NOT set addresses → Lombok creates new ArrayList automatically.

Meaning:

User user = User.builder().build();

Now internally becomes:

user.addresses = new ArrayList<>(); ✅

NOT null anymore.

---

## Why collections MUST have default value in JPA ⚠️

In JPA entities, collections should **never be null**.

Best practice:

private List<Address> addresses = new ArrayList<>();

Because we always do operations like:

- add()
- remove()
- size()

If list is null → app crashes.

So this line ensures:

- Builder safe ✔
- JPA safe ✔
- No NullPointerException ✔

---

## Now your helper method makes sense ❤️

public void address(Address address){  
    addresses.add(address);  
    address.setUser(this);  
}

This method maintains **bidirectional relationship**.

When you add address to user:

- Add address to list
- Set user inside address

So both sides stay in sync.

Usage:

User user = User.builder()  
        .name("Pravin")  
        .build();  
  
Address addr = new Address();  
user.address(addr);

Thanks to `@Builder.Default` → `addresses` is already an empty list.

So this works perfectly ✅

---

## Real-world analogy 🏠

Think of `addresses` like a bag.

Without `@Builder.Default`  
User gets **no bag** → can't put anything → crash 😵

With `@Builder.Default`  
User gets **empty bag** → can add addresses anytime 🙂

---

## Interview gold line ⭐

When using Lombok `@Builder` with collections in JPA entities, always use:

@Builder.Default  
private List<Entity> list = new ArrayList<>();

Otherwise builder creates **null collections**.