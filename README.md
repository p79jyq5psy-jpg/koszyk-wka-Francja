<!DOCTYPE html>
<html lang="pl">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>BasketFrance Hub - Full Experience</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <script src="https://unpkg.com/react@18/umd/react.production.min.js"></script>
    <script src="https://unpkg.com/react-dom@18/umd/react-dom.production.min.js"></script>
    <script src="https://unpkg.com/@babel/standalone/babel.min.js"></script>
    <link href="https://fonts.googleapis.com/css2?family=Montserrat:wght@400;700;900&display=swap" rel="stylesheet">
    <style>
        body { font-family: 'Montserrat', sans-serif; }
        .hero-text { line-height: 0.8; letter-spacing: -0.05em; }
        .glass-card { background: rgba(255, 255, 255, 0.9); backdrop-filter: blur(10px); }
    </script>
</head>
<body class="bg-slate-100 text-slate-900">
    <div id="root"></div>

    <script type="text/babel">
        const { useState, useEffect } = React;

        // --- DANE ---
        const PLAYERS = [
            { id: 1, name: "Victor Wembanyama", team: "San Antonio Spurs", stats: "21.4 PPG | 10.6 RPG | 3.6 BPG", desc: "Absolutny fenomen. Pierwszy wybór draftu 2023, który redefiniuje pojęcie obrony i ataku swoim zasięgiem i mobilnością.", role: "Generational Talent" },
            { id: 2, name: "Zaccharie Risacher", team: "Atlanta Hawks", stats: "NBA Draft 2024 #1 Pick", desc: "Niezwykle inteligentny skrzydłowy z elitarnym rzutem z dystansu. Przyszłość Hawks i reprezentacji.", role: "Rising Star" },
            { id: 3, name: "Rudy Gobert", team: "Minnesota Timberwolves", stats: "4x Defensive Player of the Year", desc: "Najlepszy obrońca swojej generacji. Filar 'Les Bleus', który pilnuje pomalowanego jak nikt inny.", role: "The Anchor" },
            { id: 4, name: "Alex Sarr", team: "Washington Wizards", stats: "NBA Draft 2024 #2 Pick", desc: "Nowoczesny podkoszowy, którego mobilność w obronie pozwala na krycie wszystkich pozycji.", role: "The Future" }
        ];

        const NEWS = [
            { title: "Draft 2024: Historyczny Dublet", date: "Czerwiec 2024", excerpt: "Francja staje się potęgą. Risacher i Sarr zajmują dwa pierwsze miejsca w drafcie NBA. To pierwszy taki przypadek w historii dla kraju spoza USA.", cat: "HISTORIA" },
            { title: "Wembanyama: Defensywny Fenomen", date: "Styczeń 2026", excerpt: "Victor prowadzi w klasyfikacji bloków i jest głównym faworytem do nagrody DPOY. Jego wpływ na grę Spurs jest kolosalny.", cat: "NBA" },
            { title: "AS Monaco: Dominacja w Pro A", date: "Luty 2026", excerpt: "Klub z Księstwa kontynuuje swoją passę zwycięstw, budując najsilniejszą kadrę w historii ligi francuskiej.", cat: "PRO A" },
            { title: "Nowa Fala w Charlotte", date: "Styczeń 2026", excerpt: "Francuscy zawodnicy coraz mocniej zaznaczają swoją obecność w Hornets. Tidjane Salaün pokazuje pazur.", cat: "NBA" },
            { title: "LNB to nie tylko NBA", date: "Luty 2026", excerpt: "Liga francuska staje się najlepszym miejscem do rozwoju dla młodych talentów z całego świata.", cat: "ANALIZA" }
        ];

        // --- KOMPONENTY ---

        const Navigation = ({ active, setTab }) => (
            <header className="sticky top-0 z-50 bg-white/80 backdrop-blur-xl border-b-4 border-blue-900 shadow-xl">
                <div className="max-w-7xl mx-auto px-6 h-24 flex justify-between items-center">
                    <div onClick={() => setTab('news')} className="flex items-center space-x-3 cursor-pointer group">
                        <div className="w-12 h-12 bg-blue-900 rounded-2xl flex items-center justify-center text-white font-black text-2xl group-hover:rotate-12 transition-transform">F</div>
                        <span className="text-2xl font-black text-blue-900 uppercase italic">Basket<span className="text-red-600">France</span> Hub</span>
                    </div>
                    <nav className="hidden xl:flex items-center space-x-2">
                        {[
                            {id: 'standings', label: 'Betclic Élite'},
                            {id: 'prob', label: 'Pro B'},
                            {id: 'lesbleus', label: 'Les Bleus'},
                            {id: 'kobiety', label: 'LFB Kobiety'},
                            {id: 'sklep', label: 'Sklep'}
                        ].map(t => (
                            <button key={t.id} onClick={() => setTab(t.id)} className={`px-4 py-2 rounded-xl text-[10px] font-black uppercase tracking-widest transition-all ${active === t.id ? 'bg-blue-900 text-white shadow-lg scale-105' : 'text-slate-500 hover:bg-slate-100'}`}>
                                {t.label}
                            </button>
                        ))}
                    </nav>
                </div>
            </header>
        );

        const App = () => {
            const [tab, setTab] = useState('news');
            const [view, setView] = useState('home'); // 'home' lub 'stars'

            return (
                <div className="min-h-screen">
                    <Navigation active={tab} setTab={setTab} />

                    {tab === 'news' && (
                        <main>
                            {view === 'home' ? (
                                <>
                                    {/* HERO SECTION */}
                                    <section className="bg-white py-32 px-6 overflow-hidden">
                                        <div className="max-w-7xl mx-auto text-center relative">
                                            <div className="absolute -top-20 -left-20 w-64 h-64 bg-blue-100 rounded-full blur-3xl opacity-50"></div>
                                            <h1 className="hero-text text-[12vw] font-black text-blue-900 italic uppercase">
                                                Le Basket <br/><span className="text-red-600">C'est Nous.</span>
                                            </h1>
                                            <p className="mt-12 text-slate-400 font-bold uppercase tracking-[0.6em] text-sm">Official Hub of French Basketball Excellence</p>
                                            <button 
                                                onClick={() => setView('stars')}
                                                className="mt-16 bg-blue-900 text-white px-14 py-7 rounded-[2rem] font-black uppercase tracking-tighter hover:bg-red-600 hover:scale-105 transition-all shadow-[0_20px_50px_rgba(30,58,138,0.3)] text-xl"
                                            >
                                                Śledź gwiazdę NBA →
                                            </button>
                                        </div>
                                    </section>

                                    {/* NEWS GRID */}
                                    <section className="max-w-7xl mx-auto px-6 py-24">
                                        <div className="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-3 gap-8">
                                            {NEWS.map((n, i) => (
                                                <div key={i} className={`bg-white p-10 rounded-[3rem] shadow-sm hover:shadow-2xl transition-all border border-slate-100 group ${i === 0 ? 'md:col-span-2' : ''}`}>
                                                    <span className="text-red-600 font-black text-xs uppercase tracking-widest">{n.cat}</span>
                                                    <h3 className="text-3xl font-black mt-4 mb-6 leading-tight uppercase italic group-hover:text-blue-900">{n.title}</h3>
                                                    <p className="text-slate-500 mb-8 leading-relaxed">{n.excerpt}</p>
                                                    <div className="flex justify-between items-center">
                                                        <span className="text-[10px] font-bold text-slate-300 uppercase">{n.date}</span>
                                                        <button className="text-blue-900 font-black text-xs uppercase">Czytaj więcej</button>
                                                    </div>
                                                </div>
                                            ))}
                                        </div>
                                    </section>
                                </>
                            ) : (
                                /* STAR TRACKER SECTION */
                                <section className="max-w-7xl mx-auto px-6 py-24 animate-in fade-in duration-500">
                                    <button onClick={() => setView('home')} className="mb-12 flex items-center space-x-2 text-blue-900 font-black uppercase hover:translate-x-[-5px] transition-transform">
                                        <span>← Wróć do strony głównej</span>
                                    </button>
                                    <h2 className="text-6xl font-black text-blue-900 uppercase italic mb-16 tracking-tighter">Profil <span className="text-red-600 text-7xl">Gwiaździsty</span></h2>
                                    <div className="grid grid-cols-1 md:grid-cols-2 gap-10">
                                        {PLAYERS.map(p => (
                                            <div key={p.id} className="bg-blue-900 rounded-[4rem] p-12 text-white shadow-2xl relative overflow-hidden group">
                                                <div className="absolute top-0 right-0 w-64 h-64 bg-red-600 rounded-full blur-[120px] opacity-20 group-hover:opacity-40 transition-opacity"></div>
                                                <span className="bg-red-600 px-4 py-1 rounded-full text-[10px] font-black uppercase tracking-widest">{p.role}</span>
                                                <h3 className="text-5xl font-black uppercase italic mt-6 tracking-tighter">{p.name}</h3>
                                                <p className="text-blue-300 font-bold uppercase mt-2">{p.team}</p>
                                                <div className="my-10 h-[1px] bg-white/10"></div>
                                                <p className="text-3xl font-black italic text-red-500 mb-6">{p.stats}</p>
                                                <p className="text-blue-100/70 leading-relaxed text-lg">{p.desc}</p>
                                            </div>
                                        ))}
                                    </div>
                                </section>
                            )}
                        </main>
                    )}

                    {tab === 'sklep' && (
                        <main className="max-w-7xl mx-auto px-6 py-32 text-center">
                            <h2 className="text-7xl font-black text-blue-900 uppercase italic">Oficjalny <span className="text-red-600 tracking-tighter">Sklep</span></h2>
                            <p className="mt-6 text-slate-500 font-bold uppercase tracking-widest">Najlepszy sprzęt prosto z Francji</p>
                            <div className="mt-20 grid grid-cols-1 md:grid-cols-3 gap-10 text-left">
                                {[
                                    { name: "Koszulka Reprezentacji 24/25", price: "399 PLN", color: "Blue/Red" },
                                    { name: "Wemby San Antonio Edition", price: "449 PLN", color: "Black/Silver" },
                                    { name: "Piłka Spalding LNB Official", price: "249 PLN", color: "Orange" }
                                ].map((item, i) => (
                                    <div key={i} className="bg-white p-8 rounded-[3rem] shadow-lg border-b-8 border-blue-900">
                                        <div className="h-64 bg-slate-100 rounded-[2rem] mb-6 flex items-center justify-center text-slate-300 font-black">PODGLĄD</div>
                                        <h4 className="font-black uppercase italic text-xl">{item.name}</h4>
                                        <p className="text-slate-400 text-sm font-bold uppercase">{item.color}</p>
                                        <p className="mt-6 text-2xl font-black text-red-600">{item.price}</p>
                                        <button className="mt-6 w-full bg-blue-900 text-white py-4 rounded-2xl font-black uppercase hover:bg-red-600 transition-colors">Dodaj do koszyka</button>
                                    </div>
                                ))}
                            </div>
                        </main>
                    )}

                    {['standings', 'prob', 'lesbleus', 'kobiety'].includes(tab) && (
                        <main className="max-w-7xl mx-auto px-6 py-40 text-center">
                            <h2 className="text-5xl font-black text-blue-900 uppercase italic">Sekcja {tab}</h2>
                            <div className="mt-12 bg-white p-20 rounded-[4rem] shadow-inner border-4 border-dashed border-slate-200">
                                <p className="text-2xl font-black text-slate-300 uppercase italic">Ładowanie oficjalnych tabel LNB / FFBB...</p>
                            </div>
                        </main>
                    )}

                    <footer className="bg-slate-900 text-white py-20 px-6 mt-20">
                        <div className="max-w-7xl mx-auto flex flex-col md:flex-row justify-between items-center border-t border-white/10 pt-10">
                            <div className="text-xl font-black uppercase italic mb-8 md:mb-0">BasketFrance Hub <span className="text-red-600">© 2026</span></div>
                            <div className="flex space-x-10 opacity-40 text-[10px] font-black uppercase tracking-[0.4em]">
                                <span>LNB</span>
                                <span>FFBB</span>
                                <span>Euroleague</span>
                                <span>NBA France</span>
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
