This is a very important backend concept because now you are extending the validation framework yourself instead of only using built-in validations like:

- `@NotBlank`
- `@Email`
- `@Size`

You created your own:

```
@Lowercase
```

Let’s understand the entire flow deeply.

---

# 1. What Is a Custom Validator?

A custom validator allows you to create your own validation rule.

Example:

```
@Lowercaseprivate String email;
```

Meaning:

> “This field value must be lowercase.”

Spring/Jakarta Validation does not provide this built-in, so you create it yourself.

---

# 2. Your Custom Annotation

```
@Target(ElementType.FIELD)@Retention(RetentionPolicy.RUNTIME)@Constraint(validatedBy = LowercaseValidator.class)public @interface Lowercase {    String message() default "must be lowercase";    Class<?>[] groups() default {};    Class<? extends Payload>[] payload() default {};}
```

This annotation defines the validation rule metadata.

---

# 3. Understanding Each Part

---

## `@Target(ElementType.FIELD)`

This tells Java:

> “This annotation can only be used on fields.”

Example:

```
@Lowercaseprivate String email;
```

Allowed ✅

But this:

```
@Lowercasepublic void test()
```

Not allowed ❌

---

## `@Retention(RetentionPolicy.RUNTIME)`

This tells Java:

> “Keep this annotation available at runtime.”

Why?

Because Spring validation works during application runtime.

Without this:

Spring cannot detect your annotation while validating.

---

# 4. Most Important Part

```
@Constraint(validatedBy = LowercaseValidator.class)
```

This connects:

```
@Lowercase
```

TO

```
LowercaseValidator
```

Meaning:

> “Whenever `@Lowercase` is used, execute `LowercaseValidator`.”

This is the heart of custom validation.

---

# 5. Your Validator Class

```
public class LowercaseValidator       implements ConstraintValidator<Lowercase, String> {
```

This means:

```
ConstraintValidator<AnnotationType, FieldType>
```

So here:

|Generic|Meaning|
|---|---|
|`Lowercase`|annotation handled|
|`String`|field type handled|

Meaning:

> This validator validates `String` fields annotated with `@Lowercase`.

---

# 6. The `isValid()` Method

```
@Overridepublic boolean isValid(        String value,        ConstraintValidatorContext context)
```

This method contains the validation logic.

---

# 7. What Is `value`?

Suppose request:

```
{  "email": "HELLO@gmail.com"}
```

And DTO:

```
@Lowercaseprivate String email;
```

Then:

```
value = "HELLO@gmail.com"
```

Spring automatically passes the field value here.

---

# 8. Return Value Meaning

## Return `true`

Validation passed ✅

---

## Return `false`

Validation failed ❌

Then Spring throws:

```
MethodArgumentNotValidException
```

Just like built-in validations.

---

# 9. Your Current Logic

You wrote:

```
if(value == null) return true;return false;
```

Meaning:

|Value|Result|
|---|---|
|`null`|valid|
|anything else|invalid|

So currently:

```
"hello"
```

fails.

```
"HELLO"
```

fails.

Everything except `null` fails.

---

# 10. Correct Lowercase Validation

You probably want:

```
@Overridepublic boolean isValid(        String value,        ConstraintValidatorContext context) {    if(value == null) {        return true;    }    return value.equals(value.toLowerCase());}
```

---

# 11. How This Works

Example:

```
value = "hello@gmail.com"
```

Then:

```
value.toLowerCase()
```

returns:

```
"hello@gmail.com"
```

Comparison:

```
value.equals(value.toLowerCase())
```

TRUE ✅

---

Example 2:

```
value = "Hello@gmail.com"
```

`toLowerCase()` returns:

```
"hello@gmail.com"
```

Comparison fails ❌

---

# 12. How Spring Executes This

Suppose DTO:

```
public class RegisterRequest {    @Lowercase    private String email;}
```

Request:

```
{  "email": "HELLO@gmail.com"}
```

Flow:

```
Request JSON    ↓@RequestBody converts JSON → DTO    ↓@Valid starts validation    ↓Spring sees @Lowercase    ↓Looks at @Constraint(validatedBy = LowercaseValidator.class)    ↓Creates LowercaseValidator object    ↓Calls isValid(value, context)    ↓Returns false    ↓Validation fails    ↓MethodArgumentNotValidException thrown
```

---

# 13. The `message()` Property

```
String message() default "must be lowercase";
```

This becomes validation error message.

Example response:

```
{  "email": "must be lowercase"}
```

---

# 14. Why `groups()` and `payload()` Exist

These are required by Jakarta Validation.

Almost all custom validators include them.

Usually beginners don't use them.

---

## `groups()`

Used for advanced grouped validation.

---

## `payload()`

Used for attaching metadata.

Mostly enterprise/advanced usage.

---

# 15. Real Usage Example

DTO:

```
public class RegisterUserRequest {    @Lowercase    @Email    private String email;}
```

Request:

```
{  "email": "HELLO@GMAIL.COM"}
```

Response:

```
{  "email": "must be lowercase"}
```

---

# 16. Why `null` Returns `true`

This is intentional.

Because null-check responsibility usually belongs to:

```
@NotNull
```

or

```
@NotBlank
```

So validators normally validate format/rules only.

Example:

```
@NotBlank@Lowercaseprivate String email;
```

Now:

|Validation|Responsibility|
|---|---|
|`@NotBlank`|field must exist|
|`@Lowercase`|must be lowercase|

This separation is good design.

---

# 17. Final Correct Version

## Annotation

```
package com.pravin.shopping_cart.validation;import jakarta.validation.Constraint;import jakarta.validation.Payload;import java.lang.annotation.*;@Target(ElementType.FIELD)@Retention(RetentionPolicy.RUNTIME)@Constraint(validatedBy = LowercaseValidator.class)public @interface Lowercase {    String message() default "must be lowercase";    Class<?>[] groups() default {};    Class<? extends Payload>[] payload() default {};}
```

---

## Validator

```
package com.pravin.shopping_cart.validation;import jakarta.validation.ConstraintValidator;import jakarta.validation.ConstraintValidatorContext;public class LowercaseValidator        implements ConstraintValidator<Lowercase, String> {    @Override    public boolean isValid(            String value,            ConstraintValidatorContext context    ) {        if(value == null) {            return true;        }        return value.equals(value.toLowerCase());    }}
```

---

# 18. Big Picture

You have now created:

✅ custom annotation  
✅ custom validation logic  
✅ integration with Spring Validation  
✅ reusable validation rule