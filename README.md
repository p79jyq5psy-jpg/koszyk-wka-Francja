<!DOCTYPE html>
<html lang="pl">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Basket France Hub - Elite Coverage</title>
    <script src="https://cdn.tailwindcss.com?plugins=typography"></script>
    <link href="https://fonts.googleapis.com/css2?family=Inter:wght@400;700;900&display=swap" rel="stylesheet">
    <style>
        body { font-family: 'Inter', sans-serif; scroll-behavior: smooth; }
        .france-gradient { background: linear-gradient(90deg, #002395 0%, #002395 33%, #ffffff 33%, #ffffff 66%, #ed2939 66%, #ed2939 100%); }
        .hero-mesh {
            background-color: #f8fafc;
            background-image: radial-gradient(at 0% 0%, hsla(225,100%,18%,0.1) 0, transparent 50%), 
                              radial-gradient(at 100% 100%, hsla(355,80%,52%,0.1) 0, transparent 50%);
        }
        .animate-in { animation: fadeIn 0.6s cubic-bezier(0.16, 1, 0.3, 1) forwards; }
        @keyframes fadeIn { from { opacity: 0; transform: translateY(15px); } to { opacity: 1; transform: translateY(0); } }
        .card-blur { backdrop-filter: blur(8px); background: rgba(255, 255, 255, 0.8); }
        ::-webkit-scrollbar { width: 6px; }
        ::-webkit-scrollbar-thumb { background: #002395; border-radius: 10px; }
    </style>
    <script type="importmap">
{
  "imports": {
    "react": "https://esm.sh/react@19",
    "react-dom/client": "https://esm.sh/react-dom@19/client",
    "@google/genai": "https://esm.sh/@google/genai",
    "react/": "https://esm.sh/react@^19.2.4/"
  }
}
</script>
</head>
<body class="bg-slate-50 text-slate-900 overflow-x-hidden">
    <div id="root"></div>

    <script type="module">
        import React, { useState, useEffect, useRef } from 'react';
        import { createRoot } from 'react-dom/client';
        import { GoogleGenAI } from "@google/genai";

        // --- AI LOGIC ---
        const askExpert = async (query) => {
            const apiKey = process.env.API_KEY || "";
            if (!apiKey) return "Błąd: Brak klucza API.";
            
            const ai = new GoogleGenAI({ apiKey });
            try {
                const response = await ai.models.generateContent({
                    model: 'gemini-3-flash-preview',
                    contents: query,
                    config: {
                        systemInstruction: "Jesteś ekspertem francuskiej koszykówki. Twoja wiedza obejmuje NBA (Wembanyama, Risacher), ligę LNB i reprezentację. Odpowiadaj z pasją, po polsku, używaj terminów sportowych.",
                    }
                });
                return response.text;
            } catch (err) {
                console.error(err);
                return "Nie udało się połączyć z ekspertem AI.";
            }
        };

        // --- COMPONENTS ---
        const App = () => {
            const [tab, setTab] = useState('news');
            const [chatOpen, setChatOpen] = useState(false);
            const [messages, setMessages] = useState([{ role: 'ai', text: 'Bonjour! O jakiej gwieździe francuskiego basketu dziś porozmawiamy?' }]);
            const [input, setInput] = useState('');
            const [loading, setLoading] = useState(false);
            const chatEndRef = useRef(null);

            const scrollToBottom = () => chatEndRef.current?.scrollIntoView({ behavior: "smooth" });
            useEffect(scrollToBottom, [messages]);

            const handleChat = async () => {
                if (!input.trim()) return;
                const userMsg = input;
                setInput('');
                setMessages(m => [...m, { role: 'user', text: userMsg }]);
                setLoading(true);
                const reply = await askExpert(userMsg);
                setMessages(m => [...m, { role: 'ai', text: reply }]);
                setLoading(false);
            };

            const nbaStars = [
                { name: "Victor Wembanyama", team: "Spurs", stats: "21.4 PPG | 3.6 BPG", img: "https://images.unsplash.com/photo-1546519638-68e109498ffc?q=80&w=800" },
                { name: "Zaccharie Risacher", team: "Hawks", stats: "15.1 PPG | #1 Pick", img: "https://images.unsplash.com/photo-1511447333015-45b65e60f6d5?q=80&w=800" },
                { name: "Alex Sarr", team: "Wizards", stats: "12.8 PPG | #2 Pick", img: "https://images.unsplash.com/photo-1574629810360-7efbbe195018?q=80&w=800" },
                { name: "Rudy Gobert", team: "Wolves", stats: "14.0 PPG | 4x DPOY", img: "https://images.unsplash.com/photo-1505666287802-931dc8394b5f?q=80&w=800" }
            ];

            return (
                <div className="min-h-screen flex flex-col">
                    {/* Header */}
                    <header className="sticky top-0 z-50 bg-white shadow-sm border-b-4 border-blue-900">
                        <div className="max-w-7xl mx-auto px-4 h-20 flex justify-between items-center">
                            <div className="flex items-center space-x-3 cursor-pointer" onClick={() => setTab('news')}>
                                <div className="w-10 h-10 bg-blue-900 text-white rounded-full flex items-center justify-center font-black text-2xl">F</div>
                                <h1 className="text-2xl font-black text-blue-900 uppercase tracking-tighter">Basket<span className="text-red-600">France</span> Hub</h1>
                            </div>
                            <nav className="hidden md:flex space-x-2">
                                {['news', 'players', 'standings', 'sklep'].map(t => (
                                    <button 
                                        key={t}
                                        onClick={() => setTab(t)}
                                        className={`px-4 py-2 rounded-xl text-[10px] font-black uppercase tracking-widest transition-all ${tab === t ? 'bg-blue-900 text-white shadow-lg' : 'text-slate-400 hover:text-blue-900 hover:bg-slate-50'}`}
                                    >
                                        {t === 'news' ? 'News' : t === 'players' ? 'NBA Stars' : t === 'standings' ? 'LNB Table' : 'Boutique'}
                                    </button>
                                ))}
                            </nav>
                        </div>
                    </header>

                    {/* Main Content */}
                    <main className="flex-grow hero-mesh">
                        {tab === 'news' && (
                            <div className="animate-in">
                                <section className="relative h-[70vh] flex items-center overflow-hidden">
                                    <img src="https://images.unsplash.com/photo-1504450758481-7338eba7524a?q=80&w=1600" className="absolute inset-0 w-full h-full object-cover brightness-[0.25]" alt="Hero" />
                                    <div className="relative z-10 max-w-7xl mx-auto px-4">
                                        <div className="inline-block bg-red-600 px-4 py-1 rounded-full text-[10px] font-black text-white uppercase mb-6 tracking-widest shadow-xl">NBA Season 24/25 Live</div>
                                        <h2 className="text-6xl md:text-9xl font-black text-white italic leading-[0.85] tracking-tighter uppercase mb-8">
                                            Le Basket<br/>C'est <span className="text-red-600 underline">Nous.</span>
                                        </h2>
                                        <div className="flex space-x-4">
                                            <button onClick={() => setTab('players')} className="bg-white text-blue-900 px-10 py-4 rounded-2xl font-black uppercase text-xs tracking-widest shadow-2xl hover:scale-105 transition-all">Śledź Gwiazdy</button>
                                            <button onClick={() => setTab('standings')} className="bg-blue-900 text-white px-10 py-4 rounded-2xl font-black uppercase text-xs tracking-widest border border-blue-800 shadow-2xl hover:scale-105 transition-all">Liga LNB</button>
                                        </div>
                                    </div>
                                </section>
                                
                                <section className="max-w-7xl mx-auto px-4 py-20">
                                    <h3 className="text-4xl font-black italic uppercase tracking-tighter mb-12 border-l-8 border-red-600 pl-6">Najnowsze Raporty</h3>
                                    <div className="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-3 gap-10">
                                        {[
                                            { title: "Zaccharie Risacher: Powiew Świeżości", cat: "Draft", img: "https://images.unsplash.com/photo-1546519638-68e109498ffc?q=80&w=800" },
                                            { title: "Wembanyama bije rekordy bloków", cat: "NBA", img: "https://images.unsplash.com/photo-1519861531473-9200362f88bf?q=80&w=800" },
                                            { title: "EuroLeague: Monaco gotowe na F4", cat: "Europe", img: "https://images.unsplash.com/photo-1574629810360-7efbbe195018?q=80&w=800" }
                                        ].map((n, i) => (
                                            <div key={i} className="group card-blur rounded-[2.5rem] overflow-hidden border border-slate-100 shadow-sm hover:shadow-2xl transition-all cursor-pointer">
                                                <div className="h-56 overflow-hidden relative">
                                                    <img src={n.img} className="w-full h-full object-cover group-hover:scale-110 transition-transform duration-700" alt={n.title} />
                                                    <div className="absolute top-6 left-6 bg-white/90 px-3 py-1 rounded-lg text-[8px] font-black text-blue-900 uppercase shadow-sm">{n.cat}</div>
                                                </div>
                                                <div className="p-8">
                                                    <h4 className="text-xl font-black italic uppercase leading-tight mb-4 group-hover:text-blue-900 transition-colors">{n.title}</h4>
                                                    <p className="text-slate-500 text-xs font-bold uppercase tracking-widest">Czytaj więcej →</p>
                                                </div>
                                            </div>
                                        ))}
                                    </div>
                                </section>
                            </div>
                        )}

                        {tab === 'players' && (
                            <div className="max-w-7xl mx-auto px-4 py-20 animate-in">
                                <div className="text-center mb-16">
                                    <h2 className="text-6xl font-black italic uppercase tracking-tighter leading-none mb-4">Generacja <span className="text-blue-900 underline decoration-red-600">Unicornów.</span></h2>
                                    <p className="text-slate-400 font-bold uppercase tracking-[0.3em] text-[10px]">Francuska dominacja w NBA</p>
                                </div>
                                <div className="grid grid-cols-1 lg:grid-cols-2 gap-10">
                                    {nbaStars.map(p => (
                                        <div key={p.name} className="group flex bg-white rounded-[3.5rem] overflow-hidden shadow-xl border border-slate-100 hover:shadow-2xl transition-all">
                                            <div className="w-2/5 overflow-hidden relative">
                                                <img src={p.img} className="absolute inset-0 w-full h-full object-cover group-hover:scale-110 transition-all duration-1000" alt={p.name} />
                                            </div>
                                            <div className="w-3/5 p-12">
                                                <h3 className="text-3xl font-black italic uppercase tracking-tighter mb-1">{p.name}</h3>
                                                <p className="text-red-600 font-black uppercase text-[10px] tracking-widest mb-6">{p.team} Franchise</p>
                                                <div className="bg-slate-50 p-6 rounded-[2rem] border border-slate-100 mb-8">
                                                    <p className="text-[10px] font-black text-slate-400 uppercase mb-2">Season Stats</p>
                                                    <p className="text-xl font-black text-blue-900">{p.stats}</p>
                                                </div>
                                                <button className="w-full bg-slate-900 text-white py-4 rounded-2xl font-black uppercase text-xs tracking-widest hover:bg-red-600 transition-colors shadow-lg">Raport Skautingowy</button>
                                            </div>
                                        </div>
                                    ))}
                                </div>
                            </div>
                        )}

                        {tab === 'standings' && (
                            <div className="max-w-4xl mx-auto px-4 py-20 animate-in text-center">
                                <h2 className="text-5xl font-black italic uppercase tracking-tighter mb-12">LNB Betclic <span className="text-red-600 underline">Elite</span></h2>
                                <div className="bg-white rounded-[3rem] overflow-hidden shadow-2xl border border-slate-100">
                                    <table className="w-full text-left">
                                        <thead className="bg-slate-50 text-[10px] font-black uppercase tracking-[0.2em] text-slate-400">
                                            <tr>
                                                <th className="p-8">Poz</th><th>Drużyna</th><th className="text-center">W</th><th className="text-center">P</th>
                                            </tr>
                                        </thead>
                                        <tbody className="divide-y divide-slate-100">
                                            {[
                                                { r: 1, t: "AS Monaco", w: 21, l: 3 },
                                                { r: 2, t: "Paris Basketball", w: 18, l: 6 },
                                                { r: 3, t: "LDLC ASVEL", w: 17, l: 7 },
                                                { r: 4, t: "JL Bourg", w: 16, l: 8 }
                                            ].map(row => (
                                                <tr key={row.r} className="hover:bg-blue-50/50 transition-colors">
                                                    <td className="p-8 font-black text-slate-300">#{row.r}</td>
                                                    <td className="p-8 font-black text-slate-800 uppercase tracking-tight italic text-xl">{row.t}</td>
                                                    <td className="p-8 text-center text-green-600 font-black">{row.w}</td>
                                                    <td className="p-8 text-center text-red-600 font-black">{row.l}</td>
                                                </tr>
                                            ))}
                                        </tbody>
                                    </table>
                                </div>
                            </div>
                        )}
                        
                        {tab === 'sklep' && (
                            <div className="py-40 text-center animate-in">
                                <h2 className="text-6xl font-black italic uppercase tracking-tighter text-slate-300">Sklep Wkrótce</h2>
                                <p className="text-slate-400 font-bold uppercase tracking-widest text-[10px] mt-4">Merch Les Bleus 2024</p>
                            </div>
                        )}
                    </main>

                    {/* Chatbot AI */}
                    <div className="fixed bottom-8 right-8 z-[100]">
                        {chatOpen ? (
                            <div className="bg-white w-[350px] h-[500px] shadow-2xl rounded-[2.5rem] flex flex-col border border-slate-100 overflow-hidden animate-in">
                                <div className="bg-blue-900 p-6 text-white flex justify-between items-center">
                                    <div className="flex items-center space-x-2">
                                        <div className="w-2 h-2 bg-green-400 rounded-full animate-pulse"></div>
                                        <span className="font-black text-xs uppercase tracking-widest">Ekspert AI</span>
                                    </div>
                                    <button onClick={() => setChatOpen(false)} className="hover:rotate-90 transition-transform">✕</button>
                                </div>
                                <div className="flex-grow p-6 overflow-y-auto space-y-4 bg-slate-50/50">
                                    {messages.map((m, i) => (
                                        <div key={i} className={`flex ${m.role === 'user' ? 'justify-end' : 'justify-start'}`}>
                                            <div className={`max-w-[85%] p-4 rounded-[1.5rem] text-sm leading-relaxed ${m.role === 'user' ? 'bg-red-600 text-white shadow-lg rounded-br-none' : 'bg-white shadow-sm border border-slate-100 rounded-bl-none'}`}>
                                                {m.text}
                                            </div>
                                        </div>
                                    ))}
                                    {loading && <div className="text-[10px] animate-pulse italic text-slate-400 pl-2">Analiza danych...</div>}
                                    <div ref={chatEndRef} />
                                </div>
                                <div className="p-4 bg-white border-t flex gap-2">
                                    <input 
                                        type="text" 
                                        value={input} 
                                        onChange={e => setInput(e.target.value)}
                                        onKeyDown={e => e.key === 'Enter' && handleChat()}
                                        placeholder="Zapytaj o Wemby'ego..." 
                                        className="flex-grow bg-slate-100 rounded-full px-5 py-3 text-sm outline-none focus:ring-2 focus:ring-blue-900"
                                    />
                                    <button onClick={handleChat} className="bg-blue-900 text-white w-12 h-12 rounded-full flex items-center justify-center hover:bg-red-600 transition-colors shadow-lg">➤</button>
                                </div>
                            </div>
                        ) : (
                            <button 
                                onClick={() => setChatOpen(true)}
                                className="bg-blue-900 text-white p-6 rounded-full shadow-2xl hover:scale-110 transition-transform flex items-center space-x-3 border-4 border-white"
                            >
                                <span className="font-black text-xs uppercase hidden md:inline tracking-widest">Ekspert AI</span>
                                <svg className="w-6 h-6" fill="none" stroke="currentColor" viewBox="0 0 24 24"><path strokeLinecap="round" strokeLinejoin="round" strokeWidth="2" d="M8 10h.01M12 10h.01M16 10h.01M9 16H5a2 2 0 01-2-2V6a2 2 0 012-2h14a2 2 0 012 2v8a2 2 0 01-2 2h-5l-5 5v-5z"></path></svg>
                            </button>
                        )}
                    </div>

                    {/* Footer */}
                    <footer className="bg-slate-900 text-white py-20 border-t-8 border-red-600">
                        <div className="max-w-7xl mx-auto px-4 text-center">
                            <h3 className="text-3xl font-black italic uppercase tracking-tighter mb-4">BasketFrance Hub</h3>
                            <div className="france-gradient h-1 w-24 mx-auto mb-8"></div>
                            <p className="text-slate-500 text-[10px] font-black uppercase tracking-[0.5em]">© 2024 Niezależne Centrum Francuskiego Basketu</p>
                        </div>
                    </footer>
                </div>
            );
        };

        const container = document.getElementById('root');
        const root = createRoot(container);
        root.render(<App />);
    </script>
</body>
</html>
