# E-commerce with Sequelize

## Description

This week I'm building on my SQL practice and implementing the Sequelize ORM. To play around with it, I'll be setting up the back-end of a hypothetical e-commerce site with products, categories, and tags. I'll set up routes using Express.js and run some CRUD commands through those.

| **Scenario**                                                                                                                                                                                |
| :------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| _Given some product information, including categories and tags, I'd like store all that information in a database. I'd like to be able to perform CRUD actions on that data via API calls._ |

Here it is in action:
![GIF of app in use](/images/app-in-use.gif)

---

## Usage

I don't know why anyone would want to use this other than personal exploration, but if you want to, feel free! To do so:

- Install the necessary packages using `npm i`
- Create a database named `ecommerce_db` in MySQL by your preferred method.
  > The repo includes a `schema.sql` file, if you'd like to use that.
- Sequelize will construct the tables (models, in Sequelize-speak) on start, so you can run `npm start` to get that going.
- If you'd like to use the included seed data to populate the tables, you can do so once the tables are created by exiting the program and running `npm run seed`.
- Lastly, restart the program and try making some API calls to the included routes.

  > Currently available routes are as follows:
  >
  > - <http://localhost:3001/api/categories> Accepts GET, POST
  > - <http://localhost:3001/api/categories/:id> Accepts GET, PUT, DELETE
  > - <http://localhost:3001/api/products> Accepts GET, POST
  > - <http://localhost:3001/api/products/:id> Accepts GET, PUT, DELETE
  > - <http://localhost:3001/api/tags> Accepts GET, POST
  > - <http://localhost:3001/api/tags/:id> Accepts GET, PUT, DELETE

---

## My Approach

Not a whole lot of interesting creative work on this one. Given that my primary goal was to play around with the Sequelize ORM, I didn't do much more than implement things I found in the [Sequelize documentation](https://sequelize.org/docs/v6/). (Excellent documentation, by the way. Thank you, Sequelize team!)

In that vein, I suppose it's worth mentioning the Association definitions.

### Associations

I have a reasonable amount of experience writing SQL queries for an existing database, but I haven't had much experience building new databases, so this was a new topic for me. It makes sense intuitively, and Sequelize does a great job explaining it in the docs, as well as writing intuitive methods.

To take a look at some examples from this project:

```javascript
Category.hasMany(Product, {
  foreignKey: "category_id",
  onDelete: "CASCADE",
});
```

I've split my ecommerce site into categories, and every category has multiple products associated with it. So, we declare the association `Category.hasMany(Product)`

I have to feed it the foreign key that links the two. In this case, Product has a foreign key `category_id` that's associated with the Category's primary key of `id`.

The last option, `onDelete`, is set to `CASCADE`, because if I delete a category I also want to delete all of its associated products, i.e. "we're getting rid of the Shirts department, so throw out all the shirts."

```javascript
Product.belongsTo(Category, {
  foreignKey: "category_id",
});
```

Associations go both ways, so this is the other half of the Category-Products relationship. Each individual product belongs to a category. The foreign key is the same.

```javascript
Product.belongsToMany(Tag, { through: ProductTag });
Tag.belongsToMany(Product, { through: ProductTag });
```

I also wanted to include tags, but unlike categories, a single product can have many tags, and a single tag may be applied to many products. So, rather than link them directly, I had to set up a _through table_ that I called `ProductTag`.

It's not the easiest thing to demonstrate in markdown, but I'll give it a shot.

> | **Product Table** |
> | :---------------- |
> | id                |
> | 1                 |
> | 2                 |

| **ProductTag Table** |        |
| :------------------- | :----- |
| product_id           | tag_id |
| 1                    | a      |
| 2                    | a      |
| 2                    | b      |

> | **Tag Table** |
> | :------------ |
> | id            |
> | a             |
> | b             |

You can see on the **ProductTag** table how `product_id 1` can be given two tags (a, b) and at the same time `tag_id a` can be assigned to two products (1, 2).

---

## Learnings / Reflections

API routes are all well and good. I'm excited to start tying it all into the front-end!
