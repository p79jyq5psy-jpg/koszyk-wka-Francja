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
    <script src="https://cdn.jsdelivr.net/npm/@google/generative-ai/dist/index.min.js"></script>
</head>
<body class="bg-slate-50">
    <div id="root"></div>

    <script type="text/babel">
        const { useState, useEffect } = React;

        // --- KOMPONENTY TWOJEJ STRONY ---

        const Header = ({ onNavigate, activeTab }) => (
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
                      onClick={() => onNavigate(tab)}
                      className={`text-[10px] font-black uppercase tracking-widest px-4 py-2 rounded-lg transition-all ${
                        activeTab === tab ? 'bg-blue-900 text-white shadow-md' : 'text-slate-500 hover:text-blue-900'
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
          const news = [
            { id: '1', title: "Draft 2024: Historyczny Dublet", excerpt: "Risacher i Sarr zajmują dwa pierwsze miejsca w drafcie NBA. To rekord dla Europy.", category: 'Draft', imageUrl: 'https://images.unsplash.com/photo-1546519638-68e109498ffc?q=80&w=800' },
            { id: '2', title: "Wembanyama: Defensywny Fenomen", excerpt: "Victor prowadzi w klasyfikacji bloków NBA i redefiniuje obronę.", category: 'NBA', imageUrl: 'https://images.unsplash.com/photo-1519861531473-9200362f88bf?q=80&w=800' }
          ];

          return (
            <div className="py-12 px-4 max-w-7xl mx-auto">
              <h2 className="text-4xl font-black text-slate-800 uppercase italic mb-10 tracking-tighter">Najnowsze <span className="text-blue-900">Raporty</span></h2>
              <div className="grid grid-cols-1 md:grid-cols-2 gap-10">
                {news.map(item => (
                  <div key={item.id} className="group bg-white rounded-3xl overflow-hidden shadow-sm hover:shadow-xl transition-all border border-slate-100">
                    <img src={item.imageUrl} className="w-full h-64 object-cover" />
                    <div className="p-8">
                      <span className="text-blue-900 font-black text-xs uppercase mb-4 block">{item.category}</span>
                      <h3 className="text-2xl font-black mb-4 group-hover:text-blue-900">{item.title}</h3>
                      <p className="text-slate-500 text-sm mb-6">{item.excerpt}</p>
                      <button className="text-red-600 font-black uppercase text-xs">Czytaj raport →</button>
                    </div>
                  </div>
                ))}
              </div>
            </div>
          );
        };

        const PlayerSpotlight = () => (
          <div className="bg-blue-900 py-20 text-white overflow-hidden relative">
             <div className="max-w-7xl mx-auto px-4 flex flex-col md:flex-row items-center gap-12">
                <div className="z-10 flex-1">
                   <span className="text-red-600 font-black uppercase tracking-[0.3em]">Player Spotlight</span>
                   <h2 className="text-6xl font-black italic uppercase leading-none mt-4 tracking-tighter">Victor <br/>Wembanyama</h2>
                   <p className="mt-6 text-blue-100 max-w-md">Największy talent w historii francuskiej koszykówki. Dominacja po obu stronach parkietu.</p>
                   <div className="grid grid-cols-3 gap-4 mt-10">
                      <div><p className="text-3xl font-black">21.4</p><p className="text-[10px] uppercase opacity-60">Punkty</p></div>
                      <div><p className="text-3xl font-black">10.6</p><p className="text-[10px] uppercase opacity-60">Zbiórki</p></div>
                      <div><p className="text-3xl font-black">3.6</p><p className="text-[10px] uppercase opacity-60">Bloki</p></div>
                   </div>
                </div>
                <div className="flex-1 relative">
                   <div className="w-80 h-80 bg-red-600 rounded-full absolute top-1/2 left-1/2 -translate-x-1/2 -translate-y-1/2 blur-[100px] opacity-30"></div>
                   <img src="https://images.unsplash.com/photo-1546519638-68e109498ffc?q=80&w=800" className="relative z-10 rounded-3xl shadow-2xl rotate-3" />
                </div>
             </div>
          </div>
        );

        const App = () => {
          const [activeTab, setActiveTab] = useState('news');
          
          return (
            <div className="min-h-screen">
              <Header onNavigate={setActiveTab} activeTab={activeTab} />
              
              {activeTab === 'news' && (
                <>
                  <section className="bg-white py-24 text-center px-4">
                    <h1 className="text-8xl font-black text-blue-900 italic uppercase leading-[0.85] tracking-tighter">Le Basket <br/><span className="text-red-600">C'est Nous.</span></h1>
                    <p className="mt-8 text-slate-500 font-bold uppercase tracking-widest text-sm">Oficjalny portal francuskiej koszykówki</p>
                  </section>
                  <NewsSection />
                  <PlayerSpotlight />
                </>
              )}

              {activeTab !== 'news' && (
                <div className="max-w-7xl mx-auto py-20 px-4 text-center">
                  <h2 className="text-4xl font-black uppercase text-blue-900">Sekcja {activeTab}</h2>
                  <p className="mt-4 text-slate-500 text-lg">Właśnie tutaj pojawią się Twoje dane z LNB i sklepiku.</p>
                  <div className="mt-10 p-20 border-4 border-dashed border-slate-200 rounded-[3rem] text-slate-300 font-black text-2xl uppercase italic">
                    Dane są aktualizowane...
                  </div>
                </div>
              )}

              <footer className="bg-blue-950 text-white py-12 px-4 mt-20">
                <div className="max-w-7xl mx-auto flex justify-between items-center">
                  <span className="font-black italic uppercase">BasketFrance Hub © 2026</span>
                  <div className="flex gap-6 opacity-60 text-xs font-bold uppercase tracking-widest">
                    <span>LNB</span> <span>FFBB</span> <span>NBA</span>
                  </div>
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
