# fala
if
// RealEstatePrototype.jsx
// Single-file React component (default export) using Tailwind CSS
// Purpose: interactive prototype for an online real-estate marketplace (buy/sell/rent)
// Features:
// - Multi-language (ar, fr, en) toggles
// - Listings with type (Buy/Sell/Rent), location, price, area
// - Map placeholder using Google Maps iframe (replace with API map later)
// - Search + simple filters (city, type, price range)
// - Contact buttons (call, email) and "Request Viewing" modal
// - Responsive design, simple/classic color palette

import React, { useState } from 'react';

// --- Mock data ---
const LISTINGS = [
  {
    id: 1,
    title: { en: 'Nice 2BR Apartment', fr: 'Appartement 2CH Agréable', ar: 'شقة 2 غرف مريحة' },
    type: 'rent',
    city: 'Annaba',
    price: 35000,
    area: 85,
    phone: '+213600000001',
    lat: 36.897, lon: 7.766,
    desc: { en: 'Near the sea, bright and ventilated.', fr: "Près de la mer, lumineux.", ar: 'قريب من البحر، مشرقة و جيدة التهوية.' }
  },
  {
    id: 2,
    title: { en: 'Modern 3BR for Sale', fr: '3CH Moderne à Vendre', ar: 'منزل 3 غرف عصري للبيع' },
    type: 'sale',
    city: 'Constantine',
    price: 120000,
    area: 120,
    phone: '+213600000002',
    lat: 36.365, lon: 6.614,
    desc: { en: 'Spacious, good neighborhood.', fr: 'Spacieux, bon quartier.', ar: 'فسيح، في حي جيد.' }
  },
  {
    id: 3,
    title: { en: 'Land 500 m²', fr: "Terrain 500 m²", ar: 'أرض 500 م²' },
    type: 'buy',
    city: 'Annaba',
    price: 80000,
    area: 500,
    phone: '+213600000003',
    lat: 36.9, lon: 7.75,
    desc: { en: 'Close to main road.', fr: 'Proche de la route principale.', ar: 'قريبة من الطريق الرئيسية.' }
  }
];

const TRANSLATIONS = {
  en: {
    searchPlaceholder: 'Search by city, price, or area',
    typeAll: 'All',
    typeRent: 'Rent',
    typeSale: 'Sale',
    typeBuy: 'Buy',
    addListing: 'Add Listing',
    call: 'Call',
    email: 'Email',
    requestViewing: 'Request Viewing',
    price: 'Price',
    area: 'Area',
    location: 'Location'
  },
  fr: {
    searchPlaceholder: 'Rechercher par ville, prix ou surface',
    typeAll: 'Tous',
    typeRent: 'Location',
    typeSale: 'Vente',
    typeBuy: 'Achat',
    addListing: 'Ajouter une annonce',
    call: 'Appeler',
    email: 'Email',
    requestViewing: 'Demander visite',
    price: 'Prix',
    area: 'Surface',
    location: 'Localisation'
  },
  ar: {
    searchPlaceholder: 'ابحث بالمدينة، السعر أو المساحة',
    typeAll: 'الكل',
    typeRent: 'كراء',
    typeSale: 'بيع',
    typeBuy: 'شراء',
    addListing: 'أضف إعلان',
    call: 'اتصال',
    email: 'بريد',
    requestViewing: 'طلب مشاهدة',
    price: 'السعر',
    area: 'المساحة',
    location: 'الموقع'
  }
};

export default function RealEstatePrototype() {
  const [lang, setLang] = useState('ar');
  const t = TRANSLATIONS[lang];
  const [filterType, setFilterType] = useState('all');
  const [query, setQuery] = useState('');
  const [minPrice, setMinPrice] = useState('');
  const [maxPrice, setMaxPrice] = useState('');
  const [selectedListing, setSelectedListing] = useState(null);
  const [showModal, setShowModal] = useState(false);

  const filtered = LISTINGS.filter(l => {
    if (filterType !== 'all' && l.type !== filterType) return false;
    if (minPrice && l.price < Number(minPrice)) return false;
    if (maxPrice && l.price > Number(maxPrice)) return false;
    const q = query.trim().toLowerCase();
    if (q) {
      const inCity = l.city.toLowerCase().includes(q);
      const inTitle = l.title[lang].toLowerCase().includes(q);
      return inCity || inTitle;
    }
    return true;
  });

  function openContact(listing) {
    setSelectedListing(listing);
    setShowModal(true);
  }

  return (
    <div className="min-h-screen bg-gray-50 text-gray-800">
      <header className="bg-white shadow p-4">
        <div className="container mx-auto flex items-center justify-between">
          <div className="flex items-center gap-3">
            <div className="w-10 h-10 rounded-full bg-blue-800 flex items-center justify-center text-white font-bold">عق</div>
            <div>
              <h1 className="font-semibold text-lg">منصة العقار</h1>
              <p className="text-sm text-gray-500">وسيط بين المشتري والبائع</p>
            </div>
          </div>

          <div className="flex items-center gap-3">
            <div className="flex border rounded overflow-hidden">
              <button onClick={() => setLang('ar')} className={`px-3 py-1 ${lang==='ar' ? 'bg-blue-800 text-white' : 'bg-white'}`}>AR</button>
              <button onClick={() => setLang('fr')} className={`px-3 py-1 ${lang==='fr' ? 'bg-blue-800 text-white' : 'bg-white'}`}>FR</button>
              <button onClick={() => setLang('en')} className={`px-3 py-1 ${lang==='en' ? 'bg-blue-800 text-white' : 'bg-white'}`}>EN</button>
            </div>
            <a href="#add" className="px-3 py-2 bg-gray-900 text-white rounded">{t.addListing}</a>
          </div>
        </div>
      </header>

      <main className="container mx-auto p-4 grid grid-cols-1 lg:grid-cols-3 gap-6">
        {/* Search + Filters */}
        <aside className="col-span-1 bg-white p-4 rounded shadow">
          <input value={query} onChange={e=>setQuery(e.target.value)} placeholder={t.searchPlaceholder} className="w-full border p-2 rounded mb-3" />
          <div className="flex gap-2 mb-3">
            <select value={filterType} onChange={e=>setFilterType(e.target.value)} className="flex-1 border p-2 rounded">
              <option value="all">{t.typeAll}</option>
              <option value="rent">{t.typeRent}</option>
              <option value="sale">{t.typeSale}</option>
              <option value="buy">{t.typeBuy}</option>
            </select>
          </div>

          <div className="mb-3">
            <label className="text-sm">{t.price}</label>
            <div className="flex gap-2 mt-1">
              <input value={minPrice} onChange={e=>setMinPrice(e.target.value)} placeholder="Min" className="w-1/2 border p-2 rounded" />
              <input value={maxPrice} onChange={e=>setMaxPrice(e.target.value)} placeholder="Max" className="w-1/2 border p-2 rounded" />
            </div>
          </div>

          <div className="mb-3">
            <button onClick={()=>{setQuery(''); setFilterType('all'); setMinPrice(''); setMaxPrice('')}} className="w-full border p-2 rounded">Reset</button>
          </div>

          <div className="text-sm text-gray-600">{filtered.length} {lang==='ar' ? 'نتائج' : (lang==='fr' ? 'résultats' : 'results')}</div>
        </aside>

        {/* Listings */}
        <section className="col-span-2">
          <div className="grid gap-4">
            {filtered.map(listing => (
              <div key={listing.id} className="bg-white rounded shadow p-4 flex flex-col lg:flex-row gap-4">
                <div className="w-full lg:w-1/3 bg-gray-100 rounded p-2 flex items-center justify-center">
                  {/* Image placeholder */}
                  <div className="text-center text-gray-400">Photo</div>
                </div>
                <div className="flex-1">
                  <h2 className="font-semibold text-lg">{listing.title[lang]}</h2>
                  <p className="text-sm text-gray-600">{listing.desc[lang]}</p>
                  <div className="flex gap-4 mt-3 text-sm text-gray-700">
                    <div><strong>{t.price}:</strong> {listing.price} DA</div>
                    <div><strong>{t.area}:</strong> {listing.area} m²</div>
                    <div><strong>{t.location}:</strong> {listing.city}</div>
                  </div>

                  <div className="mt-3 flex gap-2">
                    <a href={`tel:${listing.phone}`} className="px-3 py-2 border rounded">{t.call}</a>
                    <a href={`mailto:owner@example.com?subject=Inquiry about ${encodeURIComponent(listing.title[lang])}`} className="px-3 py-2 border rounded">{t.email}</a>
                    <button onClick={()=>openContact(listing)} className="px-3 py-2 bg-blue-800 text-white rounded">{t.requestViewing}</button>
                    <a target="_blank" rel="noreferrer" href={`https://www.google.com/maps/search/?api=1&query=${listing.lat},${listing.lon}`} className="px-3 py-2 border rounded">Map</a>
                  </div>
                </div>
              </div>
            ))}

            {/* Map */}
            <div className="bg-white rounded shadow p-4">
              <h3 className="font-semibold mb-2">Map</h3>
              {/* Simple iframe map - replace with Google Maps JS API for advanced features */}
              <div className="w-full h-64">
                <iframe title="map" className="w-full h-full rounded" src={`https://www.google.com/maps/d/u/0/embed?mid=1`} />
              </div>
            </div>
          </div>
        </section>
      </main>

      {/* Contact modal */}
      {showModal && selectedListing && (
        <div className="fixed inset-0 bg-black/40 flex items-center justify-center p-4">
          <div className="bg-white rounded shadow p-4 w-full max-w-md">
            <h4 className="font-semibold mb-2">{selectedListing.title[lang]}</h4>
            <p className="text-sm text-gray-600 mb-3">{selectedListing.desc[lang]}</p>
            <div className="mb-3">
              <label className="block text-sm">Your name</label>
              <input className="w-full border p-2 rounded" />
            </div>
            <div className="mb-3">
              <label className="block text-sm">Phone or Email</label>
              <input className="w-full border p-2 rounded" />
            </div>
            <div className="flex gap-2 justify-end">
              <button onClick={()=>setShowModal(false)} className="px-3 py-2 border rounded">Close</button>
              <button onClick={()=>{ alert('Request sent (prototype)'); setShowModal(false); }} className="px-3 py-2 bg-blue-800 text-white rounded">Send</button>
            </div>
          </div>
        </div>
      )}

      <footer className="bg-white mt-8 p-4 text-center text-sm text-gray-600">
        © {new Date().getFullYear()} منصة العقار — Prototype
      </footer>
    </div>
  );
}

/*
README (quick):

1) How to run (locally):
- Create a React app (Vite or CRA), enable Tailwind.
- Put this file as src/App.jsx and import it in src/main.jsx.
- Start dev server: npm run dev

2) Deploy online quickly:
- Push to GitHub and deploy on Vercel or Netlify (they detect React apps automatically).

3) Next steps to make production-ready:
- Replace mock data with a backend (API + database) or WordPress + REST API.
- Add authentication for users and owners.
- Replace iframe map with Google Maps JS API for clustering and searching by radius.
- Add image upload (S3 or Cloudinary).
- Add moderation panel for listings.
- Integrate multi-language via i18n library (react-i18next) and translate all UI strings.
- Add payment gateway if you plan to charge for premium listings.

If you want, I can:
- Deploy this prototype for you on a free host (Vercel) and connect a temporary domain.
- Or build a WordPress-based solution with a premium real-estate theme and full admin panel.
*/
