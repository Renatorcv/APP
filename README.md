// Prompt de Desenvolvimento - Check List Pro (React + Firebase + Mapbox + QR Code)

// Importações principais
import React, { useState, useEffect, useRef } from 'react';
import { initializeApp } from 'firebase/app';
import {
  getFirestore, collection, addDoc, query, where, getDocs, doc, setDoc
} from 'firebase/firestore';
import mapboxgl from 'mapbox-gl';
import QRCode from 'qrcode.react';
import { LucideMenu } from 'lucide-react'; // Exemplo de importação de ícone Lucide
import 'tailwindcss/tailwind.css';

// Config Firebase (exemplo)
const firebaseConfig = {
  apiKey: "SUA_API_KEY",
  authDomain: "SEU_AUTH_DOMAIN",
  projectId: "SEU_PROJECT_ID",
  storageBucket: "SEU_STORAGE_BUCKET",
  messagingSenderId: "SEU_MESSAGING_SENDER_ID",
  appId: "SEU_APP_ID"
};
const app = initializeApp(firebaseConfig);
const db = getFirestore(app);

// Config Mapbox
mapboxgl.accessToken = 'SEU_MAPBOX_ACCESS_TOKEN';

// --- Sidebar.js ---
export function Sidebar({ isOpen, toggle }) {
  return (
    <aside className={`fixed top-0 left-0 h-full bg-[#1A237E] text-white w-64 p-4 transition-transform duration-300 ${isOpen ? 'translate-x-0' : '-translate-x-full'} md:translate-x-0`}>
      <button className="md:hidden mb-4" onClick={toggle}>
        <LucideMenu size={24} />
      </button>
      <nav className="flex flex-col space-y-4">
        <a href="#dashboard" className="hover:text-[#00BFA5]">🏠 Dashboard</a>
        <a href="#checklists" className="hover:text-[#00BFA5]">📋 Checklists</a>
        <a href="#map" className="hover:text-[#00BFA5]">🗺 Mapa</a>
        <a href="#config" className="hover:text-[#00BFA5]">⚙ Config</a>
      </nav>
    </aside>
  );
}

// --- Dashboard.js ---
export function Dashboard() {
  // Exemplo simples de cards
  return (
    <section className="p-6 bg-[#121858] text-white rounded-lg shadow-lg">
      <h1 className="text-2xl font-bold mb-4">Dashboard</h1>
      <div className="grid grid-cols-1 md:grid-cols-3 gap-6">
        <div className="bg-[#00BFA5] rounded-lg p-4 shadow-md">
          <h2 className="text-lg font-semibold">Checklists Recentes</h2>
          <p>12</p>
        </div>
        <div className="bg-[#00BFA5] rounded-lg p-4 shadow-md">
          <h2 className="text-lg font-semibold">Viagens Ativas</h2>
          <p>3</p>
        </div>
        <div className="bg-[#00BFA5] rounded-lg p-4 shadow-md">
          <h2 className="text-lg font-semibold">Alertas</h2>
          <p>1</p>
        </div>
      </div>
    </section>
  );
}

// --- ChecklistForm.js ---
export function ChecklistForm({ onComplete }) {
  const [formData, setFormData] = useState({
    chassi: '',
    itens: {}, // Exemplo: { "chaveIgnição": true, "macaco": false, ... }
    fotos: [],
  });

  // Função para upload de fotos (simplificada)
  const handlePhotoUpload = (e) => {
    const files = Array.from(e.target.files);
    setFormData(prev => ({ ...prev, fotos: [...prev.fotos, ...files] }));
  };

  const handleChange = (e) => {
    const { name, value, type, checked } = e.target;
    if (type === 'checkbox') {
      setFormData(prev => ({
        ...prev,
        itens: { ...prev.itens, [name]: checked }
      }));
    } else {
      setFormData(prev => ({ ...prev, [name]: value }));
    }
  };

  const handleSubmit = async (e) => {
    e.preventDefault();
    // Salvar rascunho no localStorage ou Firebase (simplificado)
    // Aqui chamamos onComplete para avançar no fluxo
    onComplete(formData);
  };

  return (
    <form onSubmit={handleSubmit} className="bg-[#121858] p-6 rounded-lg shadow-lg text-white max-w-xl mx-auto">
      <h2 className="text-xl font-bold mb-4">Checklist do Veículo</h2>
      <label className="block mb-2">
        Número do Chassi:
        <input
          type="text"
          name="chassi"
          value={formData.chassi}
          onChange={handleChange}
          required
          className="w-full p-2 rounded mt-1 text-black"
          placeholder="Digite o número do chassi"
        />
      </label>

      {/* Exemplo de checkbox */}
      <div className="mb-4">
        <label className="inline-flex items-center mr-4">
          <input type="checkbox" name="chaveIgnição" onChange={handleChange} />
          <span className="ml-2">Chave de Ignição</span>
        </label>
        <label className="inline-flex items-center mr-4">
          <input type="checkbox" name="macaco" onChange={handleChange} />
          <span className="ml-2">Macaco</span>
        </label>
        {/* Adicione os demais itens conforme necessário */}
      </div>

      <label className="block mb-4">
        Fotos:
        <input type="file" multiple accept="image/*" onChange={handlePhotoUpload} className="block mt-1" />
      </label>

      <button type="submit" className="bg-[#00BFA5] px-4 py-2 rounded font-semibold hover:bg-[#009e8f] transition">
        Finalizar Checklist e Iniciar Viagem
      </button>
    </form>
  );
}

// --- MapComponent.js ---
export function MapComponent({ destination, onTripEnd }) {
  const mapContainer = useRef(null);
  const map = useRef(null);
  const [lng, setLng] = useState(-57.0763); // Uruguaiana longitude exemplo
  const [lat, setLat] = useState(-29.7546); // Uruguaiana latitude exemplo
  const [zoom, setZoom] = useState(12);
  const [routeCoords, setRouteCoords] = useState([]);
  const [tracking, setTracking] = useState(false);
  const watchId = useRef(null);
  const originMarker = useRef(null);
  const destinationMarker = useRef(null);
  const routeLine = useRef(null);

  // Inicializa o mapa
  useEffect(() => {
    if (map.current) return; // initialize map only once
    map.current = new mapboxgl.Map({
      container: mapContainer.current,
      style: 'mapbox://styles/mapbox/dark-v10',
      center: [lng, lat],
      zoom: zoom
    });
  }, []);

  // Atualiza destino e marcador
  useEffect(() => {
    if (!map.current || !destination) return;

    // Remove marcador antigo
    if (destinationMarker.current) {
      destinationMarker.current.remove();
    }

    // Adiciona marcador destino
    destinationMarker.current = new mapboxgl.Marker({ color: '#00BFA5' })
      .setLngLat([destination.lng, destination.lat])
      .addTo(map.current);
  }, [destination]);

  // Função para iniciar rastreamento
  const startTracking = () => {
    if (!navigator.geolocation) {
      alert('Geolocalização não suportada pelo navegador.');
      return;
    }

    setTracking(true);

    watchId.current = navigator.geolocation.watchPosition(
      (position) => {
        const { longitude, latitude } = position.coords;
        setLng(longitude);
        setLat(latitude);

        // Atualiza marcador origem na primeira posição
        if (!originMarker.current) {
          originMarker.current = new mapboxgl.Marker({ color: '#00BFA5' })
            .setLngLat([longitude, latitude])
            .addTo(map.current);
        }

        // Atualiza rota
        setRouteCoords(prev => [...prev, [longitude, latitude]]);

        // Centraliza mapa
        map.current.flyTo({ center: [longitude, latitude], zoom: 14 });

        // Verifica proximidade do destino (50m)
        if (destination) {
          const distance = getDistanceFromLatLonInMeters(latitude, longitude, destination.lat, destination.lng);
          if (distance <= 50) {
            window.speechSynthesis.speak(new SpeechSynthesisUtterance('Viagem concluída'));
            stopTracking();
            if (onTripEnd) onTripEnd(routeCoords);
          }
        }
      },
      (error) => {
        console.error('Erro ao obter localização:', error);
      },
      { enableHighAccuracy: true, maximumAge: 5000, timeout: 10000 }
    );
  };

  // Função para parar rastreamento
  const stopTracking = () => {
    if (watchId.current) {
      navigator.geolocation.clearWatch(watchId.current);
      watchId.current = null;
    }
    setTracking(false);
  };

  // Desenha rota no mapa
  useEffect(() => {
    if (!map.current || routeCoords.length < 2) return;

    if (routeLine.current) {
      map.current.removeLayer('route');
      map.current.removeSource('route');
    }

    map.current.addSource('route', {
      type: 'geojson',
      data: {
        type: 'Feature',
        properties: {},
        geometry: {
          type: 'LineString',
          coordinates: routeCoords
        }
      }
    });

    map.current.addLayer({
      id: 'route',
      type: 'line',
      source: 'route',
      layout: { 'line-join': 'round', 'line-cap': 'round' },
      paint: { 'line-color': '#00BFA5', 'line-width': 5 }
    });

    routeLine.current = true;
  }, [routeCoords]);

  // Cálculo de distância entre dois pontos em metros
  function getDistanceFromLatLonInMeters(lat1, lon1, lat2, lon2) {
    const R = 6371000; // Raio da Terra em metros
    const dLat = deg2rad(lat2 - lat1);
    const dLon = deg2rad(lon2 - lon1);
    const a =
      Math.sin(dLat / 2) * Math.sin(dLat / 2) +
      Math.cos(deg2rad(lat1)) * Math.cos(deg2rad(lat2)) *
      Math.sin(dLon / 2) * Math.sin(dLon / 2);
    const c = 2 * Math.atan2(Math.sqrt(a), Math.sqrt(1 - a));
    return R * c;
  }
  function deg2rad(deg) {
    return deg * (Math.PI / 180);
  }

  return (
    <div className="relative h-[400px] rounded-lg shadow-lg overflow-hidden">
      <div ref={mapContainer} className="w-full h-full" />
      <button
        onClick={tracking ? stopTracking : startTracking}
        className="absolute bottom-4 left-4 bg-[#00BFA5] text-black px-4 py-2 rounded shadow hover:bg-[#009e8f] transition"
      >
        {tracking ? 'Parar Rastreamento' : 'Iniciar Rastreamento'}
      </button>
    </div>
  );
}

// --- QrCodeGenerator.js ---
export function QrCodeGenerator({ data }) {
  return (
    <div className="p-4 bg-[#121858] rounded-lg shadow-lg text-white max-w-sm mx-auto text-center">
      <h2 className="mb-4 font-bold text-lg">QR Code do Checklist</h2>
      <QRCode value={JSON.stringify(data)} size={256} bgColor="#1A237E" fgColor="#00BFA5" />
      <p className="mt-2 text-sm">Escaneie para visualizar os dados do checklist.</p>
    </div>
  );
}

// --- App.js (fluxo principal simplificado) ---
export default function App() {
  const [sidebarOpen, setSidebarOpen] = useState(false);
  const [checklistData, setChecklistData] = useState(null);
  const [destination, setDestination] = useState(null);
  const [tripData, setTripData] = useState(null);
  const [showQr, setShowQr] = useState(false);

  // Simples autocomplete para endereço (exemplo estático)
  const handleDestinationChange = (e) => {
    // Aqui você integraria API de geocoding para obter lat/lng reais
    // Exemplo fixo para Uruguaiana
    setDestination({ lat: -29.7546, lng: -57.0763 });
  };

  const handleChecklistComplete = (data) => {
    setChecklistData(data);
    // Após checklist, pedir destino
    alert('Checklist salvo! Agora informe o destino.');
  };

  const handleStartTrip = () => {
    if (!destination) {
      alert('Informe o destino antes de iniciar a viagem.');
      return;
    }
    window.speechSynthesis.speak(new SpeechSynthesisUtterance('Iniciando a viagem'));
  };

  const handleTripEnd = (routeCoords) => {
    // Consolidar dados da viagem
    setTripData({
      checklist: checklistData,
      destination,
      route: routeCoords,
      timestamp: new Date().toISOString()
    });
    setShowQr(true);
  };

  return (
    <div className="flex min-h-screen bg-[#121858] text-white">
      <Sidebar isOpen={sidebarOpen} toggle={() => setSidebarOpen(!sidebarOpen)} />
      <main className="flex-1 p-6 md:ml-64">
        <header className="flex justify-between items-center mb-6">
          <button className="md:hidden" onClick={() => setSidebarOpen(!sidebarOpen)}>
            <LucideMenu size={28} />
          </button>
          <h1 className="text-3xl font-bold">Check List Pro</h1>
        </header>

        {!checklistData && <ChecklistForm onComplete={handleChecklistComplete} />}

        {checklistData && !destination && (
          <div className="max-w-md mx-auto">
            <label className="block mb-2">
              Informe o endereço de destino:
              <input
                type="text"
                onChange={handleDestinationChange}
                placeholder="Digite o endereço"
                className="w-full p-2 rounded mt-1 text-black"
              />
            </label>
            <button
              onClick={handleStartTrip}
              className="bg-[#00BFA5] px-4 py-2 rounded font-semibold hover:bg-[#009e8f] transition"
            >
              Finalizar Checklist e Iniciar Viagem
            </button>
          </div>
        )}

        {destination && !showQr && (
          <MapComponent destination={destination} onTripEnd={handleTripEnd} />
        )}

        {showQr && tripData && (
          <QrCodeGenerator data={tripData} />
        )}
      </main>
    </div>
  );
}
