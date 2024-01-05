import 'package:flutter/material.dart';
import 'package:provider/provider.dart';

// Define Product model
class Product {
  final String image;
  final String name;
  final double price;

  Product({required this.image, required this.name, required this.price});
}

void main() {
  runApp(MyApp());
}

class Cart extends ChangeNotifier {
  List<Product> cartItems = [];

  void addToCart(Product product) {
    cartItems.add(product);
    notifyListeners();
  }

  void removeFromCart(Product product) {
    cartItems.remove(product);
    notifyListeners();
  }
}

class MyApp extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return ChangeNotifierProvider(
      create: (context) => Cart(),
      child: MaterialApp(
        title: 'E-commerce App',
        initialRoute: '/',
        routes: {
          '/': (context) => ProductListingScreen(),
          '/cart': (context) => CartScreen(),
        },
      ),
    );
  }
}

class ProductListingScreen extends StatelessWidget {
  final List<Product> productList = [
    Product(image: 'assets/product1.jpg', name: 'Product 1', price: 1),
     Product(image: 'assets/product2.jpg', name: 'Product 2', price: 2),
     Product(image: 'assets/product3.jpg', name: 'Product 3', price: 4),
     Product(image: 'assets/product4.jpg', name: 'Product 4', price: 8),
     Product(image: 'assets/product5.jpg', name: 'Product 5', price: 16),
        Product(image: 'assets/product6.jpg', name: 'Product 6', price: 32),
     Product(image: 'assets/product7.jpg', name: 'Product 7', price: 64),
     Product(image: 'assets/product8.jpg', name: 'Product 8', price: 128),
     Product(image: 'assets/product9.jpg', name: 'Product 9', price: 256),
     Product(image: 'assets/product10.jpg', name: 'Product 10', price: 512),
    // Add more products...
    // unable to add pictures.

  ];

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(
        title: Text('Products'),
        actions: [
          IconButton(
            icon: Icon(Icons.shopping_cart),
            onPressed: () {
              Navigator.pushNamed(context, '/cart');
            },
          ),
        ],
      ),
      body: ListView.builder(
        itemCount: productList.length,
        itemBuilder: (context, index) {
          final product = productList[index];
          return ListTile(
            leading: Image.asset(product.image),
            title: Text(product.name),
            subtitle: Text('\$${product.price.toString()}'),
            trailing: Consumer<Cart>(
              builder: (context, cart, _) {
                final added = cart.cartItems.contains(product);
                return IconButton(
                  icon: added ? Icon(Icons.done) : Icon(Icons.add_shopping_cart),
                  onPressed: () {
                    added ? cart.removeFromCart(product) : cart.addToCart(product);
                  },
                );
              },
            ),
          );
        },
      ),
    );
  }
}

class CartScreen extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    final cart = Provider.of<Cart>(context);
    final cartItems = cart.cartItems;

    double totalPrice = cartItems.fold(0, (total, current) => total + current.price);

    return Scaffold(
      appBar: AppBar(
        title: Text('Cart'),
      ),
      body: ListView.builder(
        itemCount: cartItems.length,
        itemBuilder: (context, index) {
          final product = cartItems[index];
          return ListTile(
            leading: Image.asset(product.image),
            title: Text(product.name),
            subtitle: Text('\$${product.price.toString()}'),
          );
        },
      ),
      bottomNavigationBar: Padding(
        padding: EdgeInsets.all(8.0),
        child: Row(
          mainAxisAlignment: MainAxisAlignment.spaceBetween,
          children: [
            Text(
              'Total: \$${totalPrice.toString()}',
              style: TextStyle(fontSize: 18),
            ),
            ElevatedButton(
              onPressed: () {
                cart.cartItems.clear();
              },
              child: Text('BUY'),
            ),
          ],
        ),
      ),
    );
  }
}
