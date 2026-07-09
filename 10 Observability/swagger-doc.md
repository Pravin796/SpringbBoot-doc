# What is Swagger?

Imagine you've built an API like this:

POST /users
GET /users
GET /users/{id}
PUT /users/{id}
DELETE /users/{id}
Now another developer wants to use your API.

They'll ask questions like:

What endpoints exist?

Which HTTP method should I use?

What request body should I send?

What response will I get?

Which fields are required?

What are the error codes?

Does it require authentication?

Without documentation, you'll probably send them a PDF, Postman collection, or explain everything manually.

That's difficult to maintain.

Swagger solves this problem.

It automatically generates beautiful API documentation from your Spring Boot code.

Example:

GET /products

Description:
Returns all products.

Response:

[
   {
      "id":1,
      "name":"Laptop",
      "price":50000
   }
]
And you can even click Try it Out to call the API directly from the browser.

Is Swagger and OpenAPI the same?
Not exactly.

People often use the names interchangeably, but they're different.

OpenAPI
   ↑
Specification (Rules)

Swagger
   ↑
Tools that implement OpenAPI
Think of it like:

Java
↓

Spring Boot
Java is the language.

Spring Boot is a framework built on Java.

Similarly,

OpenAPI = Specification

Swagger = Tools
What does Swagger provide?
Mainly two things.

1. Swagger UI
A webpage that shows your APIs.

Example:

GET /products

POST /products

PUT /products/{id}

DELETE /products/{id}
Click any endpoint.

It shows

Description

Parameters

Request Body

Response

Error codes

You can even execute the API.

2. OpenAPI JSON
Behind the scenes Swagger generates a huge JSON file.

/v3/api-docs
Example

{
  "paths": {
    "/products": {
      "get": {
        "summary": "Get all products"
      }
    }
  }
}
Swagger UI simply reads this JSON and displays it nicely.

So the flow is:

Spring Boot Code
        ↓
OpenAPI JSON
        ↓
Swagger UI
Why do companies use Swagger?
Suppose the backend team builds

GET /products
The frontend team doesn't need to ask for documentation.

They simply open

http://localhost:8080/swagger-ui/index.html
Everything is there.

Benefits:

No manual documentation

Always up to date

Can test APIs

Shows request/response models

Easy for frontend, QA, and mobile developers

Adding Swagger to Spring Boot
Since you're using Spring Boot 4.x, the commonly used library is springdoc-openapi (the old Springfox project is no longer recommended).

Add the dependency to your pom.xml:

<dependency>
    <groupId>org.springdoc</groupId>
    <artifactId>springdoc-openapi-starter-webmvc-ui</artifactId>
    <version>2.8.13</version>
</dependency>
(Use the latest compatible version for your Spring Boot version if a newer one is available.)

Restart your application.

Open Swagger
Run the project.

Visit

http://localhost:8080/swagger-ui/index.html
You'll see something like:

Product Controller

GET /products

POST /products

GET /products/{id}
It detects your controllers automatically.

Example Controller
Suppose you have

@RestController
@RequestMapping("/products")
public class ProductController {

    @GetMapping
    public List<ProductDto> getAllProducts() {
        return service.getAllProducts();
    }

}
Swagger automatically creates documentation.

You didn't write anything extra.

But the documentation isn't very descriptive...
It might show something like:

GET /products

Description:
No description
That's why we use Swagger annotations.

@Operation
Used to describe an API.

@Operation(
    summary = "Get all products",
    description = "Returns a list of all available products."
)
@GetMapping
public List<ProductDto> getAllProducts() {
    return service.getAllProducts();
}
Swagger now shows

GET /products

Summary
Get all products

Description
Returns a list of all available products.
@Parameter
Documents parameters.

@GetMapping("/{id}")
public ProductDto getProduct(
        @Parameter(description = "Product ID")
        @PathVariable Long id
) {
    return service.getProduct(id);
}
Swagger displays

Parameter

id

Description:
Product ID
@RequestBody
Suppose

@PostMapping
public ProductDto createProduct(
        @RequestBody ProductRequest request
) {
    ...
}
Swagger automatically shows the request body.

But you can improve it with:

@Operation(summary = "Create Product")
@Schema
Used for model documentation.

Instead of

public class ProductDto {

    private Long id;
    private String name;
    private BigDecimal price;

}
Write

public class ProductDto {

    @Schema(description = "Product ID", example = "1")
    private Long id;

    @Schema(description = "Product Name", example = "Laptop")
    private String name;

    @Schema(description = "Price", example = "49999")
    private BigDecimal price;

}
Swagger displays

id
Product ID

Example
1

name
Laptop

price
49999
Much more informative.

@ApiResponse
Describes responses.

@Operation(summary = "Find Product")
@ApiResponses({
    @ApiResponse(responseCode = "200", description = "Product Found"),
    @ApiResponse(responseCode = "404", description = "Product Not Found")
})
@GetMapping("/{id}")
public ProductDto getProduct(@PathVariable Long id) {
    ...
}
Swagger shows

Responses

200 OK

404 Not Found
Authentication (JWT)
Later, after documenting APIs, you can configure Swagger to send a JWT.

A button appears:

Authorize 🔒
Click it.

Paste

Bearer eyJhbGciOiJIUzI1Ni...
Now every request includes

Authorization:
Bearer eyJhbGciOiJIUzI1Ni...
This is very useful when testing secured APIs.

Typical annotations you'll use
Annotation	Purpose
@Operation	Describe an endpoint
@Parameter	Describe path/query/header parameters
@Schema	Document model fields
@ApiResponse	Document responses
@ApiResponses	Group multiple responses
@Tag	Group related APIs in Swagger
Learning roadmap
I recommend learning Swagger in this order:

✅ What Swagger/OpenAPI is

✅ Add Swagger to a Spring Boot project

✅ Explore the generated Swagger UI

Learn @Operation

Learn @Schema

Learn @Parameter

Learn @ApiResponse and @ApiResponses

Group controllers using @Tag

Add JWT authentication to Swagger

Customize API information (title, version, contact, license)

Organize documentation for production-ready APIs

Since you've already built a Shopping Cart application with controllers, DTOs, validation, and Spring Security, we can document a real controller from your project step by step. That will show not only what each annotation does, but also where it belongs and why it's useful in a production codebase.


