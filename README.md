src/
├── app/
│   ├── layout.tsx
│   ├── page.tsx (หน้าหลัก)
│   ├── trading/page.tsx
│   ├── marketplace/page.tsx
│   ├── portfolio/page.tsx
│   ├── admin/page.tsx
│   └── providers.tsx
├── components/
│   ├── Navigation.tsx
│   ├── WalletConnect.tsx
│   ├── CoinCard.tsx
│   ├── MarketplaceCard.tsx
│   ├── PortfolioTracker.tsx
│   └── AdminPanel.tsx
├── lib/
│   ├── coins.ts
│   └── marketplace.ts
└── types/
    └── index.ts

1️⃣ src/app/layout.tsx
import type { Metadata } from 'next';
import { Inter } from 'next/font/google';
import './globals.css';
import { Providers } from './providers';

const inter = Inter({ subsets: ['latin'] });

export const metadata: Metadata = {
  title: 'Halal Crypto - Shariah-Compliant Trading Platform',
  description: 'Trade interest-free cryptocurrencies on Base blockchain',
};

export default function RootLayout({
  children,
}: {
  children: React.ReactNode;
}) {
  return (
    <html lang="en">
      <body className={inter.className}>
        <Providers>{children}</Providers>
      </body>
    </html>
  );
}

2️⃣ src/app/providers.tsx
'use client';

import { ReactNode } from 'react';
import { WagmiProvider, createConfig, http } from 'wagmi';
import { base } from 'wagmi/chains';
import { QueryClient, QueryClientProvider } from '@tanstack/react-query';
import { OnchainKitProvider } from '@coinbase/onchainkit';
import { coinbaseWallet } from 'wagmi/connectors';

const config = createConfig({
  chains: [base],
  connectors: [
    coinbaseWallet({
      appName: 'Halal Crypto',
      preference: 'smartWalletOnly',
    }),
  ],
  transports: {
    [base.id]: http(),
  },
});

const queryClient = new QueryClient();

export function Providers({ children }: { children: ReactNode }) {
  return (
    <WagmiProvider config={config}>
      <QueryClientProvider client={queryClient}>
        <OnchainKitProvider
          apiKey={process.env.NEXT_PUBLIC_ONCHAINKIT_API_KEY}
          chain={base}
        >
          {children}
        </OnchainKitProvider>
      </QueryClientProvider>
    </WagmiProvider>
  );
}

3️⃣ src/app/page.tsx (หน้าหลัก)
'use client';

import { useState } from 'react';
import Navigation from '@/components/Navigation';
import WalletConnect from '@/components/WalletConnect';
import CoinCard from '@/components/CoinCard';
import { coins } from '@/lib/coins';

export default function Home() {
  return (
    <div className="min-h-screen bg-gradient-to-br from-emerald-50 to-amber-50">
      <Navigation />
      
      {/* Hero Section */}
      <section className="pt-24 pb-12 px-4">
        <div className="max-w-7xl mx-auto text-center">
          <div className="mb-6">
            <span className="text-6xl">🕌</span>
          </div>
          <h1 className="text-5xl font-bold text-emerald-900 mb-4">
            Halal Crypto
          </h1>
          <p className="text-xl text-emerald-700 mb-8">
            Shariah-Compliant Cryptocurrency Trading Platform
          </p>
          <WalletConnect />
        </div>
      </section>

      {/* Coins Section */}
      <section className="py-12 px-4">
        <div className="max-w-7xl mx-auto">
          <h2 className="text-3xl font-bold text-emerald-900 mb-8 text-center">
            ✅ Halal Certified Cryptocurrencies
          </h2>
          <div className="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-4 gap-6">
            {coins.map((coin) => (
              <CoinCard key={coin.symbol} coin={coin} />
            ))}
          </div>
        </div>
      </section>

      {/* Features Section */}
      <section className="py-12 px-4 bg-white">
        <div className="max-w-7xl mx-auto">
          <h2 className="text-3xl font-bold text-emerald-900 mb-12 text-center">
            Platform Features
          </h2>
          <div className="grid grid-cols-1 md:grid-cols-3 gap-8">
            <div className="text-center p-6">
              <div className="text-5xl mb-4">💰</div>
              <h3 className="text-xl font-bold text-emerald-800 mb-2">
                Interest-Free Trading
              </h3>
              <p className="text-emerald-600">
                Trade cryptocurrencies following Islamic finance principles
              </p>
            </div>
            <div className="text-center p-6">
              <div className="text-5xl mb-4">🛒</div>
              <h3 className="text-xl font-bold text-emerald-800 mb-2">
                Islamic Marketplace
              </h3>
              <p className="text-emerald-600">
                Purchase halal-certified goods and services
              </p>
            </div>
            <div className="text-center p-6">
              <div className="text-5xl mb-4">📊</div>
              <h3 className="text-xl font-bold text-emerald-800 mb-2">
                Portfolio Tracking
              </h3>
              <p className="text-emerald-600">
                Monitor your investments in real-time
              </p>
            </div>
          </div>
        </div>
      </section>
    </div>
  );
}

4️⃣ src/components/Navigation.tsx
'use client';

import Link from 'next/link';
import { usePathname } from 'next/navigation';

export default function Navigation() {
  const pathname = usePathname();

  const links = [
    { href: '/', label: 'Home', icon: '🏠' },
    { href: '/trading', label: 'Trading', icon: '💹' },
    { href: '/marketplace', label: 'Marketplace', icon: '🛒' },
    { href: '/portfolio', label: 'Portfolio', icon: '📊' },
    { href: '/admin', label: 'Admin', icon: '👨‍💼' },
  ];

  return (
    <nav className="fixed top-0 left-0 right-0 bg-white shadow-md z-50">
      <div className="max-w-7xl mx-auto px-4">
        <div className="flex items-center justify-between h-16">
          <div className="flex items-center space-x-2">
            <span className="text-2xl">🕌</span>
            <span className="text-xl font-bold text-emerald-900">
              Halal Crypto
            </span>
          </div>
          
          <div className="flex space-x-1">
            {links.map((link) => (
              <Link
                key={link.href}
                href={link.href}
                className={`px-4 py-2 rounded-lg transition-colors ${
                  pathname === link.href
                    ? 'bg-emerald-600 text-white'
                    : 'text-emerald-700 hover:bg-emerald-50'
                }`}
              >
                <span className="mr-2">{link.icon}</span>
                {link.label}
              </Link>
            ))}
          </div>
        </div>
      </div>
    </nav>
  );
}

5️⃣ src/components/WalletConnect.tsx
'use client';

import { ConnectWallet, Wallet } from '@coinbase/onchainkit/wallet';
import { useAccount } from 'wagmi';

export default function WalletConnect() {
  const { address, isConnected } = useAccount();

  return (
    <div className="flex flex-col items-center space-y-4">
      <Wallet>
        <ConnectWallet>
          <div className="px-6 py-3 bg-emerald-600 text-white rounded-lg hover:bg-emerald-700 transition-colors cursor-pointer">
            {isConnected ? 'Wallet Connected' : 'Connect Wallet'}
          </div>
        </ConnectWallet>
      </Wallet>
      
      {isConnected && address && (
        <div className="text-sm text-emerald-700">
          Connected: {address.slice(0, 6)}...{address.slice(-4)}
        </div>
      )}
    </div>
  );
}

6️⃣ src/components/CoinCard.tsx
'use client';

import type { Coin } from '@/types';

interface CoinCardProps {
  coin: Coin;
}

export default function CoinCard({ coin }: CoinCardProps) {
  return (
    <div className="bg-white rounded-xl shadow-lg p-6 hover:shadow-xl transition-shadow border-2 border-emerald-100">
      <div className="flex items-center justify-between mb-4">
        <div className="flex items-center space-x-3">
          <div className="text-4xl">{coin.icon}</div>
          <div>
            <h3 className="text-xl font-bold text-emerald-900">
              {coin.name}
            </h3>
            <p className="text-sm text-emerald-600">{coin.symbol}</p>
          </div>
        </div>
        {coin.isHalal && (
          <div className="bg-emerald-100 text-emerald-800 px-3 py-1 rounded-full text-xs font-semibold">
            ✅ Halal
          </div>
        )}
      </div>
      
      <div className="space-y-2">
        <div className="flex justify-between">
          <span className="text-emerald-600">Price:</span>
          <span className="font-bold text-emerald-900">${coin.price}</span>
        </div>
        <div className="flex justify-between">
          <span className="text-emerald-600">24h Change:</span>
          <span
            className={`font-bold ${
              coin.change24h >= 0 ? 'text-green-600' : 'text-red-600'
            }`}
          >
            {coin.change24h >= 0 ? '+' : ''}
            {coin.change24h}%
          </span>
        </div>
        <div className="flex justify-between">
          <span className="text-emerald-600">Market Cap:</span>
          <span className="font-bold text-emerald-900">${coin.marketCap}</span>
        </div>
      </div>

      <button className="w-full mt-4 bg-emerald-600 text-white py-2 rounded-lg hover:bg-emerald-700 transition-colors">
        Trade {coin.symbol}
      </button>
    </div>
  );
}

7️⃣ src/lib/coins.ts
import type { Coin } from '@/types';

export const coins: Coin[] = [
  {
    symbol: 'ETH',
    name: 'Ethereum',
    icon: '⟠',
    price: '3,245.67',
    change24h: 2.45,
    marketCap: '390B',
    isHalal: true,
  },
  {
    symbol: 'USDC',
    name: 'USD Coin',
    icon: '💵',
    price: '1.00',
    change24h: 0.01,
    marketCap: '32B',
    isHalal: true,
  },
  {
    symbol: 'BASE',
    name: 'Base Token',
    icon: '🔵',
    price: '1.25',
    change24h: 5.67,
    marketCap: '2.5B',
    isHalal: true,
  },
  {
    symbol: 'HAQQ',
    name: 'HAQQ Network',
    icon: '☪️',
    price: '0.45',
    change24h: 3.21,
    marketCap: '150M',
    isHalal: true,
  },
];

8️⃣ src/types/index.ts
export interface Coin {
  symbol: string;
  name: string;
  icon: string;
  price: string;
  change24h: number;
  marketCap: string;
  isHalal: boolean;
}

export interface MarketplaceItem {
  id: string;
  name: string;
  category: string;
  price: string;
  seller: string;
  image: string;
  isVerified: boolean;
}

export interface PortfolioAsset {
  symbol: string;
  amount: number;
  value: string;
  change24h: number;
}

9️⃣ src/app/globals.css
@tailwind base;
@tailwind components;
@tailwind utilities;

:root {
  --emerald-50: #ecfdf5;
  --emerald-600: #059669;
  --emerald-900: #064e3b;
  --amber-50: #fffbeb;
}

body {
  font-family: 'Inter', sans-serif;
}

🔟 package.json dependencies
{
  "dependencies": {
    "next": "^15.3.8",
    "react": "^19.0.0",
    "react-dom": "^19.0.0",
    "@coinbase/onchainkit": "latest",
    "wagmi": "^2.x",
    "viem": "^2.x",
    "@tanstack/react-query": "^5.x",
    "tailwindcss": "^3.4.0"
  }
}