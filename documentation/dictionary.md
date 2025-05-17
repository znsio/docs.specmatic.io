---
layout: default
title: Dictionary
parent: Documentation
nav_order: 8
---
# Dictionary


- [Dictionary](#dictionary)
  - [Structure](#structure)
    - [Basic Field Mapping](#basic-field-mapping)
    - [Nested Properties](#nested-properties)
    - [Handling Arrays](#handling-arrays)
      - [Referencing Elements Within the Array](#referencing-elements-within-the-array)
      - [Referencing the Entire Array](#referencing-the-entire-array)
      - [Nested properties in Arrays](#nested-properties-in-arrays)
    - [Referencing Other Schemas](#referencing-other-schemas)
  - [Dictionary with Contract Testing](#dictionary-with-contract-testing)
    - [Specification](#specification)
    - [Dictionary](#dictionary-1)
    - [Run the tests](#run-the-tests)
    - [Generative Tests](#generative-tests)
  - [Dictionary with Service Virtualization](#dictionary-with-service-virtualization)
    - [Specification](#specification-1)
    - [Dictionary](#dictionary-2)
    - [Run Service Virtualization](#run-service-virtualization)
    - [Making Requests](#making-requests)
  - [Dictionary with Examples](#dictionary-with-examples)

When Specmatic is required to generate requests for tests or responses for stubs and cannot locate any examples, it will create them utilizing the structure and keys outlined in the OpenAPI specifications.

It will fill the structure with random values based on the defined schema. Although the generated values will conform to the schema, they will not contain meaningful values that reflect the context of your business domain.

The dictionary offers a method to retrieve domain-specific values in the absence of examples. You can provide a dictionary of values to Specmatic, which will utilize this dictionary as a reference when generating requests or responses.

The dictionary can be supplied in either `YAML` or `JSON` format and should adhere to the naming convention of `<spec-file-name>_dictionary.<format>` for ease of use.

## Structure

The dictionary defines a mapping where keys represent schema fields using a syntax similar to `JSONPath`.
This structured approach enables precise field references within complex objects.

- Each entry in the dictionary maps a schema field to either a `single value` or a `list of values`.
- If a single value is specified, it will be used directly.
- If a list is specified, one value will be `pseudo-randomly` selected.

### Basic Field Mapping

For example, given the `Employee` schema as follows:

```yaml
components:
  schemas:
    Employee:
      type: object
      properties:
        name:
          type: string
        age:
          type: integer
```

Corresponding dictionary entries for the `name` and `age` properties would be:

{% tabs dictionary %}
{% tab dictionary yaml %}
```yaml
Employee.name: John # Single-value
Employee.age: # Multi-value
- 20
- 30
- 40
```
{% endtab %}
{% tab dictionary json %}
```json
{
  "Employee.name": "John",
  "Employee.age": [20, 30, 40]
}
```
{% endtab %}
{% endtabs %}

- The `name` field is supplied as a `single value` and will be used directly.
- The `age` field is supplied as a `list of values`, from which either `20`, `30`, or `40` will be `pseudo-random` selected.

### Nested Properties

When working with nested structures, the key is extended using the `.` character as a separator to represent the hierarchy of properties.<br/>
For example, given the `Employee` schema as follows:

```yaml
components:
  schemas:
    Employee:
      type: object
      required:
        - name
      properties:
        name:
          type: object
          required:
            - first_name
          properties:
            first_name:
              type: string
            last_name:
              type: string
```

Corresponding dictionary entries for the `first_name` and `last_name` property would be:

{% tabs nestedProperty %}
{% tab nestedProperty Single-Value %}
```yaml
Employee.name.first_name: John
Employee.name.last_name: Smith
```
{% endtab %}
{% tab nestedProperty Multi-Value %}
```yaml
Employee.name.first_name:
 - John
 - Jane
Employee.name.last_name:
 - Smith
 - Doe
```
{% endtab %}
{% endtabs %}

### Handling Arrays

For properties that are arrays, two referencing styles are supported, referencing the entire array or referencing elements within the array.<br/>
**Note:**: If both are specified, the key referencing the entire array will be used.

#### Referencing Elements Within the Array

To reference elements within an array, the key should be appended with `[*]`.<br/>
For instance, consider the following `Employee` schema:

```yaml
components:
  schemas:
    Employee:
      type: object
      required:
        - aliases
      properties:
        aliases:
          type: array
          items:
            type: string
```

Corresponding dictionary entries for elements within the `aliases` array would be:

{% tabs ElemWithinArray %}
{% tab ElemWithinArray Single-Value %}
```yaml
Employee.aliases[*]: John
```
The same value is repeated for each elements in the array. 
{% endtab %}
{% tab ElemWithinArray Multi-Value %}
```yaml
Employee.aliases[*]:
- John
- Jane
```
For each element in the array, a value is `pseudo-randomly` selected from the list.
{% endtab %}
{% endtabs %}

#### Referencing the Entire Array

To reference the entire array, simply omit `[*]` from the key.<br/>
For example, given the `Employee` schema as follows:

```yaml
components:
  schemas:
    Employee:
      type: object
      required:
        - aliases
      properties:
        aliases:
          type: array
          items:
            type: string
```

Corresponding dictionary entries for the `aliases` array would be:

{% tabs entireArray %}
{% tab entireArray Single-Value %}
```yaml
Employee.aliases:
- "John"
- "Jane"
```
The entire array will be used directly
{% endtab %}
{% tab entireArray Multi-Value %}
```yaml
Employee.aliases:
- ["John", "Jane"]
- ["May", "Jones"]
```
One of the nested arrays will be `pseudo-randomly` selected and used directly
{% endtab %}
{% endtabs %}

#### Nested properties in Arrays

For example, given the `Employee` schema as follows:
```yaml
components:
  schemas:
    Employee:
      type: object
      required:
        - aliases
      properties:
        aliases:
          type: array
          items:
            type: object
            required:
              - first_name
            properties:
              first_name:
```

Corresponding dictionary entries for the `first_name` property within the `aliases` array would be:

{% tabs nestedPropertyInArray %}
{% tab nestedPropertyInArray Single-Value %}
```yaml
Employee.aliases[*].first_name: "John"
```
{% endtab %}
{% tab nestedPropertyInArray Multi-Value %}
```yaml
Employee.aliases[*].first_name:
- "John"
- "Jane"
```
{% endtab %}
{% endtabs %}
> **Note:** This nesting behaves correctly because `first_name` is not defined as a top-level property in the schema

### Referencing Other Schemas

When a schema utilizes `$ref` to reference another schema, the dictionary directly accesses the fields of the referenced schema.<br/>
For example, given the following `Employee` and `Address` schemas:

```yaml
components:
  schemas:
    Employee:
      type: object
      required:
        - addresses
      properties:
        first_name:
          type: string
        addresses:
          $ref: '#/components/schemas/Address'
    Address:
      type: object
      required:
        - city
      properties:
        street:
          type: string
        city:
          type: string
```

Corresponding dictionary entries for any property defined in the `Address` schema would be:

{% tabs nestedProperty %}
{% tab nestedProperty Single-Value %}
```yaml
Address.city: "New York"
Address.street: "Broadway"
```
{% endtab %}
{% tab nestedProperty Multi-Value %}
```yaml
Address.city:
- "New York"
- "London"
Address.street:
- "Broadway"
- "High Street"
```
{% endtab %}
{% endtabs %}
> **Note:** Notice that the keys begin with `Address` instead than `Employee`, because the dictionary accesses the fields from the referenced schema.

## Dictionary with Contract Testing

Dictionary can be used with contract testing, in which case specmatic will use the values provided in the dictionary when generating requests for tests, To understand how this works, lets take a look at the following example:

### Specification 

Create an OpenApi Specification file named `employees.yaml` as follows:
```yaml
openapi: 3.0.0
info:
  title: Employees
  version: '1.0'
servers: []
paths:
  /employees:
    post:
      requestBody:
        content:
          application/json:
            schema:
              $ref: '#/components/schemas/EmployeeDetails'
      responses:
        200:
          description: Employee Created
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/Employee'

        400:
          description: Bad Request
components:
  schemas:
    Employee:
      type: object
      required:
        - id
        - name
        - department
      properties:
        id:
          type: integer
        employeeCode:
          type: string
        name:
          type: string
        department:
          type: string

    EmployeeDetails:
      type: object
      required:
        - name
        - department
      properties:
        name:
          type: string
        department:
          type: string
        employeeCode:
          type: string
```

### Dictionary 

Now create a dictionary file named `employees_dictionary.yaml` in the same directory:

```yaml
EmployeeDetails.name: John Doe
EmployeeDetails.department: IT
EmployeeDetails.employeeCode: "12345"
```

### Run the tests
Now to execute contract tests on the specification using the dictionary a service is required, we will utilize [service-virtualization](/documentation/service_virtualization_tutorial.html) for this purpose.

{% tabs test %}
{% tab test java %}
```shell
java -jar specmatic.jar stub employees.yaml
```
{% endtab %}
{% tab test npm %}
```shell
npx specmatic stub employees.yaml
```
{% endtab %}
{% tab test docker %}
```shell
docker run --rm --network host -v "$(pwd)/employees.yaml:/usr/src/app/employees.yaml" -v "$(pwd)/employees_dictionary.yaml:/usr/src/app/employees_dictionary.yaml" znsio/specmatic stub "employees.yaml"
```
{% endtab %}
{% endtabs %}

Next, execute the contract tests by running the following command:

{% tabs test %}
{% tab test java %}
```shell
java -jar specmatic.jar test employees.yaml
```
{% endtab %}
{% tab test npm %}
```shell
npx specmatic test employees.yaml
```
{% endtab %}
{% tab test docker %}
```shell
docker run --rm --network host -v "$(pwd)/employees.yaml:/usr/src/app/employees.yaml" -v "$(pwd)/employees_dictionary.yaml:/usr/src/app/employees_dictionary.yaml" znsio/specmatic test "employees.yaml
```
{% endtab %}
{% endtabs %}

We can now examine the request sent to the service by reviewing the logs.
```shell
POST /employees
Accept-Charset: UTF-8
Accept: */*
Content-Type: application/json
{
  "name": "John Doe",
  "department": "IT",
  "employeeCode": "12345"
}
```
Notice that the values from the dictionary are utilized in the requests.

### Generative Tests

As it's evident that only valid values can be included in the dictionary. hence, generative tests will ignore the values in the dictionary for the key being mutated.
The other keys will still retrieve values from the dictionary if available; otherwise, random values will be generated.

For instance, if you execute the specification with generative tests enabled, one of the request will appear as follows:
```shell
POST /employees
Accept-Charset: UTF-8
Accept: */*
Content-Type: application/json
{
  "name": null,
  "department": "IT",
  "employeeCode": "12345"
}
```

In this case, the key `name` is being mutated, which results in the value from the dictionary being disregarded.
While the values for `department` and `employeeCode` are still being retrieved from the dictionary.


## Dictionary with Service Virtualization

Dictionary can be used with service virtualization, in which case specmatic will use the values provided in the dictionary when generating responses for the incoming requests, To understand how this works, lets take a look at the following example:

### Specification
Create an OpenApi Specification file named `employees.yaml` as follows:
```yaml
openapi: 3.0.0
info:
title: Employees
version: '1.0'
servers: []
paths:
/employees:
    patch:
    summary: ''
    requestBody:
        content:
        application/json:
            schema:
            $ref: '#/components/schemas/EmployeeDetails'
    responses:
        200:
        description: Employee Created Response
        content:
            application/json:
            schema:
                $ref: '#/components/schemas/Employee'
components:
schemas:
    Employee:
    type: object
    required:
        - id
        - name
        - department
        - designation
    properties:
        id:
        type: integer
        employeeCode:
        type: string
        name:
        type: string
        department:
        type: string
        designation:
        type: string

    EmployeeDetails:
    type: object
    required:
        - name
        - department
        - designation
    properties:
        name:
        type: string
        employeeCode:
        type: string

```

### Dictionary
Now create a dictionary file named `employees_dictionary.yaml` in the same directory:

```yaml
Employee.id: 10
Employee.name: Jamie
Employee.employeeCode: pqrxyz
Employee.department: Sales
Employee.designation: Associate
```

### Run Service Virtualization

To start service virtualization, use the following command:

{% tabs test %}
{% tab test java %}
```shell
java -jar specmatic.jar stub employees.yaml
```
{% endtab %}
{% tab test npm %}
```shell
npx specmatic stub employees.yaml
```
{% endtab %}
{% tab test docker %}
```shell
docker run --rm --network host -v "$(pwd)/employees.yaml:/usr/src/app/employees.yaml" -v "$(pwd)/employees_dictionary.yaml:/usr/src/app/employees_dictionary.yaml" znsio/specmatic stub "employees.yaml"
```
{% endtab %}
{% endtabs %}

### Making Requests

Execute the following curl command:
```shell
curl -X PATCH -H 'Content-Type: application/json' -d '{"name": "Jamie", "employeeCode": "pqrxyz"}' http://localhost:9000/employees
```

You'll get the following response:
```json
{
  "id": 10,
  "name": "Jamie",
  "employeeCode": "pqrxyz",
  "department": "Sales",
  "designation": "Associate"
  }
```

**Note:** None of the values mentioned above were included in any example file. They were generated by Specmatic from the dictionary while creating a response to return.

## Dictionary with Examples

You can also utilize dictionary with the example commands and the examples interactive server, allowing values to be retrieved from the dictionary for both request and response generation of examples, refer to [External Examples](/documentation/external_examples.html) for more details.
