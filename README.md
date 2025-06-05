// Task 2: Insert 10 books
db.books.insertMany([
  {
    title: "The Great Gatsby",
    author: "F. Scott Fitzgerald",
    genre: "Classic",
    published_year: 1925,
    price: 10.99,
    in_stock: true,
    pages: 180,
    publisher: "Scribner"
  },
  {
    title: "To Kill a Mockingbird",
    author: "Harper Lee",
    genre: "Classic",
    published_year: 1960,
    price: 12.99,
    in_stock: true,
    pages: 281,
    publisher: "J.B. Lippincott & Co."
  },
  {
    title: "1984",
    author: "George Orwell",
    genre: "Dystopian",
    published_year: 1949,
    price: 9.99,
    in_stock: false,
    pages: 328,
    publisher: "Secker & Warburg"
  },
  {
    title: "Brave New World",
    author: "Aldous Huxley",
    genre: "Dystopian",
    published_year: 1932,
    price: 11.50,
    in_stock: true,
    pages: 311,
    publisher: "Chatto & Windus"
  },
  {
    title: "Sapiens",
    author: "Yuval Noah Harari",
    genre: "Non-fiction",
    published_year: 2011,
    price: 18.00,
    in_stock: true,
    pages: 443,
    publisher: "Harvill Secker"
  },
  {
    title: "The Catcher in the Rye",
    author: "J.D. Salinger",
    genre: "Classic",
    published_year: 1951,
    price: 10.00,
    in_stock: false,
    pages: 214,
    publisher: "Little, Brown and Company"
  },
  {
    title: "The Alchemist",
    author: "Paulo Coelho",
    genre: "Fiction",
    published_year: 1988,
    price: 15.00,
    in_stock: true,
    pages: 208,
    publisher: "HarperTorch"
  },
  {
    title: "Becoming",
    author: "Michelle Obama",
    genre: "Biography",
    published_year: 2018,
    price: 20.00,
    in_stock: true,
    pages: 448,
    publisher: "Crown"
  },
  {
    title: "The Road",
    author: "Cormac McCarthy",
    genre: "Post-apocalyptic",
    published_year: 2006,
    price: 14.00,
    in_stock: false,
    pages: 287,
    publisher: "Alfred A. Knopf"
  },
  {
    title: "Educated",
    author: "Tara Westover",
    genre: "Memoir",
    published_year: 2018,
    price: 16.00,
    in_stock: true,
    pages: 334,
    publisher: "Random House"
  }
]);

// Task 2: CRUD Queries

// Find all books in a specific genre
db.books.find({ genre: "Classic" });

// Find books published after a certain year
db.books.find({ published_year: { $gt: 2000 } });

// Find books by a specific author
db.books.find({ author: "George Orwell" });

// Update the price of a specific book
db.books.updateOne(
  { title: "1984" },
  { $set: { price: 11.99 } }
);

// Delete a book by its title
db.books.deleteOne({ title: "To Kill a Mockingbird" });

// Task 3: Advanced Queries

// Find books that are in stock and published after 2010
db.books.find({ in_stock: true, published_year: { $gt: 2010 } });

// Projection: Only return title, author, and price
db.books.find({}, { _id: 0, title: 1, author: 1, price: 1 });

// Sort books by price (ascending)
db.books.find().sort({ price: 1 });

// Sort books by price (descending)
db.books.find().sort({ price: -1 });

// Pagination: 5 books per page
// Page 1
db.books.find().skip(0).limit(5);

// Page 2
db.books.find().skip(5).limit(5);

// Task 4: Aggregation Pipelines

// Average price of books by genre
db.books.aggregate([
  { $group: { _id: "$genre", avgPrice: { $avg: "$price" } } }
]);

// Find the author with the most books
db.books.aggregate([
  { $group: { _id: "$author", count: { $sum: 1 } } },
  { $sort: { count: -1 } },
  { $limit: 1 }
]);

// Group books by publication decade and count them
db.books.aggregate([
  {
    $project: {
      decade: {
        $concat: [
          { $toString: { $subtract: ["$published_year", { $mod: ["$published_year", 10] }] } },
          "s"
        ]
      }
    }
  },
  { $group: { _id: "$decade", count: { $sum: 1 } } },
  { $sort: { _id: 1 } }
]);

// Task 5: Indexing

// Create index on title
db.books.createIndex({ title: 1 });

// Create compound index on author and published_year
db.books.createIndex({ author: 1, published_year: -1 });

// Use explain() to analyze query performance (before and after indexing)
db.books.find({ title: "1984" }).explain("executionStats");

db.books.find({ author: "George Orwell", published_year: 1949 }).explain("executionStats");
