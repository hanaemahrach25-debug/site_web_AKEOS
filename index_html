// ============================================
// CONFIGURATION & VARIABLES GLOBALES
// ============================================

const CONFIG = {
    email: 'best.ecom01@gmail.com',
    phone: '+212641552414',
    whatsapp: 'https://whatsapp.com/channel/0029VbCJaPb9hXFB2mFqzf42',
    instagram: 'https://www.instagram.com/akeos_store',
    
    // Prix ateliers (tous à 200 DH)
    workshopPrices: {
        initiation: 200,
        reparation: 200,
        customisation: 200,
        avance: 200
    },
    
    // Impact CO2 moyen par article
    co2PerItem: 2.5,
    
    // Animations
    scrollOffset: 100,
    counterSpeed: 2000,
    
    // Cart
    cart: [],
    cartTotal: 0
};

// ============================================
// HEADER STICKY & NAVIGATION
// ============================================

const header = document.getElementById('header');
const navMenu = document.getElementById('navMenu');
const burgerMenu = document.getElementById('burgerMenu');
const navLinks = document.querySelectorAll('.nav-link');

// Header scroll effect
window.addEventListener('scroll', () => {
    if (window.scrollY > 100) {
        header.classList.add('scrolled');
    } else {
        header.classList.remove('scrolled');
    }
});

// Burger menu toggle
burgerMenu?.addEventListener('click', () => {
    navMenu.classList.toggle('active');
    burgerMenu.classList.toggle('active');
});

// Smooth scroll & active link
navLinks.forEach(link => {
    link.addEventListener('click', (e) => {
        e.preventDefault();
        const targetId = link.getAttribute('href');
        const targetSection = document.querySelector(targetId);
        
        if (targetSection) {
            const offsetTop = targetSection.offsetTop - 80;
            window.scrollTo({
                top: offsetTop,
                behavior: 'smooth'
            });
            
            // Close mobile menu
            navMenu.classList.remove('active');
            burgerMenu.classList.remove('active');
            
            // Update active link
            navLinks.forEach(l => l.classList.remove('active'));
            link.classList.add('active');
        }
    });
});

// Scroll spy - highlight active section
window.addEventListener('scroll', () => {
    let current = '';
    const sections = document.querySelectorAll('.section, .hero');
    
    sections.forEach(section => {
        const sectionTop = section.offsetTop;
        const sectionHeight = section.clientHeight;
        if (window.scrollY >= (sectionTop - 200)) {
            current = section.getAttribute('id');
        }
    });
    
    navLinks.forEach(link => {
        link.classList.remove('active');
        if (link.getAttribute('href') === `#${current}`) {
            link.classList.add('active');
        }
    });
});

// ============================================
// HERO SLIDER
// ============================================

const heroSlides = document.querySelectorAll('.hero-slide');
let currentSlide = 0;

function nextSlide() {
    heroSlides[currentSlide].classList.remove('active');
    currentSlide = (currentSlide + 1) % heroSlides.length;
    heroSlides[currentSlide].classList.add('active');
}

// Auto-slide every 5 seconds
if (heroSlides.length > 1) {
    setInterval(nextSlide, 5000);
}

// ============================================
// ANIMATED COUNTERS
// ============================================

function animateCounter(element, target, duration) {
    let start = 0;
    const increment = target / (duration / 16); // 60fps
    
    const timer = setInterval(() => {
        start += increment;
        if (start >= target) {
            element.textContent = Math.floor(target).toLocaleString();
            clearInterval(timer);
        } else {
            element.textContent = Math.floor(start).toLocaleString();
        }
    }, 16);
}

// Initialize counters when in viewport
const counterElements = document.querySelectorAll('.counter-value');
let countersActivated = false;

const observerOptions = {
    threshold: 0.5
};

const counterObserver = new IntersectionObserver((entries) => {
    entries.forEach(entry => {
        if (entry.isIntersecting && !countersActivated) {
            countersActivated = true;
            counterElements.forEach(counter => {
                const target = parseInt(counter.getAttribute('data-target'));
                animateCounter(counter, target, CONFIG.counterSpeed);
            });
        }
    });
}, observerOptions);

// Observe first counter section
const firstCounter = document.querySelector('.impact-counter');
if (firstCounter) {
    counterObserver.observe(firstCounter);
}

// ============================================
// BOUTIQUE - FILTRES & PRODUITS
// ============================================

const filterBtns = document.querySelectorAll('.filter-btn');
const productCards = document.querySelectorAll('.product-card');
const sortSelect = document.querySelector('.sort-select');

// Filter products
filterBtns.forEach(btn => {
    btn.addEventListener('click', () => {
        // Update active button
        filterBtns.forEach(b => b.classList.remove('active'));
        btn.classList.add('active');
        
        const filter = btn.getAttribute('data-filter');
        
        productCards.forEach(card => {
            if (filter === 'all' || card.getAttribute('data-category') === filter) {
                card.style.display = 'block';
                setTimeout(() => {
                    card.style.opacity = '1';
                    card.style.transform = 'scale(1)';
                }, 10);
            } else {
                card.style.opacity = '0';
                card.style.transform = 'scale(0.9)';
                setTimeout(() => {
                    card.style.display = 'none';
                }, 300);
            }
        });
    });
});

// Sort products
sortSelect?.addEventListener('change', (e) => {
    const sortValue = e.target.value;
    const productsGrid = document.getElementById('productsGrid');
    const products = Array.from(productCards);
    
    products.sort((a, b) => {
        switch (sortValue) {
            case 'prix-asc':
                return getPrice(a) - getPrice(b);
            case 'prix-desc':
                return getPrice(b) - getPrice(a);
            case 'impact':
                return getImpact(b) - getImpact(a);
            default:
                return 0;
        }
    });
    
    products.forEach(product => productsGrid.appendChild(product));
});

function getPrice(card) {
    const priceText = card.querySelector('.product-price').textContent;
    return parseInt(priceText.replace(/\D/g, ''));
}

function getImpact(card) {
    const impactText = card.querySelector('.product-impact').textContent;
    return parseFloat(impactText.replace(/[^\d.]/g, ''));
}

// ============================================
// PANIER
// ============================================

const cartBtn = document.getElementById('cartBtn');
const cartModal = document.getElementById('cartModal');
const cartCount = document.querySelector('.cart-count');
const cartItems = document.getElementById('cartItems');
const cartTotal = document.getElementById('cartTotal');
const cartImpact = document.getElementById('cartImpact');
const addToCartBtns = document.querySelectorAll('.btn-add-cart');

// Open cart
cartBtn?.addEventListener('click', () => {
    openModal('cartModal');
    updateCartDisplay();
});

// Add to cart
addToCartBtns.forEach(btn => {
    btn.addEventListener('click', (e) => {
        const productCard = e.target.closest('.product-card');
        const productId = btn.getAttribute('data-product-id');
        const productName = productCard.querySelector('.product-name').textContent;
        const productPrice = getPrice(productCard);
        const productImpact = getImpact(productCard);
        const productImg = productCard.querySelector('.product-image img').src;
        
        addToCart({
            id: productId,
            name: productName,
            price: productPrice,
            impact: productImpact,
            image: productImg,
            quantity: 1
        });
        
        // Show feedback
        btn.innerHTML = '<i class="fas fa-check"></i> Ajouté !';
        btn.style.background = '#4CAF50';
        
        setTimeout(() => {
            btn.innerHTML = '<i class="fas fa-shopping-bag"></i> Ajouter au panier';
            btn.style.background = '';
        }, 2000);
    });
});

function addToCart(product) {
    const existingItem = CONFIG.cart.find(item => item.id === product.id);
    
    if (existingItem) {
        existingItem.quantity++;
    } else {
        CONFIG.cart.push(product);
    }
    
    updateCartCount();
    saveCart();
}

function removeFromCart(productId) {
    CONFIG.cart = CONFIG.cart.filter(item => item.id !== productId);
    updateCartCount();
    updateCartDisplay();
    saveCart();
}

function updateCartCount() {
    const totalItems = CONFIG.cart.reduce((sum, item) => sum + item.quantity, 0);
    cartCount.textContent = totalItems;
}

function updateCartDisplay() {
    if (CONFIG.cart.length === 0) {
        cartItems.innerHTML = '<p class="empty-cart">Votre panier est vide</p>';
        cartTotal.textContent = '0 DH';
        cartImpact.textContent = '0 kg';
        return;
    }
    
    let html = '';
    let total = 0;
    let totalImpact = 0;
    
    CONFIG.cart.forEach(item => {
        const itemTotal = item.price * item.quantity;
        total += itemTotal;
        totalImpact += item.impact * item.quantity;
        
        html += `
            <div class="cart-item">
                <img src="${item.image}" alt="${item.name}">
                <div class="cart-item-info">
                    <h4>${item.name}</h4>
                    <p>${item.price} DH × ${item.quantity}</p>
                </div>
                <div class="cart-item-actions">
                    <button onclick="changeQuantity('${item.id}', -1)">-</button>
                    <span>${item.quantity}</span>
                    <button onclick="changeQuantity('${item.id}', 1)">+</button>
                </div>
                <button class="remove-item" onclick="removeFromCart('${item.id}')">
                    <i class="fas fa-trash"></i>
                </button>
            </div>
        `;
    });
    
    cartItems.innerHTML = html;
    cartTotal.textContent = `${total} DH`;
    cartImpact.textContent = `${totalImpact.toFixed(1)} kg`;
}

function changeQuantity(productId, delta) {
    const item = CONFIG.cart.find(i => i.id === productId);
    if (item) {
        item.quantity += delta;
        if (item.quantity <= 0) {
            removeFromCart(productId);
        } else {
            updateCartCount();
            updateCartDisplay();
            saveCart();
        }
    }
}

function saveCart() {
    localStorage.setItem('akeos_cart', JSON.stringify(CONFIG.cart));
}

function loadCart() {
    const saved = localStorage.getItem('akeos_cart');
    if (saved) {
        CONFIG.cart = JSON.parse(saved);
        updateCartCount();
    }
}

// Load cart on page load
loadCart();

// ============================================
// ATELIERS - CALENDRIER
// ============================================

const calendarGrid = document.getElementById('calendarGrid');

function generateCalendar() {
    const workshops = [
        {
            date: '2025-03-15',
            time: '10:00',
            type: 'Initiation Couture',
            price: 200,
            slots: '5/10'
        },
        {
            date: '2025-03-18',
            time: '14:00',
            type: 'Réparation & Retouche',
            price: 200,
            slots: '8/10'
        },
        {
            date: '2025-03-22',
            time: '10:00',
            type: 'Customisation & Upcycling',
            price: 200,
            slots: '3/10'
        },
        {
            date: '2025-03-25',
            time: '14:00',
            type: 'Atelier Avancé',
            price: 200,
            slots: '2/8'
        }
    ];
    
    let html = '';
    
    workshops.forEach(workshop => {
        const date = new Date(workshop.date);
        const dateStr = date.toLocaleDateString('fr-FR', { 
            weekday: 'short', 
            day: 'numeric', 
            month: 'short' 
        });
        
        const isFull = workshop.slots.split('/')[0] === workshop.slots.split('/')[1];
        
        html += `
            <div class="calendar-event ${isFull ? 'full' : ''}">
                <div class="event-date">${dateStr}</div>
                <div class="event-time">${workshop.time}</div>
                <h4>${workshop.type}</h4>
                <div class="event-meta">
                    <span class="event-price">${workshop.price} DH</span>
                    <span class="event-slots">${workshop.slots} places</span>
                </div>
                <button class="btn btn-primary ${isFull ? 'disabled' : ''}" 
                        ${isFull ? 'disabled' : ''} 
                        onclick="openWorkshopBooking('${workshop.type}', '${workshop.date}', '${workshop.time}', ${workshop.price})">
                    ${isFull ? 'Complet' : 'Réserver'}
                </button>
            </div>
        `;
    });
    
    if (calendarGrid) {
        calendarGrid.innerHTML = html;
    }
}

function openWorkshopBooking(type, date, time, price) {
    // Ouvrir modal de réservation
    alert(`Réservation pour: ${type}\nDate: ${date} à ${time}\nPrix: ${price} DH\n\nFonctionnalité en développement...`);
}

generateCalendar();

// ============================================
// FAQ ACCORDION
// ============================================

const faqQuestions = document.querySelectorAll('.faq-question');

faqQuestions.forEach(question => {
    question.addEventListener('click', () => {
        const faqItem = question.parentElement;
        const isActive = faqItem.classList.contains('active');
        
        // Close all FAQs
        document.querySelectorAll('.faq-item').forEach(item => {
            item.classList.remove('active');
        });
        
        // Open clicked FAQ if it wasn't active
        if (!isActive) {
            faqItem.classList.add('active');
        }
    });
});

// ============================================
// IMPACT CALCULATOR
// ============================================

const calculateBtn = document.getElementById('calculateBtn');
const itemsCountInput = document.getElementById('itemsCount');
const calculatorResults = document.getElementById('calculatorResults');

calculateBtn?.addEventListener('click', () => {
    const itemsCount = parseInt(itemsCountInput.value) || 1;
    
    // Calculs
    const co2Saved = (itemsCount * CONFIG.co2PerItem).toFixed(1);
    const treeEquivalent = Math.floor(itemsCount * 0.12); // ~0.12 arbre par article
    const carEquivalent = Math.floor((itemsCount * CONFIG.co2PerItem) / 2); // ~2kg CO2 par jour en voiture
    
    // Afficher résultats
    calculatorResults.style.display = 'grid';
    
    document.getElementById('co2Saved').textContent = co2Saved;
    document.getElementById('treeEquivalent').textContent = treeEquivalent;
    document.getElementById('carEquivalent').textContent = carEquivalent;
    
    // Animation
    calculatorResults.style.opacity = '0';
    setTimeout(() => {
        calculatorResults.style.opacity = '1';
    }, 100);
});

// ============================================
// FORMULAIRE CONTACT
// ============================================

const contactForm = document.getElementById('contactForm');

contactForm?.addEventListener('submit', async (e) => {
    e.preventDefault();
    
    const formData = {
        firstName: document.getElementById('firstName').value,
        lastName: document.getElementById('lastName').value,
        email: document.getElementById('email').value,
        phone: document.getElementById('phone').value,
        subject: document.getElementById('subject').value,
        message: document.getElementById('message').value
    };
    
    // Simuler envoi
    console.log('Form data:', formData);
    
    // Afficher confirmation
    alert('Message envoyé ! Nous vous répondrons sous 24-48h.');
    contactForm.reset();
    
    // TODO: Implémenter l'envoi réel via backend/API
});

// ============================================
// NEWSLETTER
// ============================================

const newsletterForm = document.getElementById('newsletterForm');

newsletterForm?.addEventListener('submit', (e) => {
    e.preventDefault();
    
    const email = e.target.querySelector('input[type="email"]').value;
    console.log('Newsletter signup:', email);
    
    alert('Merci de vous être inscrit à notre newsletter !');
    newsletterForm.reset();
    
    // TODO: Implémenter l'envoi réel via backend/API
});

// ============================================
// MODALS
// ============================================

function openModal(modalId) {
    const modal = document.getElementById(modalId);
    if (modal) {
        modal.classList.add('active');
        document.body.style.overflow = 'hidden';
    }
}

function closeModal(modalId) {
    const modal = document.getElementById(modalId);
    if (modal) {
        modal.classList.remove('active');
        document.body.style.overflow = '';
    }
}

// Close buttons
document.querySelectorAll('.modal-close').forEach(btn => {
    btn.addEventListener('click', (e) => {
        const modalId = btn.getAttribute('data-modal');
        closeModal(modalId);
    });
});

// Close on outside click
document.querySelectorAll('.modal').forEach(modal => {
    modal.addEventListener('click', (e) => {
        if (e.target === modal) {
            closeModal(modal.id);
        }
    });
});

// Close on Escape key
document.addEventListener('keydown', (e) => {
    if (e.key === 'Escape') {
        document.querySelectorAll('.modal.active').forEach(modal => {
            closeModal(modal.id);
        });
    }
});

// User/Login button
const userBtn = document.getElementById('userBtn');
userBtn?.addEventListener('click', () => {
    openModal('loginModal');
});

// Auth tabs
const authTabs = document.querySelectorAll('.auth-tab');
const authForms = document.querySelectorAll('.auth-form');

authTabs.forEach(tab => {
    tab.addEventListener('click', () => {
        const targetTab = tab.getAttribute('data-tab');
        
        authTabs.forEach(t => t.classList.remove('active'));
        tab.classList.add('active');
        
        authForms.forEach(form => {
            form.classList.remove('active');
            if (form.id === `${targetTab}Form`) {
                form.classList.add('active');
            }
        });
    });
});

// ============================================
// SCROLL TO TOP BUTTON
// ============================================

const scrollTopBtn = document.getElementById('scrollTopBtn');

window.addEventListener('scroll', () => {
    if (window.scrollY > 500) {
        scrollTopBtn.classList.add('show');
    } else {
        scrollTopBtn.classList.remove('show');
    }
});

scrollTopBtn?.addEventListener('click', () => {
    window.scrollTo({
        top: 0,
        behavior: 'smooth'
    });
});

// ============================================
// WISHLIST
// ============================================

const wishlistBtns = document.querySelectorAll('.wishlist-btn');

wishlistBtns.forEach(btn => {
    btn.addEventListener('click', (e) => {
        e.stopPropagation();
        btn.classList.toggle('active');
        
        // TODO: Save to localStorage
        if (btn.classList.contains('active')) {
            console.log('Added to wishlist');
        } else {
            console.log('Removed from wishlist');
        }
    });
});

// ============================================
// LOAD MORE PRODUCTS
// ============================================

const loadMoreBtn = document.getElementById('loadMoreBtn');
let productsToShow = 8;

loadMoreBtn?.addEventListener('click', () => {
    productsToShow += 4;
    // TODO: Load more products dynamically
    console.log('Loading more products...');
});

// ============================================
// INSTAGRAM FEED (Simulation)
// ============================================

function loadInstagramFeed() {
    const instagramGrid = document.getElementById('instagramGrid');
    if (!instagramGrid) return;
    
    // Simuler le chargement de posts Instagram
    const posts = [
        { image: 'images/instagram/post-1.jpg', likes: 245 },
        { image: 'images/instagram/post-2.jpg', likes: 312 },
        { image: 'images/instagram/post-3.jpg', likes: 189 },
        { image: 'images/instagram/post-4.jpg', likes: 421 },
        { image: 'images/instagram/post-5.jpg', likes: 367 },
        { image: 'images/instagram/post-6.jpg', likes: 298 },
        { image: 'images/instagram/post-7.jpg', likes: 445 },
        { image: 'images/instagram/post-8.jpg', likes: 256 },
        { image: 'images/instagram/post-9.jpg', likes: 389 }
    ];
    
    let html = '';
    posts.forEach(post => {
        html += `
            <div class="insta-post">
                <img src="${post.image}" alt="AKÉOS Instagram" onerror="this.src='images/placeholder.jpg'">
                <div class="insta-overlay">
                    <i class="fas fa-heart"></i> ${post.likes}
                </div>
            </div>
        `;
    });
    
    instagramGrid.innerHTML = html;
}

loadInstagramFeed();

// ============================================
// PRODUCT MODAL
// ============================================

productCards.forEach(card => {
    card.addEventListener('click', (e) => {
        // Ne pas ouvrir si on clique sur un bouton
        if (e.target.closest('button')) return;
        
        const productName = card.querySelector('.product-name').textContent;
        const productPrice = card.querySelector('.product-price').textContent;
        const productImpact = card.querySelector('.product-impact').textContent;
        const productImage = card.querySelector('.product-image img').src;
        
        // Remplir le modal
        document.getElementById('productModalName').textContent = productName;
        document.getElementById('productModalPrice').textContent = productPrice;
        document.getElementById('productModalImpact').textContent = productImpact;
        document.getElementById('productMainImage').src = productImage;
        document.getElementById('productModalDescription').textContent = 
            'Vêtement recyclé et transformé avec soin. Chaque pièce est unique et contribue à réduire notre impact environnemental.';
        
        openModal('productModal');
    });
});

// ============================================
// REFERRAL CODE
// ============================================

const getReferralCodeBtn = document.getElementById('getReferralCode');

getReferralCodeBtn?.addEventListener('click', () => {
    // Vérifier si l'utilisateur est connecté
    const isLoggedIn = false; // TODO: Vérifier l'authentification réelle
    
    if (!isLoggedIn) {
        openModal('loginModal');
    } else {
        const code = generateReferralCode();
        alert(`Votre code de parrainage: ${code}\nPartagez-le avec vos amis !`);
    }
});

function generateReferralCode() {
    return 'AKEOS' + Math.random().toString(36).substr(2, 6).toUpperCase();
}

// ============================================
// CHECKOUT
// ============================================

const checkoutBtn = document.getElementById('checkoutBtn');

checkoutBtn?.addEventListener('click', () => {
    if (CONFIG.cart.length === 0) {
        alert('Votre panier est vide !');
        return;
    }
    
    // TODO: Implémenter le tunnel de commande complet
    alert('Redirection vers le paiement...\nFonctionnalité en développement.');
});

// ============================================
// SEARCH
// ============================================

const searchBtn = document.getElementById('searchBtn');

searchBtn?.addEventListener('click', () => {
    const searchTerm = prompt('Rechercher un produit:');
    if (searchTerm) {
        console.log('Searching for:', searchTerm);
        // TODO: Implémenter la recherche
    }
});

// ============================================
// ANIMATIONS ON SCROLL
// ============================================

const animateOnScroll = () => {
    const elements = document.querySelectorAll('.benefit-card, .product-card, .workshop-card, .value-card');
    
    elements.forEach(element => {
        const elementTop = element.getBoundingClientRect().top;
        const windowHeight = window.innerHeight;
        
        if (elementTop < windowHeight - 100) {
            element.style.opacity = '1';
            element.style.transform = 'translateY(0)';
        }
    });
};

// Initial animation setup
document.querySelectorAll('.benefit-card, .product-card, .workshop-card, .value-card').forEach(element => {
    element.style.opacity = '0';
    element.style.transform = 'translateY(30px)';
    element.style.transition = 'opacity 0.6s ease, transform 0.6s ease';
});

window.addEventListener('scroll', animateOnScroll);
animateOnScroll(); // Run on page load

// ============================================
// INITIALIZE
// ============================================

console.log('AKÉOS Website initialized ✓');
console.log('Contact:', CONFIG.email);
console.log('WhatsApp:', CONFIG.whatsapp);
console.log('Instagram:', CONFIG.instagram);
