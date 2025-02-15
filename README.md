import { useState } from "react";
import { ShoppingCart } from "lucide-react";
import { Button } from "@/components/ui/button";
import { PayPalButtons, PayPalScriptProvider } from "@paypal/react-paypal-js";

const menProducts = [
  { id: 1, name: "Men's Performance Tee", price: 34.99, image: "/men_tee.jpg" },
  { id: 2, name: "Men's Training Shorts", price: 29.99, image: "/men_shorts.jpg" },
];

const womenProducts = [
  { id: 3, name: "Women's Seamless Leggings", price: 49.99, image: "/women_leggings.jpg" },
  { id: 4, name: "Women's Crop Top", price: 24.99, image: "/women_croptop.jpg" },
];

export default function Store() {
  const [cart, setCart] = useState([]);

  const addToCart = (product) => {
    if (!cart.some(item => item.id === product.id)) {
      setCart([...cart, product]);
    }
  };

  const totalAmount = cart.reduce((sum, product) => sum + product.price, 0);

  return (
    <PayPalScriptProvider options={{ "client-id": process.env.NEXT_PUBLIC_PAYPAL_CLIENT_ID }}>
      <div className="bg-gray-100 min-h-screen">
        <div className="relative w-full h-[500px] bg-cover bg-center" style={{ backgroundImage: "url('/hero-banner.jpg')" }}>
          <div className="absolute inset-0 bg-black bg-opacity-50 flex items-center justify-center">
            <h1 className="text-white text-5xl font-bold">Train Hard. Look Good.</h1>
          </div>
        </div>
        
        <div className="p-6 max-w-6xl mx-auto">
          <h2 className="text-3xl font-bold text-center mb-6">Shop Now</h2>
          <div className="grid grid-cols-1 md:grid-cols-2 gap-6">
            <div className="relative group">
              <img src="/men_category.jpg" alt="Men's Clothing" className="w-full h-64 object-cover" />
              <div className="absolute inset-0 bg-black bg-opacity-50 flex items-center justify-center group-hover:bg-opacity-70 transition">
                <h3 className="text-white text-2xl font-semibold">Men</h3>
              </div>
            </div>
            <div className="relative group">
              <img src="/women_category.jpg" alt="Women's Clothing" className="w-full h-64 object-cover" />
              <div className="absolute inset-0 bg-black bg-opacity-50 flex items-center justify-center group-hover:bg-opacity-70 transition">
                <h3 className="text-white text-2xl font-semibold">Women</h3>
              </div>
            </div>
          </div>
        </div>
        
        <div className="p-6 max-w-6xl mx-auto bg-white shadow-md rounded-md mt-6">
          <h2 className="text-2xl font-bold mb-4">Your Cart</h2>
          {cart.length > 0 ? (
            <>
              <ul>
                {cart.map((product) => (
                  <li key={product.id} className="flex justify-between p-2 border-b">
                    <span>{product.name}</span>
                    <span>${product.price.toFixed(2)}</span>
                  </li>
                ))}
              </ul>
              <div className="mt-4 text-lg font-bold">Total: ${totalAmount.toFixed(2)}</div>
              <div className="mt-4">
                <PayPalButtons
                  createOrder={(data, actions) => {
                    return actions.order.create({
                      purchase_units: [{ amount: { value: totalAmount.toFixed(2) } }],
                    });
                  }}
                  onApprove={(data, actions) => {
                    return actions.order.capture().then((details) => {
                      alert(`Transaction completed by ${details.payer.name.given_name}`);
                      setCart([]); // Empty cart after successful transaction
                    });
                  }}
                />
              </div>
            </>
          ) : (
            <p>Your cart is empty.</p>
          )}
        </div>

        <div className="fixed bottom-6 right-6 bg-black text-white p-4 rounded-full flex items-center shadow-lg">
          <ShoppingCart size={24} />
          <span className="ml-2 text-lg font-bold">{cart.length}</span>
        </div>

        <footer className="bg-gray-900 text-white p-6 mt-12 text-center">
          <p className="text-sm">© 2025 Onate Fitness. Alle Rechte vorbehalten.</p>
          <p className="text-sm mt-2"><a href="/impressum" className="underline">Impressum</a> | <a href="/datenschutz" className="underline">Datenschutz</a></p>
        </footer>
      </div>
    </PayPalScriptProvider>
  );
}
