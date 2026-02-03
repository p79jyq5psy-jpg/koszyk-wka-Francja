<!DOCTYPE html>
<html lang="pl">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>BasketFrance Hub - Pełna Wersja</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <script src="https://unpkg.com/react@18/umd/react.production.min.js"></script>
    <script src="https://unpkg.com/react-dom@18/umd/react-dom.production.min.js"></script>
    <script src="https://unpkg.com/@babel/standalone/babel.min.js"></script>
</head>
<body class="bg-slate-50 font-sans">
    <div id="root"></div>

    <script type="text/babel">
        const { useState } = React;

        // --- TWOJE KOMPONENTY (FULL WERSJA) ---
        const Header = ({ onNavigate, activeTab }) => (
          <header className="sticky top-0 z-50 bg-white/95 backdrop-blur-md shadow-sm border-b-4 border-blue-900">
            <div className="max-w-7xl mx-auto px-4 sm:px-6 lg:px-8">
              <div className="flex justify-between h-20 items-center">
                <button onClick={() => onNavigate('news')} className="flex items-center space-x-3 group">
                  <div className="w-10 h-10 bg-blue-900 rounded-full flex items-center justify-center text-white font-bold text-2xl group-hover:bg-red-600 transition-colors">F</div>
                  <span className="text-2xl font-extrabold tracking-tighter text-blue-900 uppercase">Basket<span className="text-red-600">France</span> Hub</span>
                </button>
                <nav className="hidden md:flex items-center space-x-4">
                  {['standings', 'prob', 'lesbleus', 'sklep'].map((tab) => (
                    <button key={tab} onClick={() => onNavigate(tab)}
                      className={`text-[10px] font-black uppercase tracking-widest px-4 py-2 rounded-lg transition-all ${
                        activeTab === tab ? 'bg-blue-900 text-white shadow-md' : 'text-slate-500 hover:text-blue-900'
                      } ${tab === 'sklep' ? 'bg-red-600 text-white hover:bg-red-700' : ''}`}
                    >
                      {tab === 'standings' ? 'Betclic Élite' : tab === 'prob' ? 'Pro B' : tab === 'lesbleus' ? 'Les Bleus' : 'Sklep'}
                    </button>
                  ))}
                </nav>
              </div>
            </div>
          </header>
        );

        const NewsSection = () => (
          <div className="py-12 px-4 max-w-7xl mx-auto">
            <div className="flex justify-between items-end mb-10">
              <div>
                <p className="text-red-600 font-black uppercase text-xs tracking-widest mb-1">Stay updated</p>
                <h2 className="text-4xl font-black text-slate-800 uppercase italic tracking-tighter">Najnowsze <span className="text-blue-900">Raporty</span></h2>
              </div>
            </div>
            <div className="grid grid-cols-1 md:grid-cols-2 gap-10">
              {[
                { id: '1', title: "Draft 2024: Historyczny Dublet", excerpt: "Risacher i Sarr zajmują dwa pierwsze miejsca w drafcie NBA. To absolutny rekord dla europejskiej koszykówki.", category: 'Draft', img: 'https://images.unsplash.com/photo-1546519638-68e109498ffc?q=80&w=800' },
                { id: '2', title: "Wembanyama: Defensywny Fenomen", excerpt: "Victor prowadzi w klasyfikacji bloków NBA i redefiniuje pojęcie obrony obręczy.", category: 'NBA', img: 'https://images.unsplash.com/photo-1519861531473-9200362f88bf?q=80&w=800' }
              ].map(item => (
                <div key={item.id} className="group bg-white rounded-[2rem] overflow-hidden shadow-sm hover:shadow-2xl transition-all duration-500 border border-slate-100">
                  <div className="h-72 overflow-hidden relative">
                    <img src={item.img} className="w-full h-full object-cover group-hover:scale-105 transition-transform duration-700" alt={item.title} />
                    <div className="absolute top-6 left-6 bg-white/95 px-4 py-1.5 rounded-full text-[10px] font-black uppercase tracking-widest text-blue-900 shadow-sm">{item.category}</div>
                  </div>
                  <div className="p-10">
                    <h3 className="text-2xl font-black mb-4 group-hover:text-blue-900 transition-colors leading-tight uppercase italic">{item.title}</h3>
                    <p className="text-slate-500 text-sm mb-8 leading-relaxed">{item.excerpt}</p>
                    <button className="text-xs font-black uppercase tracking-widest text-red-600 hover:text-red-700 flex items-center gap-2">
                      Czytaj raport <span className="text-lg">→</span>
                    </button>
                  </div>
                </div>
              ))}
            </div>
          </div>
        );

        const PlayerSpotlight = () => (
          <section className="bg-blue-900 py-24 text-white overflow-hidden relative">
            <div className="max-w-7xl mx-auto px-4 flex flex-col md:flex-row items-center gap-16 relative z-10">
              <div className="flex-1">
                <span className="text-red-600 font-black uppercase tracking-[0.3em] text-sm">Player Spotlight</span>
                <h2 className="text-8xl font-black italic uppercase leading-[0.85] mt-6 tracking-tighter">Victor <br/>Wembanyama</h2>
                <div className="grid grid-cols-3 gap-8 mt-12 border-t border-white/10 pt-12">
                  <div><p className="text-5xl font-black">3.6</p><p className="text-[10px] uppercase opacity-50 font-bold mt-2">Bloki/Mecz</p></div>
                  <div><p className="text-5xl font-black">21.4</p><p className="text-[10px] uppercase opacity-50 font-bold mt-2">Punkty/Mecz</p></div>
                  <div><p className="text-5xl font-black">10.6</p><p className="text-[10px] uppercase opacity-50 font-bold mt-2">Zbiórki</p></div>
                </div>
              </div>
              <div className="flex-1 relative">
                <div className="w-full h-[500px] bg-red-600 rounded-[3rem] rotate-3 absolute top-0 left-0 opacity-20 blur-2xl"></div>
                <img src="https://images.unsplash.com/photo-1504450758481-7338eba7524a?q=80&w=800" className="rounded-[3rem] shadow-2xl relative z-10 rotate-1 hover:rotate-0 transition-transform duration-500" />
              </div>
            </div>
          </section>
        );

        const App = () => {
          const [activeTab, setActiveTab] = useState('news');
          return (
            <div className="min-h-screen">
              <Header onNavigate={setActiveTab} activeTab={activeTab} />
              <main>
                {activeTab === 'news' && (
                  <>
                    <section className="bg-white py-32 text-center px-4">
                      <h1 className="text-[10rem] font-black text-blue-900 italic uppercase leading-[0.75] tracking-tighter">LE BASKET <br/><span className="text-red-600">C'EST NOUS.</span></h1>
                      <p className="mt-12 text-slate-400 font-bold uppercase tracking-[0.5em] text-sm">Official Hub of French Basketball Excellence</p>
                    </section>
                    <NewsSection />
                    <PlayerSpotlight />
                  </>
                )}
                {activeTab !== 'news' && (
                  <div className="py-40 text-center">
                    <h2 className="text-6xl font-black uppercase text-blue-900 italic tracking-tighter">Sekcja {activeTab}</h2>
                    <div className="mt-10 max-w-2xl mx-auto p-20 border-8 border-blue-900/5 rounded-[4rem] text-slate-300 font-black text-3xl uppercase italic">
                      Aktualizacja bazy danych LNB...
                    </div>
                  </div>
                )}
              </main>
              <footer className="bg-slate-950 text-white py-16 px-4">
                <div className="max-w-7xl mx-auto flex justify-between items-center opacity-50 text-[10px] font-bold uppercase tracking-[0.3em]">
                  <span>BasketFrance Hub &copy; 2026</span>
                  <span>France / USA / Euroleague</span>
                </div>
              </footer>
            </div>
          );
        };

        const root = ReactDOM.createRoot(document.getElementById('root'));
        root.render(<App />);
    </script>
</body>
</html>
