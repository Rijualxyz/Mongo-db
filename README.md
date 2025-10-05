use ecommerceDB;

db.createCollection("products");

db.products.insertMany([
  {
    name: "T-Shirt",
    price: 799,
    category: "Clothing",
    variants: [
      { color: "Red", size: "M", stock: 20 },
      { color: "Blue", size: "L", stock: 15 },
      { color: "Black", size: "S", stock: 10 }
    ]
  },
  {
    name: "Sneakers",
    price: 2999,
    category: "Footwear",
    variants: [
      { color: "White", size: 9, stock: 8 },
      { color: "Black", size: 10, stock: 12 }
    ]
  },
  {
    name: "Wrist Watch",
    price: 4999,
    category: "Accessories",
    variants: [
      { color: "Silver", size: "Standard", stock: 5 },
      { color: "Black", size: "Standard", stock: 7 }
    ]
  }
]);

print("\n=== All Products ===");
db.products.find().pretty();

print("\n=== Products in Clothing Category ===");
db.products.find({ category: "Clothing" }).pretty();

print("\n=== Product Names and Variant Colors ===");
db.products.find({}, { _id: 0, name: 1, "variants.color": 1 }).pretty();

print("\n=== Products having Black variant ===");
db.products.find({ "variants.color": "Black" }).pretty();

print("\n=== Updating stock for Blue L T-Shirt ===");
db.products.updateOne(
  { name: "T-Shirt", "variants.color": "Blue", "variants.size": "L" },
  { $inc: { "variants.$.stock": 5 } }
);

print("\n=== Flattened Variant List (Aggregation) ===");
db.products.aggregate([
  { $unwind: "$variants" },
  {
    $project: {
      _id: 0,
      name: 1,
      category: 1,
      "variants.color": 1,
      "variants.size": 1,
      "variants.stock": 1
    }
  }
]).forEach(printjson);

print("\n=== Verify Updated Product ===");
db.products.find({ name: "T-Shirt" }).pretty();
