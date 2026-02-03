<!DOCTYPE html>
<html lang="pl">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Basket France Hub - Elite Basketball Coverage</title>
    <script src="https://cdn.tailwindcss.com?plugins=typography"></script>
    <link href="https://fonts.googleapis.com/css2?family=Inter:wght@400;700;900&display=swap" rel="stylesheet">
    <style>
        body { 
            font-family: 'Inter', sans-serif; 
            scroll-behavior: smooth;
        }
        ::-webkit-scrollbar { width: 8px; }
        ::-webkit-scrollbar-track { background: #f8fafc; }
        ::-webkit-scrollbar-thumb { 
            background: #002395; 
            border-radius: 10px;
            border: 2px solid #f8fafc;
        }
        .animate-in { animation: fadeIn 0.6s cubic-bezier(0.16, 1, 0.3, 1) forwards; }
        @keyframes fadeIn { from { opacity: 0; transform: translateY(20px); } to { opacity: 1; transform: translateY(0); } }
        
        .france-flag {
            background: linear-gradient(90deg, #002395 0%, #002395 33%, #ffffff 33%, #ffffff 66%, #ed2939 66%, #ed2939 100%);
        }
        
        .hero-gradient {
            background: linear-gradient(to bottom, rgba(0, 35, 149, 0.6) 0%, transparent 100%);
        }

        .no-scrollbar::-webkit-scrollbar { display: none; }
        .no-scrollbar { -ms-overflow-style: none; scrollbar-width: none; }
    </style>
    <script type="importmap">
    {
      "imports": {
        "react": "https://esm.sh/react@^19.2.4",
        "react-dom/": "https://esm.sh/react-dom@^19.2.4/",
        "react/": "https://esm.sh/react@^19.2.4/",
        "@google/genai": "https://esm.sh/@google/genai@^1.39.0"
      }
    }
    </script>
</head>
<body class="bg-slate-50 text-slate-900 overflow-x-hidden">
    <div id="root"></div>

    <script type="module">
        import React, { useState, useRef, useEffect } from 'react';
        import ReactDOM from 'react-dom/client';
        import { GoogleGenAI } from "@google/genai";

        // --- AI SERVICE ---
        const getAIExpertResponse = async (query) => {
            const API_KEY = window.process?.env?.API_KEY || "";
            if (!API_KEY) return "Błąd: Brak klucza API.";
            
            const ai = new GoogleGenAI({ apiKey: API_KEY });
            try {
                const response = await ai.models.generateContent({
                    model: 'gemini-3-flash-preview',
                    contents: query,
                    config: {
                        systemInstruction: "Jesteś ekspertem francuskiej koszykówki. Analizujesz NBA (Wembanyama, Risacher), LNB (Monaco, Paris) i kadrę Francji. Odpowiadaj fachowo po polsku.",
                        temperature: 0.8,
                    },
                });
                return response.text;
            } catch (error) {
                return "Błąd komunikacji z AI.";
            }
        };

        // --- DATA ---
        const NEWS_DATA = [
            { id: 1, title: "Historyczny Draft: Francja na szczycie NBA", excerpt: "Risacher i Sarr przechodzą do historii jako #1 i #2 draftu NBA 2024.", cat: 'NBA', img: 'https://images.unsplash.com/photo-1546519638-68e109498ffc?q=80&w=1200' },
            { id: 2, title: "Wembanyama: Nowy standard obrony", excerpt: "Victor bije rekordy bloków i redefiniuje grę w San Antonio.", cat: 'NBA', img: 'https://images.unsplash.com/photo-1519861531473-9200362f88bf?q=80&w=1200' },
            { id: 3, title: "Monaco vs Paris: Bitwa o Francję", excerpt: "Zapowiedź wielkiego hitu Betclic Élite w nowej Adidas Arenie.", cat: 'LNB', img: 'https://images.unsplash.com/photo-1504450758481-7338eba7524a?q=80&w=1200' },
            { id: 4, title: "Coulibaly: Diament w Waszyngtonie", excerpt: "Bilal Coulibaly staje się kluczowym elementem defensywy Wizards.", cat: 'NBA', img: 'https://images.unsplash.com/photo-1511447333015-45b65e60f6d5?q=80&w=1200' }
        ];

        const PLAYERS_DATA = [
            { name: "Victor Wembanyama", team: "Spurs", pts: 21.4, blk: 3.6, img: "https://images.unsplash.com/photo-1546519638-68e109498ffc?q=80&w=800" },
            { name: "Zaccharie Risacher", team: "Hawks", pts: 15.1, blk: 0.9, img: "https://images.unsplash.com/photo-1574629810360-7efbbe195018?q=80&w=800" },
            { name: "Rudy Gobert", team: "Wolves", pts: 14.0, blk: 2.1, img: "https://images.unsplash.com/photo-1505666287802-931dc8394b5f?q=80&w=800" },
            { name: "Alex Sarr", team: "Wizards", pts: 12.8, blk: 2.4, img: "https://images.unsplash.com/photo-1511447333015-45b65e60f6d5?q=80&w=800" }
        ];

        // --- COMPONENTS ---

        const Nav = ({ active, setTab }) => (
            <header className="sticky top-0 z-50 bg-white/95 backdrop-blur-md shadow-sm border-b-4 border-blue-900">
                <div className="max-w-7xl mx-auto px-4 h-20 flex justify-between items-center">
                    <button onClick={() => setTab('news')} className="flex items-center space-x-3">
                        <div className="w-10 h-10 bg-blue-900 rounded-full flex items-center justify-center text-white font-bold text-2xl">F</div>
                        <span className="text-2xl font-black tracking-tighter text-blue-900 uppercase">Basket<span className="text-red-600">France</span> Hub</span>
                    </button>
                    <div className="hidden md:flex space-x-2">
                        {['news', 'players', 'standings', 'sklep'].map(t => (
                            <button key={t} onClick={() => setTab(t)} className={`px-4 py-2 rounded-lg text-[10px] font-black uppercase tracking-widest transition-all ${active === t ? 'bg-blue-900 text-white' : 'text-slate-500 hover:bg-slate-50'}`}>
                                {t === 'news' ? 'Start' : t === 'players' ? 'Gwiazdy' : t === 'standings' ? 'Tabela' : 'Sklep'}
                            </button>
                        ))}
                    </div>
                </div>
            </header>
        );

        const Chat = () => {
            const [open, setOpen] = useState(false);
            const [input, setInput] = useState('');
            const [msgs, setMsgs] = useState([{ role: 'ai', text: 'Witaj! Jestem ekspertem od francuskiego basketu. O co chcesz zapytać?' }]);
            const [loading, setLoading] = useState(false);
            const ref = useRef(null);

            useEffect(() => { if (ref.current) ref.current.scrollTop = ref.current.scrollHeight; }, [msgs, open]);

            const send = async () => {
                if (!input.trim()) return;
                const m = input; setInput('');
                setMsgs(prev => [...prev, { role: 'user', text: m }]);
                setLoading(true);
                const res = await getAIExpertResponse(m);
                setMsgs(prev => [...prev, { role: 'ai', text: res }]);
                setLoading(false);
            };

            return (
                <div className="fixed bottom-6 right-6 z-[100]">
                    {open ? (
                        <div className="bg-white w-80 md:w-96 h-[500px] shadow-2xl rounded-3xl flex flex-col border border-slate-200 overflow-hidden animate-in">
                            <div className="bg-blue-900 p-4 text-white flex justify-between items-center"><span className="font-black text-xs uppercase tracking-widest">Ekspert AI</span><button onClick={() => setOpen(false)}>✕</button></div>
                            <div ref={ref} className="flex-grow p-4 overflow-y-auto space-y-4 bg-slate-50">
                                {msgs.map((m, i) => (<div key={i} className={`flex ${m.role === 'user' ? 'justify-end' : 'justify-start'}`}><div className={`p-3 rounded-2xl text-sm ${m.role === 'user' ? 'bg-red-600 text-white' : 'bg-white shadow-sm'}`}>{m.text}</div></div>))}
                                {loading && <div className="text-[10px] animate-pulse italic text-slate-400">Ekspert analizuje parkiet...</div>}
                            </div>
                            <div className="p-4 bg-white border-t flex gap-2"><input type="text" value={input} onChange={e => setInput(e.target.value)} onKeyDown={e => e.key === 'Enter' && send()} placeholder="Zapytaj o LNB, NBA..." className="flex-grow bg-slate-100 rounded-full px-4 py-2 text-sm outline-none" /><button onClick={send} className="bg-blue-900 text-white p-2 rounded-full px-4">➤</button></div>
                        </div>
                    ) : (
                        <button onClick={() => setOpen(true)} className="bg-blue-900 text-white p-5 rounded-full shadow-2xl hover:scale-110 transition-transform font-black text-xs uppercase border-4 border-white">Ekspert AI</button>
                    )}
                </div>
            );
        };

        const App = () => {
            const [tab, setTab] = useState('news');
            const [selectedNews, setSelectedNews] = useState(null);
            const [selectedPlayer, setSelectedPlayer] = useState(null);

            const render = () => {
                if (tab === 'news') return (
                    <div className="space-y-16 animate-in">
                        <section className="relative h-[60vh] rounded-[3rem] overflow-hidden flex items-center p-12 mt-8 shadow-2xl">
                            <img src="https://images.unsplash.com/photo-1504450758481-7338eba7524a?q=80&w=1600" className="absolute inset-0 w-full h-full object-cover brightness-[0.3]" />
                            <div className="hero-gradient absolute inset-0"></div>
                            <div className="relative z-10 max-w-2xl">
                                <span className="bg-red-600 text-white px-3 py-1 rounded-full text-[10px] font-black uppercase mb-6 inline-block">Live NBA News</span>
                                <h1 className="text-6xl md:text-8xl font-black text-white italic uppercase leading-none mb-8 tracking-tighter">Le Basket<br/>C'est <span className="text-red-600 underline">Nous.</span></h1>
                                <button onClick={() => setTab('players')} className="bg-white text-blue-900 px-10 py-4 rounded-2xl font-black uppercase text-xs tracking-widest hover:bg-slate-100 transition-all">Odkryj Gwiazdy NBA</button>
                            </div>
                        </section>
                        <div className="grid grid-cols-1 md:grid-cols-2 gap-10">
                            {NEWS_DATA.map(n => (
                                <div key={n.id} className="group bg-white rounded-[2.5rem] overflow-hidden shadow-sm hover:shadow-2xl transition-all border border-slate-100">
                                    <div className="h-64 overflow-hidden relative"><img src={n.img} className="w-full h-full object-cover group-hover:scale-105 transition-transform duration-700" /><div className="absolute top-6 left-6 bg-white/90 px-3 py-1 rounded-full text-[10px] font-black uppercase text-blue-900">{n.cat}</div></div>
                                    <div className="p-8"><h3 className="text-2xl font-black mb-4 uppercase italic tracking-tighter leading-tight group-hover:text-blue-900 transition-colors">{n.title}</h3><p className="text-slate-500 text-sm mb-8 leading-relaxed">{n.excerpt}</p><button onClick={() => setSelectedNews(n)} className="text-xs font-black uppercase text-red-600 hover:underline">Czytaj Raport →</button></div>
                                </div>
                            ))}
                        </div>
                    </div>
                );
                if (tab === 'players') return (
                    <div className="py-12 animate-in">
                        <h2 className="text-6xl font-black text-center mb-16 uppercase italic tracking-tighter leading-none">Królowie <span className="text-blue-900 underline decoration-red-600">NBA.</span></h2>
                        <div className="grid grid-cols-1 lg:grid-cols-2 gap-10">
                            {PLAYERS_DATA.map(p => (
                                <div key={p.name} className="bg-white rounded-[3rem] overflow-hidden shadow-xl flex border border-slate-100 group">
                                    <div className="w-2/5 overflow-hidden relative"><img src={p.img} className="absolute inset-0 w-full h-full object-cover group-hover:scale-110 transition-transform duration-700" /></div>
                                    <div className="w-3/5 p-10 flex flex-col justify-between">
                                        <div><h3 className="text-3xl font-black italic uppercase tracking-tighter mb-1">{p.name}</h3><p className="text-slate-400 font-bold uppercase text-[10px] mb-6">{p.team} Elite</p>
                                        <div className="grid grid-cols-2 gap-4"><div className="bg-slate-50 p-4 rounded-2xl text-center font-black"><p className="text-[10px] text-slate-400 uppercase">PTS</p>{p.pts}</div><div className="bg-blue-900 text-white p-4 rounded-2xl text-center font-black"><p className="text-[10px] opacity-50 uppercase">BLK</p>{p.blk}</div></div></div>
                                        <button onClick={() => setSelectedPlayer(p)} className="w-full bg-slate-900 text-white py-4 rounded-2xl font-black uppercase text-xs tracking-widest hover:bg-red-600 transition-colors mt-8 shadow-xl">Analiza Profilu</button>
                                    </div>
                                </div>
                            ))}
                        </div>
                    </div>
                );
                if (tab === 'standings') return (
                    <div className="py-12 max-w-4xl mx-auto animate-in">
                        <h2 className="text-4xl font-black text-center mb-12 uppercase italic tracking-tighter">Tabela <span className="text-red-600">Betclic Élite</span></h2>
                        <div className="bg-white rounded-[2rem] overflow-hidden shadow-sm border"><table className="w-full text-left"><thead className="bg-slate-50 text-[10px] font-black uppercase text-slate-400 tracking-widest"><tr><th className="p-6">Poz</th><th>Zespół</th><th className="text-center">W</th><th className="text-center">P</th></tr></thead><tbody className="divide-y divide-slate-100">{[{r:1, t:'AS Monaco', w:21, l:3}, {r:2, t:'Paris Basketball', w:18, l:6}, {r:3, t:'LDLC ASVEL', w:17, l:7}].map(r => (<tr key={r.r} className="hover:bg-blue-50/50 transition-colors"><td className="p-6 font-bold text-slate-300">#{r.r}</td><td className="font-black text-slate-800">{r.t}</td><td className="text-center text-green-600 font-bold">{r.w}</td><td className="text-center text-red-600 font-bold">{r.l}</td></tr>))}</tbody></table></div>
                    </div>
                );
                return <div className="py-20 text-center font-black text-slate-300 uppercase italic">Sekcja w przygotowaniu...</div>;
            };

            return (
                <div className="min-h-screen bg-slate-50 flex flex-col">
                    <Nav active={tab} setTab={setTab} />
                    <main className="max-w-7xl mx-auto px-4 py-8 flex-grow w-full">
                        {render()}
                    </main>
                    <footer className="bg-slate-900 text-white p-12 mt-20 border-t-8 border-red-600 text-center">
                        <h3 className="font-black text-2xl mb-4 tracking-tighter uppercase">BasketFrance Hub</h3>
                        <p className="text-slate-600 text-[10px] uppercase font-black tracking-widest">© 2024 Niezależne źródło francuskiego basketu.</p>
                    </footer>
                    <Chat />

                    {/* MODALS */}
                    {selectedNews && (
                        <div className="fixed inset-0 z-[200] bg-slate-900/80 backdrop-blur-md flex items-center justify-center p-4 animate-in">
                            <div className="bg-white rounded-[3rem] max-w-2xl w-full p-10 relative shadow-2xl">
                                <button onClick={() => setSelectedNews(null)} className="absolute top-6 right-6 p-2 bg-slate-100 rounded-full">✕</button>
                                <img src={selectedNews.img} className="w-full h-64 object-cover rounded-3xl mb-8 shadow-lg" />
                                <h2 className="text-4xl font-black italic uppercase leading-none mb-6 tracking-tighter">{selectedNews.title}</h2>
                                <p className="text-slate-600 leading-relaxed text-lg mb-10">Analiza BasketFrance Hub potwierdza: francuski system szkolenia INSEP dominuje światową scenę. Talent i atletyzm idą w parze z unikalną technologią szkolenia młodzieży.</p>
                                <button className="w-full bg-blue-900 text-white py-5 rounded-2xl font-black uppercase text-xs">Pobierz Raport PDF</button>
                            </div>
                        </div>
                    )}

                    {selectedPlayer && (
                        <div className="fixed inset-0 z-[200] bg-slate-900/80 backdrop-blur-md flex items-center justify-center p-4 animate-in">
                            <div className="bg-white rounded-[3rem] max-w-xl w-full p-12 text-center relative shadow-2xl border-4 border-blue-900">
                                <button onClick={() => setSelectedPlayer(null)} className="absolute top-8 right-8 text-slate-400">✕ Zamknij</button>
                                <img src={selectedPlayer.img} className="w-48 h-48 rounded-full mx-auto object-cover border-8 border-slate-50 shadow-2xl mb-8" />
                                <h2 className="text-5xl font-black italic uppercase tracking-tighter mb-4">{selectedPlayer.name}</h2>
                                <div className="bg-slate-50 p-6 rounded-[2rem] text-sm italic text-slate-500 leading-relaxed">"Obecność tego gracza na parkiecie kompletnie zmienia strategię rywala. Prawdziwy rim protector nowej ery."</div>
                            </div>
                        </div>
                    )}
                </div>
            );
        };

        ReactDOM.createRoot(document.getElementById('root')).render(<App />);
    </script>
</body>
</html>
