// Project: XT SysteM
// Description: Next.js web app with Discord OAuth (NextAuth) and simple gift card storefront

// File structure:
// ├── package.json
// ├── .env.local.example
// ├── next.config.js
// ├── tailwind.config.js
// ├── postcss.config.js
// ├── /pages
// │   ├── _app.js
// │   ├── index.js
// │   ├── cart.js
// │   └── /api
// │       ├── auth/[...nextauth].js
// │       └── giftcards.js
// └── /components
//     ├── Navbar.js
//     ├── GiftCardCard.js
//     └── CartItem.js

// package.json
{
  "name": "discord-giftcard-store",
  "version": "1.0.0",
  "scripts": {
    "dev": "next dev",
    "build": "next build",
    "start": "next start"
  },
  "dependencies": {
    "next": "latest",
    "react": "latest",
    "react-dom": "latest",
    "next-auth": "latest",
    "axios": "latest",
    "tailwindcss": "latest",
    "autoprefixer": "latest",
    "postcss": "latest"
  }
}

// .env.local.example
DISCORD_CLIENT_ID=your_discord_client_id
DISCORD_CLIENT_SECRET=your_discord_client_secret
NEXTAUTH_URL=http://localhost:3000
NEXTAUTH_SECRET=some_super_secret_value

// next.config.js
module.exports = {
  reactStrictMode: true,
};

// tailwind.config.js
module.exports = {
  content: ["./pages/**/*.{js,jsx}", "./components/**/*.{js,jsx}"],
  theme: { extend: {} },
  plugins: [],
};

// postcss.config.js
module.exports = {
  plugins: {
    tailwindcss: {},
    autoprefixer: {},
  },
};

// pages/_app.js
import '../styles/globals.css';
import { SessionProvider } from 'next-auth/react';

export default function MyApp({ Component, pageProps }) {
  return (
    <SessionProvider session={pageProps.session}>
      <Component {...pageProps} />
    </SessionProvider>
  );
}

// pages/api/auth/[...nextauth].js
import NextAuth from 'next-auth';
import DiscordProvider from 'next-auth/providers/discord';

export default NextAuth({
  providers: [
    DiscordProvider({
      clientId: process.env.DISCORD_CLIENT_ID,
      clientSecret: process.env.DISCORD_CLIENT_SECRET,
    }),
  ],
  secret: process.env.NEXTAUTH_SECRET,
});

// components/Navbar.js
import { signIn, signOut, useSession } from 'next-auth/react';
export default function Navbar() {
  const { data: session } = useSession();
  return (
    <nav className="p-4 bg-gray-800 text-white flex justify-between">
      <h1 className="text-xl">Gift Card Store</h1>
      <div>
        {session ? (
          <>
            <span className="mr-4">{session.user.username}</span>
            <button onClick={() => signOut()}>Sign Out</button>
          </>
        ) : (
          <button onClick={() => signIn('discord')}>Sign In with Discord</button>
        )}
      </div>
    </nav>
  );
}

// pages/index.js
import axios from 'axios';
import { useState, useEffect } from 'react';
import Navbar from '../components/Navbar';
import GiftCardCard from '../components/GiftCardCard';

export default function Home() {
  const [giftcards, setGiftcards] = useState([]);

  useEffect(() => {
    axios.get('/api/giftcards').then(res => setGiftcards(res.data));
  }, []);

  return (
    <div>
      <Navbar />
      <div className="p-8 grid grid-cols-1 sm:grid-cols-2 lg:grid-cols-3 gap-6">
        {giftcards.map(gc => (
          <GiftCardCard key={gc.id} card={gc} />
        ))}
      </div>
    </div>
  );
}

// components/GiftCardCard.js
import { useContext } from 'react';
import CartContext from '../contexts/CartContext';
export default function GiftCardCard({ card }) {
  const { addToCart } = useContext(CartContext);
  return (
    <div className="border rounded-lg p-4 shadow">
      <h2 className="text-lg font-semibold mb-2">{card.name}</h2>
      <p className="mb-4">Value: ${card.value}</p>
      <button
        className="bg-blue-600 text-white px-4 py-2 rounded"
        onClick={() => addToCart(card)}>
        Add to Cart
      </button>
    </div>
  );
}

// pages/api/giftcards.js
export default function handler(req, res) {
  // Example static gift cards; replace with DB in production
  const cards = [
    { id: 'gc1', name: '10 USD Card', value: 10 },
    { id: 'gc2', name: '25 USD Card', value: 25 },
    { id: 'gc3', name: '50 USD Card', value: 50 },
  ];
  res.status(200).json(cards);
}

// pages/cart.js
import { useContext } from 'react';
import Navbar from '../components/Navbar';
import CartContext from '../contexts/CartContext';

export default function CartPage() {
  const { cart, removeFromCart, total } = useContext(CartContext);
  return (
    <div>
      <Navbar />
      <div className="p-8">
        <h1 className="text-2xl mb-4">Your Cart</h1>
        {cart.length === 0 ? (
          <p>No items in cart.</p>
        ) : (
          <div>
            {cart.map(item => (
              <div key={item.id} className="flex justify-between mb-2">
                <span>{item.name} (${item.value})</span>
                <button onClick={() => removeFromCart(item.id)}>Remove</button>
              </div>
            ))}
            <div className="mt-4 font-bold">Total: ${total}</div>
          </div>
        )}
      </div>
    </div>
  );
}

// contexts/CartContext.js
import { createContext, useState, useEffect } from 'react';
const CartContext = createContext();
export function CartProvider({ children }) {
  const [cart, setCart] = useState([]);
  useEffect(() => {
    const saved = JSON.parse(localStorage.getItem('cart') || '[]');
    setCart(saved);
  }, []);
  useEffect(() => {
    localStorage.setItem('cart', JSON.stringify(cart));
  }, [cart]);
  const addToCart = item => setCart([...cart, item]);
  const removeFromCart = id => setCart(cart.filter(i => i.id !== id));
  const total = cart.reduce((sum, i) => sum + i.value, 0);
  return (
    <CartContext.Provider value={{ cart, addToCart, removeFromCart, total }}>
      {children}
    </CartContext.Provider>
  );
}
export default CartContext;

