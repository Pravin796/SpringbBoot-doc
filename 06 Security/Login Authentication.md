  
Before Spring Security, you were doing authentication manually:

```
var user = userRepository.findByEmail(request.getEmail()).orElse(null);if(user == null){    return ResponseEntity.status(HttpStatus.UNAUTHORIZED).build();}if(!passwordEncoder.matches(request.getPassword(), user.getPassword())){    return ResponseEntity.status(HttpStatus.UNAUTHORIZED).build();}
```

Here **you** were responsible for:

1. Finding the user from DB
    
2. Checking if user exists
    
3. Comparing passwords
    
4. Returning success/failure
    

Spring Security can do all of that for you.

---

# What happens now?

When this line runs:

```
authenticationManager.authenticate(    new UsernamePasswordAuthenticationToken(        request.getEmail(),        request.getPassword()    ));
```

you are basically saying:

> "Spring Security, please authenticate this user for me."

---

# Step 1: AuthenticationManager receives request

The token contains:

```
email = "pravin@gmail.com"password = "123456"
```

Spring creates:

```
UsernamePasswordAuthenticationToken
```

which contains:

```
Principal  -> pravin@gmail.comCredentials -> 123456
```

and sends it to:

```
AuthenticationManager
```

---

# Step 2: AuthenticationManager finds AuthenticationProvider

You registered this bean:

```
@Beanpublic AuthenticationProvider authenticationProvider() {    DaoAuthenticationProvider provider =            new DaoAuthenticationProvider(userDetailsService);    provider.setPasswordEncoder(passwordEncoder());    return provider;}
```

So Spring knows:

> "Use DaoAuthenticationProvider for username/password authentication."

Flow:

```
AuthenticationManager        |        vDaoAuthenticationProvider
```

---

# Step 3: DaoAuthenticationProvider loads user

The provider does NOT use your repository directly.

Instead it calls:

```
userDetailsService.loadUserByUsername(email);
```

This is why Spring Security requires a `UserDetailsService`.

Something like:

```
@Service@AllArgsConstructorpublic class CustomUserDetailsService        implements UserDetailsService {    private final UserRepository userRepository;    @Override    public UserDetails loadUserByUsername(String email)            throws UsernameNotFoundException {        User user = userRepository                .findByEmail(email)                .orElseThrow(                        () -> new UsernameNotFoundException("User not found")                );        return new CustomUserDetails(user);    }}
```

Notice:

```
UserRepository
```

is still used.

But now:

```
Controller   ↓AuthenticationManager   ↓DaoAuthenticationProvider   ↓UserDetailsService   ↓UserRepository
```

instead of directly inside the controller.

---

# Step 4: UserDetailsService returns UserDetails

Example:

```
return User.builder()        .username(user.getEmail())        .password(user.getPassword())        .authorities("ROLE_USER")        .build();
```

Spring receives:

```
username = pravin@gmail.compassword = $2a$10....
```

(the password stored in DB)

---

# Step 5: Password comparison

Now DaoAuthenticationProvider automatically does:

```
passwordEncoder.matches(    enteredPassword,    storedPassword);
```

Equivalent to:

```
passwordEncoder.matches(    "123456",    "$2a$10....");
```

because you configured:

```
provider.setPasswordEncoder(passwordEncoder());
```

which is:

```
new BCryptPasswordEncoder();
```

---

# Step 6: Success

If password matches:

```
Authentication authentication =        UsernamePasswordAuthenticationToken.authenticated(...)
```

is returned.

No exception is thrown.

Execution continues:

```
return ResponseEntity.ok().build();
```

---

# Step 7: Failure

If:

- user doesn't exist
    
- password is wrong
    

then:

```
BadCredentialsException
```

is thrown.

Your handler catches it:

```
@ExceptionHandler(BadCredentialsException.class)public ResponseEntity<Void> handleBadCredentialsException() {    return ResponseEntity.status(HttpStatus.UNAUTHORIZED).build();}
```

Response:

```
401 Unauthorized
```

---

# Complete Flow

```
POST /auth/login        |        vAuthController        |        vAuthenticationManager.authenticate()        |        vDaoAuthenticationProvider        |        vUserDetailsService.loadUserByUsername()        |        vUserRepository.findByEmail()        |        vUser found        |        vBCryptPasswordEncoder.matches()        |   +----+----+   |         |Match     No Match   |         |   v         vSuccess   BadCredentialsException   |         |   v         v200 OK    401 Unauthorized
```

So the repository is **still being used**, but indirectly through `UserDetailsService`. The controller no longer needs to know how users are stored or how passwords are checked; it delegates all of that to Spring Security's authentication infrastructure.