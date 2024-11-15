#### here from 15 documents where has a products array with price and quantity,this calculates total for every documents and sum all of the documents total

```javascript
db.orders.aggregate([
  {
    $facet: {
      totalSale: [
        {
          $project: {
            orderTotal: {
              $sum: {
                $map: {
                  input: "$products",
                  as: "product", // this refers every item in producst array
                  in: { $multiply: ["$$product.quantity", "$$product.price"] },
                },
              },
            },
          },
        },
        {
          $group: { _id: null, totalSale: { $sum: "$orderTotal" } },
        },
        { $project: { _id: 0, totalSale: 1 } },
      ],

      averageOrderTotal: [],
    },
  },
]);
```

#### it apply for just shipped status and also count avg

```javascript
db.orders.aggregate([
  {
    $match: {
      status: "Shipped",
    },
  },
  {
    $facet: {
      totalSale: [
        {
          $project: {
            orderTotal: {
              $sum: {
                $map: {
                  input: "$products",
                  as: "product", // this refers every item in producst array
                  in: { $multiply: ["$$product.quantity", "$$product.price"] },
                },
              },
            },
          },
        },
        {
          $group: { _id: null, totalSale: { $sum: "$orderTotal" } },
        },
        { $project: { _id: 0, totalSale: 1 } },
      ],

      averageOrderTotal: [
        {
          $project: {
            orderTotal: {
              $sum: {
                $map: {
                  input: "$products",
                  as: "product",
                  in: { $multiply: ["$$product.quantity", "$$product.price"] },
                },
              },
            },
          },
        },
        {
          $group: {
            _id: null,
            avgOrderTotal: { $avg: "$orderTotal" },
          },
        },
        {
          $project: { _id: 0, avgOrderTotal: 1 },
        },
      ],
    },
  },
]);
```
