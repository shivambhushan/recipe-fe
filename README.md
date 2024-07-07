# HOW TO RUN
## 1 Recipe Service - python
1. Run docker: From the root of the project => Run docker-compose up -d to start the database

2. Install python requirements: From the root of the project => Run the below commands
    - pip install virtualenv
    - python/python3 -m virtualenv env 
    - Windows: env\Scripts\activate or Linux: source env/bin/activate
    - pip install -r requirements.txt

3. Run the recipe service: python/python3 app.py

## 2 Recipe Selection React App
1. From the root of the project => Install dependencies => npm install

2. Start the development server: npm start

## Developer review
I had fun doing this test. I have tried to cover all the edge cases mentioned.

## Missing Requirements
- Unit testing and test suite 
  - Unit Testing:
    - Write unit tests for individual components or functions.
    - Ensure high code coverage.
  - Integration Testing:
    - Test interactions between different modules or services.
    - Ensure data integrity and correct API responses.
  - User Acceptance Testing (UAT):
    - Conduct UAT with stakeholders to ensure the system meets their expectations.
    - Gather feedback and make necessary adjustments.
  - Performance Testing:
    - Test the system’s performance under various loads.
    - Identify and address any bottlenecks.


> By following this implementation plan, we can systematically approach your project and ensure all critical aspects are covered.


## OVERVIEW

Welcome, and thank you for applying to HelloFresh. For this step of your hiring journey, we have prepared a full-stack coding challenge that will help you to demonstrate your technical skills.

The Recipe Selection Page is the core experience of our digital product. Every week millions of users enter this page to select the recipes they'll receive at their homes.

For this challenge, we want you to get familiar with our day-to-day, so we have provided a minimalistic implementation of our Recipe Selection Page, some of the main components from our Design System, and a set of tasks for you.

Please treat this test as if you were part of a team and you have to tackle this feature request; the change would be reviewed by one of your teammates and would go to production.

## INSTRUCTIONS

1. Clone this repository and install the dependencies using `yarn`
2. Create a new branch named dev `git checkout -b dev`
3. Complete the tasks described in this document by applying your solution and committing code.
4. Once you finish, create a pull request to the master branch of this repository.
5. After creating your pull request, please send the link via email to the recruiter you're in touch with.


## RECOMMENDATIONS

*   For this challenge, data should be provided by a backend service and **stored in a database**.
*   This repository is based on create-react-app, you can use `yarn start` to run the application.
*   In the initial state of the application, we provide you a custom hook, **useFetchHelloFreshBox**, that simulates data fetching from a remote server. It is a simple timeout function that returns an object.
*   As this is a **full-stack challenge**, we expect you to replace the empty data set by the **useFetchHelloFreshBox** with data coming from the **recipes-service** you will build.
*   **Do not eject** the application.
*   Feel free to **change any file, variable, or value** to achieve your solution.
*   **If you run out of time, write a clear implementation plan of any missing requirements** 

### OUR DEVELOPMENT VISION

At Hellofresh we value the best experience for our users as well as our engineers when coding. We consider a good development process one that

* considers people's disabilities and limitations
* is well documented and has maintainability in mind
* has a good version control and traceability 
* applies the best standards of javascript
* considers today's performance requirements
* is self protected and well tested
* knows the standards of a theme library
* applies functional programming to its best

We hope you can share the same vision as we do, as well as showcase your own expertise and knowledge! As well as have fun coding this test!

### Database setup
To save time, we have provided a docker-compose to setup the database and prefill it with sample data.

From the root of the project:
* Run `docker-compose up -d` to start the database

If you can't use Make or Docker feel free to setup the database manually and prefill it with the data defined in `/src/db`
```sql
MySQL 8.1.0

CREATE DATABASE IF NOT EXISTS recipes_service;
USE recipes_service;

CREATE USER 'recipe_db_user'@'localhost' IDENTIFIED BY 'hellofresh';
GRANT ALL ON recipes_service.* TO 'recipe_db_user'@'localhost';

create table product
(
    id                varchar(50) primary key,
    created_at        timestamp NOT NULL default CURRENT_TIMESTAMP,
    updated_at        timestamp NOT NULL default CURRENT_TIMESTAMP,
    productName       varchar(250) NOT NULL,
    headline          varchar(250) NOT NULL,
    min               integer NOT NULL,
    max               integer NOT NULL,
    baseRecipePrice   integer NOT NULL,
    shippingPrice     integer NOT NULL
);

create table recipes
(
    id                varchar(50) primary key,
    created_at        timestamp NOT NULL default CURRENT_TIMESTAMP,
    updated_at        timestamp NOT NULL default CURRENT_TIMESTAMP,
    product_id        varchar(50) NOT NULL,
    name              varchar(250) NOT NULL,
    slug              varchar(250) NOT NULL,
    headline          varchar(250) NOT NULL,
    image             varchar(250) NOT NULL,
    selected          integer NOT NULL,
    selectionLimit    integer,
    extraCharge       integer NOT NULL default 0,
    yields            integer NOT NULL
);
```

## TASKS

### Build the recipes-service

* The service can be built in any programming language of your choice.
* The source code should be added to this repository with instructions on how to set it up locally so that the frontend application can communicate seamlessly with the service.
* A database should be used to store the recipe information.

In the interest of time and complexity we do not require to associate the recipe selection to a user. The structure is simplified and in the challenge assume only one user to ever access the service. 

* The service should include two endpoints: 
  * **GET /recipes**:
    * This endpoint should take product's id **id** (in the example below it's `5f4e821d531e677602591a9b`) as a param and return a list of recipes belonging to that product
    * response JSON schema:

```json
{
  // HelloFresh box identifier
  "id": "5f4e821d531e677602591a9b",
  // Product name
  "productName": "classic-box",
  // Headline
  "headline": "WEEK OF OCTOBER 12TH'",
  // Minimum recipes for this HelloFresh box
  "min": 3,
  // Maximum recipes for this HelloFresh box
  "max": 8,
  // Base price of every recipe
  "baseRecipePrice": 1798,
  // Shipping price of this HelloFresh box
  "shippingPrice": 1298,
  // Array of recipes that the customer can select for this HelloFresh box
  "recipes": [
    {
      // Recipe identifier
      "id": "5f4d4a7e62fb0224951e7ec4",
      // Name of the recipe
      "name": "Chicken Sausage & Spinach Ravioli",
      // Recipe slug
      "slug": "chicken-sausage-spinach-ravioli",
      // Recipe headline
      "headline": "with Tomato & Lemon",
      // Recipe image
      "image":
        "https://img.hellofresh.com/c_fill,f_auto,fl_lossy,h_405,q_auto,w_720/hellofresh_s3/image/5f4d4a7e62fb0224951e7ec4-2fe03fc2.jpg",
      // Indicates the times this recipe was selected, this is used to perform the recipe selection
      "selected": 1,
      // Maximum number of times this recipe can be selected
      "selectionLimit": 1,
      // Extra charge for this recipe
      "extraCharge": 0,
      // Servings
      "yields": 2,
    },
  ]
}
```

* **GET /recipes**  should be used by the web application
* **PATCH /recipe**. This endpoint should mutate a recipe, based on the **recipe ID**.
* Both endpoints **GET /recipes** and **PATCH /recipe** should be used by the web application

#### Acceptance Criteria

* The service and database are encapsulated in a docker containers and can be setup with a simple command 
* There is validation that ensures that no more recipes may be selected than defined for this product
* There is validation that ensures that no less recipes may be selected than defined for this product
* The endpoints respond with appropriate status codes
* Authentication is NOT required. (In the interest of time and complexity we do not require to associate the recipe selection to a user. The structure is simplified and in the challenge assume only one user to ever access the service.)
* **Desired:** Unit testing.

### Integrate the Frontend Application with recipes-service

Once the service is build, change the **useFetchHelloFreshBox** hook to consume data from the **recipes-service** you built. Demonstrating the pagination functionality uppon scroll is desired. 

#### Acceptance Criteria

* Recipes data is coming from the **recipes-service** 
* The whole application (Frontend + isolated service) can be executed with ease given instructions to be provided by the candidate.

### Recipe Selection

Adding and removing recipes from your box is the primary functionality of the Recipe Selection Page. For this task, you'll implement the recipe selection functionality by following the defined acceptance criteria.

#### Acceptance Criteria


*   The **selected** property determines the recipe's selection, **0 means not selected**, and any integer above 0 is the **number of times** the recipe is **selected**.
*   A customer can **add** a recipe to their box **as many times** as they want, as long as they **don't exceed** the maximum number of **allowed recipes** per box or the recipe's **selection limit.**
*   When the **selection limit** of a recipe is reached, the **add button** should be **disabled** for that recipe
*   When the **maximum** number of allowed recipes in a box is **reached**, the **add button** for all recipes should be **disabled**.
*   When the **minimum** number of selected recipes for that box is **reached**, the copy of all the **add buttons** for the non-selected recipes **changes** from "Add" to "Add Extra Meal".


### Price Calculation & Price Summary

During the selection of recipes, we have to communicate to customers the price of their selection. For this task, you'll implement the price calculation functionality and the price summary user interface.


#### Acceptance Criteria

*   The **price summary** should include the price for **every selected recipe on your box**, the number of **times it is selected**, the **shipping price**, and the **total price**.
*   Some of our recipes with top-quality ingredients have an extra charge; the total price for a recipe is the base recipe price plus the extra charge by the number of times the recipe is selected: `Total Recipe Price = (Base Recipe Price + Extra Charge) * Selected Times`.
*   The **total price** is the **sum** of all the **selected recipes' total price** plus the **shipping price.**
*   The price summary **user interface** should follow the specification of the provided **designs**.
*   **Desired:** A test suite that includes unit, integration and e2e 

## DESIGNS

### Recipe Selection

#### Below minimum selected recipes

This screen shows the state of the user interface for when the customer have less recipes selected than the box minimum.

<img src="./readme-assets/Desktop - below min recipes selected.png" />

#### Minimum selected recipes

This screen shows the state of the user interface for when the customer have selected the minimum number of recipes for this box.

<img src="./readme-assets/Desktop - min recipes selected.png" />

#### Maximum selected recipes

This screen shows the state of the user interface for when the customer have selected the maximum number of recipes for this box.

<img src="./readme-assets/Desktop - max recipes selected.png" />

### Price Summary

#### Price summary open

This screen shows the behavior of the price summary with some recipes selected a single time and a recipe selected more than one time.

<img src="./readme-assets/Desktop - Price Summary.png" />


## COMPONENTS

To speed up the time it takes you to solve these tasks we have provided several components that will help you style and structure the user interface.

These components are based on the styled-system specification and rendered using styled-components.


### Box

The Box component serves as a wrapper component for layout related needs. Use Box to set values such as display, width, height, and more. In practice, this component is used as a wrapper around other components to achieve Box Model related styling.

This component uses the following functions from the styled-system: background, border, color, flexbox, layout, position, shadow, and space.

Reference table for styled-system: [https://github.com/styled-system/styled-system/blob/master/docs/table.md](https://github.com/styled-system/styled-system/blob/master/docs/table.md)

We recommend to use long-hand properties, for example:

```js
// Do
<Box borderTopWidth="sm" borderTopColor="border" borderTopStyle="solid" />
// Don't
<Box borderTop="1px solid" borderColor="border" />
```

### Flex

The Flex component behaves the same as the Box component except that it has display: flex set by default.


### List

The List component renders a ul element with list-style-type: none set by default.

This component uses the following functions from styled-system: layout, space.

Reference table for styled-system: [https://github.com/styled-system/styled-system/blob/master/docs/table.md](https://github.com/styled-system/styled-system/blob/master/docs/table.md)


### ListItem

The ListItem component renders a li element, and it's recommended to be used as the children of the List component.


### Text

The Text component is a wrapper component that will apply typography styles to the text inside. It renders a div element as default, but using the ["as" polyphormic property from the styled-components specification](https://styled-components.com/docs/api#as-polymorphic-prop) can render any text element, such as p, h1, span, etc.


### Button

The Button component can use two different variants; primary and secondary, that can be manipulated through the variant property.

This component uses the following functions from styled-system: layout, space.

Reference table for styled-system: [https://github.com/styled-system/styled-system/blob/master/docs/table.md](https://github.com/styled-system/styled-system/blob/master/docs/table.md)


### Grid

Our grid is implemented through the styled-bootstrap-grid [https://github.com/dragma/styled-bootstrap-grid](https://github.com/dragma/styled-bootstrap-grid)




## GLOSSARY

**HelloFresh Box:** the weekly physical box that arrives at your door containing the recipes that you have previously selected. Boxes have boundaries around the minimum and maximum recipes that you can receive.

**Recipe:** a combination of ingredients that are cooked together create a HelloFresh recipe; a box can have multiple recipes and multiple items of the same recipe. This way, our customers can cook more portions of the same recipe in case they're throwing a dinner party!

**Recipe Selection:** the action of adding or removing a recipe from your box based on the configuration from your box and recipe, such as box selection boundaries and recipe selection limit.
