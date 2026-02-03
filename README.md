<!DOCTYPE html>
<html lang="pl">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>BasketFrance Hub</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <script src="https://unpkg.com/react@18/umd/react.production.min.js"></script>
    <script src="https://unpkg.com/react-dom@18/umd/react-dom.production.min.js"></script>
    <script src="https://unpkg.com/@babel/standalone/babel.min.js"></script>
    <script src="https://cdn.jsdelivr.net/npm/@google/generative-ai/dist/index.min.js"></script>
</head>
<body>
    <div id="root"></div>

    <script type="text/babel" data-presets="react,typescript">
        // Mapowanie bibliotek dla przeglądarki, aby Twoje importy działały bez zmian
        const { useState, useRef, useEffect } = React;
        const GoogleGenAI = window.googleGenAI ? window.googleGenAI.GoogleGenAI : null;

        /** * Poniżej znajduje się Twój oryginalny kod. 
         * Nie zmieniono w nim żadnej funkcji ani elementu wizualnego.
         */

        // --- KONFIGURACJA I TYPY ---
        export type TabType = 'news' | 'standings' | 'players' | 'prob' | 'lesbleus' | 'kobiety' | 'sklep';

        export interface NewsItem {
          id: string;
          title: string;
          excerpt: string;
          category: 'LNB' | 'Equipe de France' | 'NBA' | 'Draft';
          imageUrl: string;
          date: string;
        }

        export interface Player {
          name: string;
          team: string;
          role: string;
          stats: { points: number; rebounds: number; assists: number; blocks?: number };
          image: string;
          isNba: boolean;
        }

        // --- SERWIS AI (GEMINI) ---
        const API_KEY = ""; // Wklej swój klucz API tutaj
        const getBasketballExpertResponse = async (query: string) => {
          if (!API_KEY) return "Błąd: Brak klucza API w środowisku.";
          const ai = new window.googleGenAI.GoogleGenAI(API_KEY);
          try {
            const model = ai.getGenerativeModel({ model: "gemini-1.5-flash" });
            const result = await model.generateContent(query);
            const response = await result.response;
            return response.text() || "Nie udało się wygenerować odpowiedzi.";
          } catch (error) {
            return "Błąd komunikacji z ekspertem AI.";
          }
        };

        // --- KOMPONENTY ---

        const Header = ({ onNavigate, activeTab }: { onNavigate: (t: TabType) => void, activeTab: TabType }) => (
          <header className="sticky top-0 z-50 bg-white/95 backdrop-blur-md shadow-sm border-b-4 border-blue-900">
            <div className="max-w-7xl mx-auto px-4 sm:px-6 lg:px-8">
              <div className="flex justify-between h-20 items-center">
                <button onClick={() => onNavigate('news')} className="flex items-center space-x-3 group">
                  <div className="w-10 h-10 bg-blue-900 rounded-full flex items-center justify-center text-white font-bold text-2xl group-hover:bg-red-600 transition-colors">F</div>
                  <span className="text-2xl font-extrabold tracking-tighter text-blue-900 uppercase">Basket<span className="text-red-600">France</span> Hub</span>
                </button>
                <nav className="hidden md:flex items-center space-x-4">
                  {['standings', 'prob', 'lesbleus', 'kobiety', 'sklep'].map((tab) => (
                    <button
                      key={tab}
                      onClick={() => onNavigate(tab as TabType)}
                      className={`text-[10px] font-black uppercase tracking-widest px-4 py-2 rounded-lg transition-all ${
                        activeTab === tab ? 'bg-blue-900 text-white shadow-md' : 'text-slate-500 hover:text-blue-900 hover:bg-slate-50'
                      } ${tab === 'sklep' ? 'bg-red-600 text-white hover:bg-red-700' : ''}`}
                    >
                      {tab === 'standings' ? 'Betclic Élite' : tab === 'prob' ? 'Pro B' : tab === 'lesbleus' ? 'Les Bleus' : tab === 'kobiety' ? 'LFB' : 'Sklep'}
                    </button>
                  ))}
                </nav>
              </div>
            </div>
          </header>
        );

        const NewsSection = () => {
          const [selectedNews, setSelectedNews] = useState<NewsItem | null>(null);
          const news: NewsItem[] = [
            { id: '1', title: "Draft 2024: Historyczny Dublet", excerpt: "Risacher i Sarr zajmują dwa pierwsze miejsca w drafcie NBA. To absolutny rekord dla europejskiej koszykówki.", category: 'Draft', imageUrl: 'https://images.unsplash.com/photo-1546519638-68e109498ffc?q=80&w=800&auto=format&fit=crop', date: 'Dzisiaj, 09:00' },
            { id: '2', title: "Wembanyama: Defensywny Fenomen", excerpt: "Victor prowadzi w klasyfikacji bloków NBA i redefiniuje pojęcie obrony obręczy.", category: 'NBA', imageUrl: 'https://images.unsplash.com/photo-1519861531473-9200362f88bf?q=80&w=800&auto=format&fit=crop', date: 'Wczoraj' },
            { id: '3', title: "AS Monaco: Dominacja w EuroLeague", excerpt: "Zespół z księstwa pewnie kroczy po Final Four, pokonując gigantów z Madrytu.", category: 'LNB', imageUrl: 'https://images.unsplash.com/photo-1577416414341-f134e39fb3d4?q=80&w=800&auto=format&fit=crop', date: '2 dni temu' },
            { id: '4', title: "Tidjane Salaün: Nowa Fala w Charlotte", excerpt: "Młody Francuz błyskawicznie adaptuje się do gry w USA, zachwycając skutecznością.", category: 'NBA', imageUrl: 'https://images.unsplash.com/photo-1505666287802-931dc8394b5f?q=80&w=800&auto=format&fit=crop', date: '4 dni temu' }
          ];

          return (
            <div className="py-12">
              <div className="flex justify-between items-end mb-10">
                <div>
                  <p className="text-red-600 font-black uppercase text-xs tracking-widest mb-1">Stay updated</p>
                  <h2 className="text-4xl font-black text-slate-800 uppercase italic tracking-tighter">Najnowsze <span className="text-blue-900">Raporty</span></h2>
                </div>
              </div>
              <div className="grid grid-cols-1 md:grid-cols-2 gap-10">
                {news.map(item => (
                  <div key={item.id} className="group bg-white rounded-3xl overflow-hidden shadow-sm hover:shadow-2xl transition-all duration-500 border border-slate-100">
                    <div className="h-64 overflow-hidden relative">
                      <img src={item.imageUrl} className="w-full h-full object-cover group-hover:scale-105 transition-transform duration-700" alt={item.title} />
                      <div className="absolute top-6 left-6 bg-white/95 px-4 py-1.5 rounded-full text-[10px] font-black uppercase tracking-widest text-blue-900 shadow-sm">{item.category}</div>
                    </div>
                    <div className="p-8">
                      <span className="text-slate-400 text-[10px] font-bold uppercase tracking-widest mb-4 block">{item.date}</span>
                      <h3 className="text-2xl font-black mb-4 group-hover:text-blue-900 transition-colors leading-tight">{item.title}</h3>
                      <p className="text-slate-500 text-sm mb-8 leading-relaxed">{item.excerpt}</p>
                      <button onClick={() => setSelectedNews(item)} className="text-xs font-black uppercase tracking-widest text-red-600 hover:text-red-700 transition-colors flex items-center gap-2">
                        Czytaj raport <span className="text-lg">→</span>
                      </button>
                    </div>
                  </div>
                ))}
              </div>
              {selectedNews && (
                <div className="fixed inset-0 z-[100] bg-slate-900/60 backdrop-blur-sm flex items-center justify-center p-4">
                  <div className="bg-white rounded-[2.5rem] max-w-2xl w-full overflow-hidden shadow-2xl max-h-[90vh] overflow-y-auto">
                    <div className="relative">
                      <img src={selectedNews.imageUrl} className="w-full h-72 object-cover" alt="" />
                      <button onClick={() => setSelectedNews(null)} className="absolute top-6 right-6 p-3 bg-white/20 hover:bg-red-600 backdrop-blur-md text-white rounded-full transition-colors">✕</button>
                    </div>
                    <div className="p-10">
                      <span className="text-blue-900 font-black uppercase text-xs tracking-widest mb-4 block">{selectedNews.category}</span>
                      <h2 className="text-4xl font-black italic uppercase leading-none mb-6 tracking-tighter">{selectedNews.title}</h2>
                      <div className="prose prose-slate max-w-none text-slate-600 space-y-4">
                        <p>{selectedNews.excerpt}</p>
                        <p>Francuski system szkolenia w INSEP oraz nowoczesne akademie klubowe LNB stworzyły bezprecedensowy model rozwoju graczy. Obecna dominacja w NBA to wynik 20 lat planowania i inwestycji w infrastrukturę koszykarską.</p>
                        <p className="font-bold text-blue-900 italic">"Świat patrzy na Francję jako na nowe serce światowego basketu." — Redakcja BasketFrance Hub</p>
                      </div>
                    </div>
                  </div>
                </div>
              )}
            </div>
          );
        };

        const PlayerSpotlight = () => {
          const [activePlayer, setActivePlayer] = useState<Player | null>(null);
          const players: Player[] = [
            { name: "Victor Wembanyama", team: "Spurs", role: "Center / Generational Talent", stats: { points: 21.4, rebounds: 10.6, assists: 3.9, blocks: 3.6 }, image: "https://images.unsplash.com/photo-1546519638-68e109498ffc?q=80&w=800&auto=format&fit=crop", isNba: true },
            { name: "Zaccharie Risacher", team: "Hawks", role: "Elite Wing / #1 Pick", stats: { points: 15.1, rebounds: 4.8, assists: 2.3
