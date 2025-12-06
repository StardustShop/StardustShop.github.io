<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Stardust Shop</title>
  <script src="https://cdn.tailwindcss.com"></script>
  <script src="https://cdn.jsdelivr.net/npm/@supabase/supabase-js@2"></script>
</head>
<body class="bg-gradient-to-br from-indigo-900 via-purple-900 to-pink-900 min-h-screen">
  <div id="app"></div>

  <script>
    const { createClient } = window.supabase;
    
    // Initialize Supabase
    const supabaseClient = createClient(
      'https://vmbegeesynixyxbrupuu.supabase.co',
      'eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJpc3MiOiJzdXBhYmFzZSIsInJlZiI6InZtYmVnZWVzeW5peHl4YnJ1cHV1Iiwicm9sZSI6ImFub24iLCJpYXQiOjE3NjUwMzIyODgsImV4cCI6MjA4MDYwODI4OH0.Z1jclEv0N70Tsf7YcKwUcRgA73K-Jr5MTSWgJxsiZNE'
    );
    
    const ADMIN_EMAIL = 'stardustshopagere@gmail.com';
    
    let state = {
      products: [],
      cart: [],
      user: null,
      isMenuOpen: false,
      isCartOpen: false,
      selectedCategory: 'all',
      currentView: 'shop',
      authMode: 'login',
      email: '',
      password: '',
      authError: '',
      loading: true,
      checkoutStep: 1,
      shippingInfo: { name: '', address: '', city: '', state: '', zip: '', country: '' },
      paymentInfo: { cardNumber: '', cardName: '', expiry: '', cvv: '' },
      orderComplete: false,
      orderNumber: '',
      newProduct: { name: '', price: '', category: 'pacifiers', image: '🌟', description: '' }
    };

    // Initialize
    async function init() {
      await loadProducts();
      await checkUser();
      state.loading = false;
      render();
    }

    async function checkUser() {
      const { data: { session } } = await supabaseClient.auth.getSession();
      if (session?.user) {
        state.user = {
          email: session.user.email,
          isAdmin: session.user.email === ADMIN_EMAIL
        };
        await loadCart();
      }
    }

    async function loadProducts() {
      // For now, just use default products
      // Database integration can be added later once Supabase tables are set up
      state.products = getDefaultProducts();
    }

    function getDefaultProducts() {
      return [
        { id: 1, name: 'Starry Night Pacifier', price: 12.99, category: 'pacifiers', image: '🌟', description: 'Glow-in-the-dark pacifier with stars' },
        { id: 2, name: 'Moon Plushie', price: 24.99, category: 'plushies', image: '🌙', description: 'Soft and cuddly moon friend' },
        { id: 3, name: 'Galaxy Sippy Cup', price: 15.99, category: 'feeding', image: '🌌', description: 'Spill-proof cup with cosmic design' },
        { id: 4, name: 'Stardust Onesie', price: 29.99, category: 'clothing', image: '✨', description: 'Cozy onesie with star patterns' },
        { id: 5, name: 'Nebula Blanket', price: 34.99, category: 'comfort', image: '🌠', description: 'Ultra-soft blanket in pastel galaxy colors' },
        { id: 6, name: 'Cosmic Coloring Book', price: 8.99, category: 'activities', image: '🎨', description: 'Space-themed coloring pages' },
      ];
    }

    async function loadCart() {
      // Skip database cart loading for now - use in-memory cart only
      return;
    }

    async function saveCart() {
      // Skip database cart saving for now - use in-memory cart only
      return;
    }

    async function handleAuth(e) {
      e.preventDefault();
      state.authError = '';
      
      try {
        if (state.authMode === 'signup') {
          const { data, error } = await supabaseClient.auth.signUp({
            email: state.email,
            password: state.password,
          });
          
          if (error) throw error;
          
          state.authError = 'Check your email for verification link!';
          setTimeout(() => {
            state.authError = '';
            render();
          }, 3000);
        } else {
          const { data, error } = await supabaseClient.auth.signInWithPassword({
            email: state.email,
            password: state.password,
          });
          
          if (error) throw error;
          
          state.user = {
            email: data.user.email,
            isAdmin: data.user.email === ADMIN_EMAIL
          };
          state.currentView = 'shop';
          state.email = '';
          state.password = '';
          await loadCart();
        }
      } catch (err) {
        state.authError = err.message;
      }
      
      render();
    }

    async function handleLogout() {
      await supabaseClient.auth.signOut();
      state.user = null;
      state.cart = [];
      state.currentView = 'shop';
      state.isMenuOpen = false;
      render();
    }

    async function addProduct(e) {
      e.preventDefault();
      
      // Add to in-memory products list
      const newId = Math.max(...state.products.map(p => p.id), 0) + 1;
      const newProduct = {
        id: newId,
        name: state.newProduct.name,
        price: parseFloat(state.newProduct.price),
        category: state.newProduct.category,
        image: state.newProduct.image,
        description: state.newProduct.description
      };
      
      state.products.push(newProduct);
      state.newProduct = { name: '', price: '', category: 'pacifiers', image: '🌟', description: '' };
      render();
    }

    async function deleteProduct(id) {
      // Remove from in-memory products list
      state.products = state.products.filter(p => p.id !== id);
      render();
    }

    async function processCheckout() {
      const orderNum = 'SD' + Date.now();
      state.orderNumber = orderNum;
      
      // Clear cart
      state.cart = [];
      state.orderComplete = true;
      render();
    }

    function addToCart(product) {
      const existingItem = state.cart.find(item => item.id === product.id);
      if (existingItem) {
        state.cart = state.cart.map(item => 
          item.id === product.id 
            ? { ...item, quantity: item.quantity + 1 }
            : item
        );
      } else {
        state.cart.push({ ...product, quantity: 1 });
      }
      saveCart();
      render();
    }

    function removeFromCart(productId) {
      state.cart = state.cart.filter(item => item.id !== productId);
      saveCart();
      render();
    }

    function updateQuantity(productId, change) {
      state.cart = state.cart.map(item => {
        if (item.id === productId) {
          const newQuantity = item.quantity + change;
          return newQuantity > 0 ? { ...item, quantity: newQuantity } : item;
        }
        return item;
      }).filter(item => item.quantity > 0);
      saveCart();
      render();
    }

    function getCartTotal() {
      return state.cart.reduce((sum, item) => sum + (item.price * item.quantity), 0);
    }

    function getCartItemCount() {
      return state.cart.reduce((sum, item) => sum + item.quantity, 0);
    }

    function getFilteredProducts() {
      return state.selectedCategory === 'all' 
        ? state.products 
        : state.products.filter(p => p.category === state.selectedCategory);
    }

    const categories = ['all', 'pacifiers', 'plushies', 'feeding', 'clothing', 'comfort', 'activities'];

    function render() {
      const app = document.getElementById('app');
      
      if (state.currentView === 'auth') {
        app.innerHTML = renderAuthView();
        attachAuthListeners();
      } else if (state.currentView === 'checkout') {
        app.innerHTML = renderCheckoutView();
        attachCheckoutListeners();
      } else if (state.currentView === 'admin' && state.user?.isAdmin) {
        app.innerHTML = renderAdminView();
        attachAdminListeners();
      } else {
        app.innerHTML = renderShopView();
        attachShopListeners();
      }
    }

    function renderAuthView() {
      return `
        <div class="min-h-screen flex items-center justify-center p-4">
          <div class="bg-indigo-950/80 backdrop-blur-md rounded-3xl p-8 max-w-md w-full border-2 border-pink-300/20">
            <div class="text-center mb-8">
              <div class="text-6xl mb-4">⭐</div>
              <h2 class="text-3xl font-bold text-pink-200 mb-2">
                ${state.authMode === 'login' ? 'Welcome Back!' : 'Join Us!'}
              </h2>
              <p class="text-purple-200">
                ${state.authMode === 'login' ? 'Sign in to your account' : 'Create your account'}
              </p>
            </div>

            <form id="authForm" class="space-y-4">
              <div>
                <label class="block text-pink-200 mb-2">Email</label>
                <input
                  type="email"
                  id="emailInput"
                  value="${state.email}"
                  class="w-full px-4 py-3 rounded-lg bg-indigo-900/50 text-pink-100 border border-pink-300/20 focus:border-pink-300/50 focus:outline-none"
                  required
                />
              </div>
              <div>
                <label class="block text-pink-200 mb-2">Password</label>
                <input
                  type="password"
                  id="passwordInput"
                  value="${state.password}"
                  class="w-full px-4 py-3 rounded-lg bg-indigo-900/50 text-pink-100 border border-pink-300/20 focus:border-pink-300/50 focus:outline-none"
                  required
                />
              </div>

              ${state.authError ? `
                <div class="p-3 rounded-lg ${state.authError.includes('Check') ? 'bg-green-500/20 text-green-200' : 'bg-red-500/20 text-red-200'}">
                  ${state.authError}
                </div>
              ` : ''}

              <button
                type="submit"
                class="w-full bg-gradient-to-r from-pink-400 to-purple-400 text-white py-3 rounded-full font-bold hover:shadow-lg hover:shadow-pink-500/50 transition-all"
              >
                ${state.authMode === 'login' ? 'Sign In' : 'Sign Up'}
              </button>
            </form>

            <div class="mt-6 text-center">
              <button
                id="toggleAuthMode"
                type="button"
                class="text-purple-200 hover:text-pink-200"
              >
                ${state.authMode === 'login' ? "Don't have an account? Sign up" : 'Already have an account? Sign in'}
              </button>
            </div>

            <button
              id="continueAsGuest"
              type="button"
              class="mt-4 w-full text-purple-300 hover:text-pink-300"
            >
              Continue as guest
            </button>
          </div>
        </div>
      `;
    }

    // Event Listeners
    function attachAuthListeners() {
      const form = document.getElementById('authForm');
      const emailInput = document.getElementById('emailInput');
      const passwordInput = document.getElementById('passwordInput');
      const toggleBtn = document.getElementById('toggleAuthMode');
      const guestBtn = document.getElementById('continueAsGuest');

      emailInput.addEventListener('input', (e) => {
        state.email = e.target.value;
      });

      passwordInput.addEventListener('input', (e) => {
        state.password = e.target.value;
      });

      form.addEventListener('submit', handleAuth);

      toggleBtn.addEventListener('click', () => {
        state.authMode = state.authMode === 'login' ? 'signup' : 'login';
        state.authError = '';
        render();
      });

      guestBtn.addEventListener('click', () => {
        state.currentView = 'shop';
        state.authError = '';
        state.email = '';
        state.password = '';
        render();
      });
    }

    function attachCheckoutListeners() {
      const backBtn = document.getElementById('backToShop');
      const continueShoppingBtn = document.getElementById('continueShoppingBtn');

      if (backBtn) {
        backBtn.addEventListener('click', () => {
          state.currentView = 'shop';
          render();
        });
      }

      if (continueShoppingBtn) {
        continueShoppingBtn.addEventListener('click', () => {
          state.currentView = 'shop';
          state.orderComplete = false;
          state.checkoutStep = 1;
          render();
        });
      }

      if (state.checkoutStep === 1) {
        const continueBtn = document.getElementById('continueToPayment');
        continueBtn.addEventListener('click', () => {
          state.shippingInfo.name = document.getElementById('shipName').value;
          state.shippingInfo.address = document.getElementById('shipAddress').value;
          state.shippingInfo.city = document.getElementById('shipCity').value;
          state.shippingInfo.state = document.getElementById('shipState').value;
          state.shippingInfo.zip = document.getElementById('shipZip').value;
          state.shippingInfo.country = document.getElementById('shipCountry').value;
          state.checkoutStep = 2;
          render();
        });
      }

      if (state.checkoutStep === 2) {
        const backBtn = document.getElementById('backToShipping');
        const reviewBtn = document.getElementById('reviewOrder');

        backBtn.addEventListener('click', () => {
          state.checkoutStep = 1;
          render();
        });

        reviewBtn.addEventListener('click', () => {
          state.paymentInfo.cardNumber = document.getElementById('cardNumber').value;
          state.paymentInfo.cardName = document.getElementById('cardName').value;
          state.paymentInfo.expiry = document.getElementById('cardExpiry').value;
          state.paymentInfo.cvv = document.getElementById('cardCvv').value;
          state.checkoutStep = 3;
          render();
        });
      }

      if (state.checkoutStep === 3) {
        const backBtn = document.getElementById('backToPayment');
        const placeOrderBtn = document.getElementById('placeOrder');

        backBtn.addEventListener('click', () => {
          state.checkoutStep = 2;
          render();
        });

        placeOrderBtn.addEventListener('click', processCheckout);
      }
    }

    function attachAdminListeners() {
      const backBtn = document.getElementById('backToShopFromAdmin');
      const form = document.getElementById('addProductForm');

      backBtn.addEventListener('click', () => {
        state.currentView = 'shop';
        render();
      });

      document.getElementById('prodName').addEventListener('input', (e) => {
        state.newProduct.name = e.target.value;
      });

      document.getElementById('prodPrice').addEventListener('input', (e) => {
        state.newProduct.price = e.target.value;
      });

      document.getElementById('prodCategory').addEventListener('change', (e) => {
        state.newProduct.category = e.target.value;
      });

      document.getElementById('prodImage').addEventListener('input', (e) => {
        state.newProduct.image = e.target.value;
      });

      document.getElementById('prodDescription').addEventListener('input', (e) => {
        state.newProduct.description = e.target.value;
      });

      form.addEventListener('submit', addProduct);
    }

    function attachShopListeners() {
      const cartBtn = document.getElementById('cartBtn');
      const signInBtn = document.getElementById('signInBtn');
      const userMenuBtn = document.getElementById('userMenuBtn');
      const closeCartBtn = document.getElementById('closeCart');
      const cartOverlay = document.getElementById('cartOverlay');
      const checkoutBtn = document.getElementById('checkoutBtn');

      cartBtn.addEventListener('click', () => {
        state.isCartOpen = true;
        render();
      });

      if (closeCartBtn) {
        closeCartBtn.addEventListener('click', () => {
          state.isCartOpen = false;
          render();
        });
      }

      if (cartOverlay) {
        cartOverlay.addEventListener('click', () => {
          state.isCartOpen = false;
          render();
        });
      }

      if (checkoutBtn) {
        checkoutBtn.addEventListener('click', () => {
          state.currentView = 'checkout';
          state.isCartOpen = false;
          render();
        });
      }

      if (signInBtn) {
        signInBtn.addEventListener('click', () => {
          state.currentView = 'auth';
          state.isMenuOpen = false;
          render();
        });
      }

      if (userMenuBtn) {
        userMenuBtn.addEventListener('click', () => {
          state.isMenuOpen = !state.isMenuOpen;
          render();
        });
      }

      const adminBtn = document.getElementById('adminBtn');
      if (adminBtn) {
        adminBtn.addEventListener('click', () => {
          state.currentView = 'admin';
          state.isMenuOpen = false;
          render();
        });
      }

      const logoutBtn = document.getElementById('logoutBtn');
      if (logoutBtn) {
        logoutBtn.addEventListener('click', handleLogout);
      }

      document.querySelectorAll('.category-btn').forEach(btn => {
        btn.addEventListener('click', (e) => {
          state.selectedCategory = e.target.dataset.category;
          render();
        });
      });
    }

    function renderCheckoutView() {
      if (state.orderComplete) {
        return `
          <div class="min-h-screen flex items-center justify-center p-4">
            <div class="bg-indigo-950/80 backdrop-blur-md rounded-3xl p-8 max-w-md w-full border-2 border-pink-300/20 text-center">
              <div class="w-20 h-20 bg-green-500 rounded-full flex items-center justify-center mx-auto mb-6">
                <i data-lucide="check" class="w-12 h-12 text-white"></i>
              </div>
              <h2 class="text-3xl font-bold text-pink-200 mb-4">Order Complete! ✨</h2>
              <p class="text-purple-200 mb-2">Your order number is:</p>
              <p class="text-2xl font-bold text-yellow-300 mb-6">${state.orderNumber}</p>
              <p class="text-purple-200 mb-8">
                Thank you for shopping at Stardust Shop! We'll send you an email with tracking information soon.
              </p>
              <button
                id="continueShoppingBtn"
                class="w-full bg-gradient-to-r from-pink-400 to-purple-400 text-white py-3 rounded-full font-bold"
              >
                Continue Shopping
              </button>
            </div>
          </div>
        `;
      }

      return `
        <div class="min-h-screen p-4">
          <div class="container mx-auto max-w-2xl">
            <button id="backToShop" class="text-pink-200 hover:text-pink-100 mb-6 flex items-center gap-2">
              ← Back to Shop
            </button>

            <div class="bg-indigo-950/80 backdrop-blur-md rounded-3xl p-8 border-2 border-pink-300/20">
              <h2 class="text-3xl font-bold text-pink-200 mb-6">Checkout</h2>

              <div class="flex justify-between mb-8">
                ${[1, 2, 3].map(step => `
                  <div class="flex items-center">
                    <div class="${state.checkoutStep >= step ? 'bg-gradient-to-r from-pink-400 to-purple-400 text-white' : 'bg-indigo-900/50 text-purple-300'} w-10 h-10 rounded-full flex items-center justify-center font-bold">
                      ${step}
                    </div>
                    ${step < 3 ? '<div class="w-12 h-1 bg-indigo-900/50 mx-2"></div>' : ''}
                  </div>
                `).join('')}
              </div>

              ${state.checkoutStep === 1 ? renderShippingForm() : ''}
              ${state.checkoutStep === 2 ? renderPaymentForm() : ''}
              ${state.checkoutStep === 3 ? renderOrderReview() : ''}
            </div>
          </div>
        </div>
      `;
    }

    function renderShippingForm() {
      return `
        <div class="space-y-4">
          <h3 class="text-xl font-bold text-pink-200 mb-4">Shipping Information</h3>
          <input type="text" id="shipName" placeholder="Full Name" value="${state.shippingInfo.name}" class="w-full px-4 py-3 rounded-lg bg-indigo-900/50 text-pink-100 border border-pink-300/20" />
          <input type="text" id="shipAddress" placeholder="Address" value="${state.shippingInfo.address}" class="w-full px-4 py-3 rounded-lg bg-indigo-900/50 text-pink-100 border border-pink-300/20" />
          <div class="grid grid-cols-2 gap-4">
            <input type="text" id="shipCity" placeholder="City" value="${state.shippingInfo.city}" class="w-full px-4 py-3 rounded-lg bg-indigo-900/50 text-pink-100 border border-pink-300/20" />
            <input type="text" id="shipState" placeholder="State" value="${state.shippingInfo.state}" class="w-full px-4 py-3 rounded-lg bg-indigo-900/50 text-pink-100 border border-pink-300/20" />
          </div>
          <div class="grid grid-cols-2 gap-4">
            <input type="text" id="shipZip" placeholder="ZIP Code" value="${state.shippingInfo.zip}" class="w-full px-4 py-3 rounded-lg bg-indigo-900/50 text-pink-100 border border-pink-300/20" />
            <input type="text" id="shipCountry" placeholder="Country" value="${state.shippingInfo.country}" class="w-full px-4 py-3 rounded-lg bg-indigo-900/50 text-pink-100 border border-pink-300/20" />
          </div>
          <button id="continueToPayment" class="w-full bg-gradient-to-r from-pink-400 to-purple-400 text-white py-3 rounded-full font-bold mt-6">
            Continue to Payment
          </button>
        </div>
      `;
    }

    function renderPaymentForm() {
      return `
        <div class="space-y-4">
          <h3 class="text-xl font-bold text-pink-200 mb-4">Payment Information</h3>
          <input type="text" id="cardNumber" placeholder="Card Number" value="${state.paymentInfo.cardNumber}" class="w-full px-4 py-3 rounded-lg bg-indigo-900/50 text-pink-100 border border-pink-300/20" />
          <input type="text" id="cardName" placeholder="Cardholder Name" value="${state.paymentInfo.cardName}" class="w-full px-4 py-3 rounded-lg bg-indigo-900/50 text-pink-100 border border-pink-300/20" />
          <div class="grid grid-cols-2 gap-4">
            <input type="text" id="cardExpiry" placeholder="MM/YY" value="${state.paymentInfo.expiry}" class="w-full px-4 py-3 rounded-lg bg-indigo-900/50 text-pink-100 border border-pink-300/20" />
            <input type="text" id="cardCvv" placeholder="CVV" value="${state.paymentInfo.cvv}" class="w-full px-4 py-3 rounded-lg bg-indigo-900/50 text-pink-100 border border-pink-300/20" />
          </div>
          <div class="flex gap-4 mt-6">
            <button id="backToShipping" class="flex-1 bg-indigo-900/50 text-pink-200 py-3 rounded-full font-bold border border-pink-300/20">Back</button>
            <button id="reviewOrder" class="flex-1 bg-gradient-to-r from-pink-400 to-purple-400 text-white py-3 rounded-full font-bold">Review Order</button>
          </div>
        </div>
      `;
    }

    function renderOrderReview() {
      return `
        <div class="space-y-6">
          <h3 class="text-xl font-bold text-pink-200 mb-4">Review Your Order</h3>
          
          <div class="bg-indigo-900/50 rounded-lg p-4 border border-pink-300/20">
            <h4 class="text-pink-200 font-bold mb-2">Order Items</h4>
            ${state.cart.map(item => `
              <div class="flex justify-between text-purple-200 py-2">
                <span>${item.name} x${item.quantity}</span>
                <span>${(item.price * item.quantity).toFixed(2)}</span>
              </div>
            `).join('')}
            <div class="border-t border-pink-300/20 mt-2 pt-2 flex justify-between font-bold text-yellow-300">
              <span>Total:</span>
              <span>${getCartTotal().toFixed(2)}</span>
            </div>
          </div>

          <div class="bg-indigo-900/50 rounded-lg p-4 border border-pink-300/20">
            <h4 class="text-pink-200 font-bold mb-2">Shipping To</h4>
            <p class="text-purple-200">${state.shippingInfo.name}</p>
            <p class="text-purple-200">${state.shippingInfo.address}</p>
            <p class="text-purple-200">${state.shippingInfo.city}, ${state.shippingInfo.state} ${state.shippingInfo.zip}</p>
            <p class="text-purple-200">${state.shippingInfo.country}</p>
          </div>

          <div class="flex gap-4">
            <button id="backToPayment" class="flex-1 bg-indigo-900/50 text-pink-200 py-3 rounded-full font-bold border border-pink-300/20">Back</button>
            <button id="placeOrder" class="flex-1 bg-gradient-to-r from-pink-400 to-purple-400 text-white py-3 rounded-full font-bold">Place Order ✨</button>
          </div>
        </div>
      `;
    }

    function renderAdminView() {
      return `
        <div class="min-h-screen">
          <header class="bg-indigo-950/50 backdrop-blur-md border-b border-pink-300/20 p-4">
            <div class="container mx-auto flex items-center justify-between">
              <h1 class="text-2xl font-bold text-pink-200">Admin Dashboard</h1>
              <button id="backToShopFromAdmin" class="text-pink-200 hover:text-pink-100">Back to Shop</button>
            </div>
          </header>

          <div class="container mx-auto p-4 max-w-6xl">
            <div class="grid md:grid-cols-2 gap-8">
              <div class="bg-indigo-950/50 backdrop-blur-md rounded-3xl p-6 border-2 border-pink-300/20">
                <h2 class="text-2xl font-bold text-pink-200 mb-6">Add New Product</h2>
                <form id="addProductForm" class="space-y-4">
                  <input type="text" id="prodName" placeholder="Product Name" value="${state.newProduct.name}" class="w-full px-4 py-3 rounded-lg bg-indigo-900/50 text-pink-100 border border-pink-300/20" required />
                  <input type="number" step="0.01" id="prodPrice" placeholder="Price" value="${state.newProduct.price}" class="w-full px-4 py-3 rounded-lg bg-indigo-900/50 text-pink-100 border border-pink-300/20" required />
                  <select id="prodCategory" class="w-full px-4 py-3 rounded-lg bg-indigo-900/50 text-pink-100 border border-pink-300/20">
                    ${categories.filter(c => c !== 'all').map(cat => `
                      <option value="${cat}" ${state.newProduct.category === cat ? 'selected' : ''}>${cat}</option>
                    `).join('')}
                  </select>
                  <input type="text" id="prodImage" placeholder="Emoji (e.g., 🌟)" value="${state.newProduct.image}" class="w-full px-4 py-3 rounded-lg bg-indigo-900/50 text-pink-100 border border-pink-300/20" required />
                  <textarea id="prodDescription" placeholder="Description" rows="3" class="w-full px-4 py-3 rounded-lg bg-indigo-900/50 text-pink-100 border border-pink-300/20" required>${state.newProduct.description}</textarea>
                  <button type="submit" class="w-full bg-gradient-to-r from-pink-400 to-purple-400 text-white py-3 rounded-full font-bold">Add Product</button>
                </form>
              </div>

              <div class="bg-indigo-950/50 backdrop-blur-md rounded-3xl p-6 border-2 border-pink-300/20">
                <h2 class="text-2xl font-bold text-pink-200 mb-6">Manage Products</h2>
                <div class="space-y-3 max-h-[600px] overflow-y-auto">
                  ${state.products.map(product => `
                    <div class="bg-indigo-900/50 rounded-lg p-4 flex items-center justify-between">
                      <div class="flex items-center gap-3">
                        <span class="text-3xl">${product.image}</span>
                        <div>
                          <p class="text-pink-200 font-bold">${product.name}</p>
                          <p class="text-purple-300 text-sm">${product.price.toFixed(2)}</p>
                        </div>
                      </div>
                      <button onclick="deleteProduct(${product.id})" class="text-red-400 hover:text-red-300">
                        <i data-lucide="trash-2" class="w-5 h-5"></i>
                      </button>
                    </div>
                  `).join('')}
                </div>
              </div>
            </div>
          </div>
        </div>
      `;
    }

    function renderShopView() {
      const filteredProducts = getFilteredProducts();
      const cartItemCount = getCartItemCount();
      const cartTotal = getCartTotal();
      
      return `
        <div class="min-h-screen">
          <!-- Starry Background -->
          <div class="fixed inset-0 overflow-hidden pointer-events-none">
            ${Array.from({length: 50}, (_, i) => `
              <div class="absolute bg-white rounded-full animate-pulse" style="width: ${Math.random() * 3 + 1}px; height: ${Math.random() * 3 + 1}px; top: ${Math.random() * 100}%; left: ${Math.random() * 100}%; animation-delay: ${Math.random() * 3}s; animation-duration: ${Math.random() * 3 + 2}s;"></div>
            `).join('')}
          </div>

          <!-- Header -->
          <header class="relative bg-indigo-950/50 backdrop-blur-md border-b border-pink-300/20 sticky top-0 z-50">
            <div class="container mx-auto px-4 py-4">
              <div class="flex items-center justify-between">
                <div class="flex items-center gap-3">
                  <div class="w-12 h-12 bg-gradient-to-br from-pink-300 to-purple-300 rounded-full flex items-center justify-center">
                    <i data-lucide="star" class="text-indigo-900 fill-current"></i>
                  </div>
                  <h1 class="text-3xl font-bold bg-gradient-to-r from-pink-300 via-purple-300 to-blue-300 bg-clip-text text-transparent">
                    Stardust Shop
                  </h1>
                </div>

                <div class="flex items-center gap-4">
                  <button id="cartBtn" class="relative text-pink-200 hover:text-pink-100 transition-colors">
                    <i data-lucide="shopping-cart" class="w-6 h-6"></i>
                    ${cartItemCount > 0 ? `
                      <span class="absolute -top-1 -right-1 bg-yellow-300 text-indigo-900 rounded-full w-5 h-5 flex items-center justify-center text-xs font-bold">
                        ${cartItemCount}
                      </span>
                    ` : ''}
                  </button>

                  ${state.user ? `
                    <div class="relative">
                      <button id="userMenuBtn" class="flex items-center gap-2 text-pink-200 hover:text-pink-100 transition-colors">
                        <i data-lucide="user" class="w-6 h-6"></i>
                        <span class="hidden md:inline">${state.user.email}</span>
                      </button>
                      
                      ${state.isMenuOpen ? `
                        <div id="userMenuDropdown" class="absolute right-0 mt-2 w-48 bg-indigo-950/95 backdrop-blur-md rounded-lg border border-pink-300/20 shadow-xl overflow-hidden z-50">
                          <div class="py-2">
                            <div class="px-4 py-2 text-purple-200 text-sm border-b border-pink-300/20">
                              ${state.user.email}
                            </div>
                            ${state.user.isAdmin ? `
                              <button id="adminBtn" class="w-full text-left px-4 py-2 text-pink-200 hover:bg-indigo-900/50 transition-colors flex items-center gap-2">
                                <i data-lucide="settings" class="w-4 h-4"></i>
                                Admin Dashboard
                              </button>
                            ` : ''}
                            <button id="logoutBtn" class="w-full text-left px-4 py-2 text-pink-200 hover:bg-indigo-900/50 transition-colors flex items-center gap-2">
                              <i data-lucide="log-out" class="w-4 h-4"></i>
                              Logout
                            </button>
                          </div>
                        </div>
                      ` : ''}
                    </div>
                  ` : `
                    <button id="signInBtn" class="flex items-center gap-2 text-pink-200 hover:text-pink-100 transition-colors">
                      <i data-lucide="log-in" class="w-6 h-6"></i>
                      <span class="hidden md:inline">Sign In</span>
                    </button>
                  `}
                </div>
              </div>
            </div>
          </header>

          <!-- Hero Section -->
          <section class="relative py-20 px-4">
            <div class="container mx-auto text-center">
              <div class="flex justify-center mb-6">
                <div class="w-48 h-48 rounded-full shadow-2xl shadow-pink-500/50 bg-gradient-to-br from-indigo-900 via-purple-900 to-pink-900 border-4 border-pink-300/30 flex items-center justify-center">
                  <div class="text-center">
                    <i data-lucide="star" class="w-12 h-12 text-yellow-300 mx-auto mb-2 fill-current"></i>
                    <i data-lucide="moon" class="w-8 h-8 text-blue-300 mx-auto fill-current"></i>
                  </div>
                </div>
              </div>
              <h2 class="text-5xl md:text-6xl font-bold text-pink-200 mb-4">
                Welcome to Stardust Shop
              </h2>
              <p class="text-xl text-purple-200 mb-8 max-w-2xl mx-auto">
                Your magical destination for age regression comfort items. 
                Where stars shine bright and dreams come true! ✨
              </p>
              <div class="flex gap-4 justify-center">
                <i data-lucide="sparkles" class="text-yellow-300 animate-pulse"></i>
                <i data-lucide="moon" class="text-blue-300 animate-bounce"></i>
                <i data-lucide="heart" class="text-pink-300 animate-pulse"></i>
              </div>
            </div>
          </section>

          <!-- Category Filter -->
          <section class="relative px-4 pb-8">
            <div class="container mx-auto">
              <div class="flex flex-wrap gap-3 justify-center">
                ${categories.map(category => `
                  <button data-category="${category}" class="category-btn px-6 py-2 rounded-full font-medium transition-all ${
                    state.selectedCategory === category
                      ? 'bg-gradient-to-r from-pink-400 to-purple-400 text-white shadow-lg shadow-pink-500/50'
                      : 'bg-indigo-950/50 text-pink-200 hover:bg-indigo-900/70 border border-pink-300/20'
                  }">
                    ${category.charAt(0).toUpperCase() + category.slice(1)}
                  </button>
                `).join('')}
              </div>
            </div>
          </section>

          <!-- Products Grid -->
          <section id="products" class="relative py-12 px-4">
            <div class="container mx-auto">
              <h2 class="text-4xl font-bold text-center text-pink-200 mb-12">
                Our Magical Collection
              </h2>
              
              ${state.loading ? `
                <div class="text-center text-pink-200 text-xl">
                  <i data-lucide="sparkles" class="w-12 h-12 animate-spin mx-auto mb-4"></i>
                  Loading magical items...
                </div>
              ` : `
                <div class="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-3 gap-8">
                  ${filteredProducts.map(product => `
                    <div class="bg-indigo-950/50 backdrop-blur-md rounded-3xl overflow-hidden border-2 border-pink-300/20 hover:border-pink-300/50 transition-all hover:shadow-xl hover:shadow-pink-500/30 hover:scale-105">
                      <div class="h-48 bg-gradient-to-br from-purple-500/30 to-pink-500/30 flex items-center justify-center text-8xl">
                        ${product.image}
                      </div>
                      <div class="p-6">
                        <h3 class="text-2xl font-bold text-pink-200 mb-2">
                          ${product.name}
                        </h3>
                        <p class="text-purple-200 mb-4">${product.description}</p>
                        <div class="flex items-center justify-between">
                          <span class="text-3xl font-bold text-yellow-300">
                            ${product.price.toFixed(2)}
                          </span>
                          <button onclick="addToCart(${JSON.stringify(product).replace(/"/g, '&quot;')})" class="bg-gradient-to-r from-pink-400 to-purple-400 text-white px-6 py-2 rounded-full hover:shadow-lg hover:shadow-pink-500/50 transition-all flex items-center gap-2">
                            <i data-lucide="shopping-cart" class="w-4 h-4"></i>
                            Add
                          </button>
                        </div>
                      </div>
                    </div>
                  `).join('')}
                </div>
              `}
            </div>
          </section>

          <!-- About Section -->
          <section id="about" class="relative py-20 px-4 bg-indigo-950/30">
            <div class="container mx-auto max-w-3xl text-center">
              <h2 class="text-4xl font-bold text-pink-200 mb-6">About Stardust Shop</h2>
              <p class="text-lg text-purple-200 leading-relaxed mb-6">
                Stardust Shop is a safe, welcoming space dedicated to providing quality age regression items 
                for the little space community. We believe everyone deserves comfort, joy, and a sprinkle 
                of magic in their lives.
              </p>
              <p class="text-lg text-purple-200 leading-relaxed">
                Each item is carefully selected to bring comfort and happiness to your little space journey. 
                We're here to support you with love and understanding. 💫
              </p>
            </div>
          </section>

          <!-- Footer -->
          <footer class="relative py-12 px-4 bg-indigo-950/50 border-t border-pink-300/20">
            <div class="container mx-auto text-center">
              <div class="flex justify-center gap-4 mb-6">
                <i data-lucide="star" class="text-yellow-300 fill-current"></i>
                <i data-lucide="moon" class="text-blue-300 fill-current"></i>
                <i data-lucide="sparkles" class="text-pink-300"></i>
              </div>
              <p class="text-pink-200 mb-2">© 2024 Stardust Shop. Made with love and stardust ✨</p>
              <p class="text-purple-300 text-sm">
                Safe Space • Judgment-Free • Always Here for You
              </p>
            </div>
          </footer>

          <!-- Cart Sidebar -->
          ${state.isCartOpen ? `
            <div id="cartOverlay" class="fixed inset-0 bg-black/50 backdrop-blur-sm z-50"></div>
            <div class="fixed right-0 top-0 h-full w-full md:w-96 bg-gradient-to-b from-indigo-950 to-purple-950 shadow-2xl z-50 overflow-y-auto">
              <div class="p-6">
                <div class="flex items-center justify-between mb-6">
                  <h2 class="text-3xl font-bold text-pink-200 flex items-center gap-2">
                    <i data-lucide="shopping-cart"></i>
                    Your Cart
                  </h2>
                  <button id="closeCart" class="text-pink-200 hover:text-pink-100">
                    <i data-lucide="x" class="w-7 h-7"></i>
                  </button>
                </div>

                ${state.cart.length === 0 ? `
                  <div class="text-center py-12">
                    <i data-lucide="moon" class="w-16 h-16 text-blue-300 mx-auto mb-4 opacity-50"></i>
                    <p class="text-purple-200 text-lg">Your cart is empty</p>
                    <p class="text-purple-300 text-sm mt-2">Add some magical items! ✨</p>
                  </div>
                ` : `
                  <div class="space-y-4 mb-6">
                    ${state.cart.map(item => `
                      <div class="bg-indigo-900/50 rounded-lg p-4 border border-pink-300/20">
                        <div class="flex items-start gap-3">
                          <div class="text-4xl">${item.image}</div>
                          <div class="flex-1">
                            <h3 class="text-pink-200 font-bold">${item.name}</h3>
                            <p class="text-yellow-300 font-bold">${item.price.toFixed(2)}</p>
                            <div class="flex items-center gap-3 mt-2">
                              <button onclick="updateQuantity(${item.id}, -1)" class="bg-purple-500 hover:bg-purple-600 text-white rounded-full p-1">
                                <i data-lucide="minus" class="w-4 h-4"></i>
                              </button>
                              <span class="text-pink-200 font-bold">${item.quantity}</span>
                              <button onclick="updateQuantity(${item.id}, 1)" class="bg-purple-500 hover:bg-purple-600 text-white rounded-full p-1">
                                <i data-lucide="plus" class="w-4 h-4"></i>
                              </button>
                              <button onclick="removeFromCart(${item.id})" class="ml-auto text-red-400 hover:text-red-300">
                                <i data-lucide="trash-2" class="w-5 h-5"></i>
                              </button>
                            </div>
                          </div>
                        </div>
                      </div>
                    `).join('')}
                  </div>

                  <div class="border-t border-pink-300/20 pt-4">
                    <div class="flex items-center justify-between mb-4">
                      <span class="text-2xl font-bold text-pink-200">Total:</span>
                      <span class="text-3xl font-bold text-yellow-300">
                        ${cartTotal.toFixed(2)}
                      </span>
                    </div>
                    <button id="checkoutBtn" class="w-full bg-gradient-to-r from-pink-400 to-purple-400 text-white py-4 rounded-full font-bold text-lg hover:shadow-lg hover:shadow-pink-500/50 transition-all flex items-center justify-center gap-2">
                      <i data-lucide="credit-card" class="w-5 h-5"></i>
                      Checkout ✨
                    </button>
                  </div>
                `}
              </div>
            </div>
          ` : ''}
        </div>
      `;
    }

    // Make functions available globally
    window.addToCart = addToCart;
    window.removeFromCart = removeFromCart;
    window.updateQuantity = updateQuantity;
    window.deleteProduct = deleteProduct;

    // Start the app
    init();
  </script>
</body>
</html>
