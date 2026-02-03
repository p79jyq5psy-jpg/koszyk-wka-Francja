<!DOCTYPE html>
<html lang="pl">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Basket France Hub | Oficjalny Portal</title>
    <!-- Tailwind CSS -->
    <script src="https://cdn.tailwindcss.com?plugins=typography,aspect-ratio"></script>
    <!-- Google Fonts -->
    <link href="https://fonts.googleapis.com/css2?family=Inter:wght@400;700;900&display=swap" rel="stylesheet">
    
    <style>
        body { font-family: 'Inter', sans-serif; background-color: #f8fafc; }
        .france-gradient { background: linear-gradient(90deg, #002395 0%, #002395 33%, #ffffff 33%, #ffffff 66%, #ed2939 66%, #ed2939 100%); }
        .hero-title { line-height: 0.85; letter-spacing: -0.05em; }
        .animate-fade-up { animation: fadeUp 0.8s cubic-bezier(0.16, 1, 0.3, 1) forwards; }
        @keyframes fadeUp { from { opacity: 0; transform: translateY(20px); } to { opacity: 1; transform: translateY(0); } }
        ::-webkit-scrollbar { width: 6px; }
        ::-webkit-scrollbar-thumb { background: #002395; border-radius: 10px; }
    </style>

    <!-- Import Map for React 19 and GenAI -->
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
<body class="overflow-x-hidden">
    <div id="root"></div>

    <script type="module">
        import React, { useState, useEffect, useRef } from 'react';
        import { createRoot } from 'react-dom/client';
        import { GoogleGenAI } from "@google/genai";

        // --- APPLIKACJA ---
        const App = () => {
            const [tab, setTab] = useState('home');
            const [chatOpen, setChatOpen] = useState(false);
            const [messages, setMessages] = useState([
                { role: 'ai', text: 'Bonjour! Jestem Twoim ekspertem od francuskiego basketu. O co chcesz zapytać?' }
            ]);
            const [input, setInput] = useState('');
            const [isTyping, setIsTyping] = useState(false);
            const chatEndRef = useRef(null);

            const scrollToBottom = () => chatEndRef.current?.scrollIntoView({ behavior: "smooth" });
            useEffect(scrollToBottom, [messages]);

            const handleAskAI = async () => {
                if (!input.trim()) return;
                const userText = input;
                setInput('');
                setMessages(prev => [...prev, { role: 'user', text: userText }]);
                setIsTyping(true);

                try {
                    const ai = new GoogleGenAI({ apiKey: window.process?.env?.API_KEY || "" });
                    const response = await ai.models.generateContent({
                        model: 'gemini-3-flash-preview',
                        contents: userText,
                        config: {
                            systemInstruction: "Jesteś ekspertem francuskiej koszykówki. Odpowiadaj z pasją, fachowo i po polsku. Skup się na NBA (Wembanyama) i LNB.",
                        }
                    });
                    setMessages(prev => [...prev, { role: 'ai', text: response.text }]);
                } catch (e) {
                    setMessages(prev => [...prev, { role: 'ai', text: 'Ekspert chwilowo niedostępny. Sprawdź klucz API.' }]);
                } finally {
                    setIsTyping(false);
                }
            };

            return (
                <div className="min-h-screen flex flex-col">
                    {/* Navigation */}
                    <nav className="sticky top-0 z-50 bg-white shadow-xl border-b-4 border-blue-900">
                        <div className="max-w-7xl mx-auto px-4 h-20 flex justify-between items-center">
                            <div className="flex items-center space-x-3 cursor-pointer" onClick={() => setTab('home')}>
                                <div className="w-10 h-10 bg-blue-900 text-white rounded-full flex items-center justify-center font-black text-2xl">F</div>
                                <span className="text-2xl font-black uppercase tracking-tighter text-blue-900 italic">Basket<span className="text-red-600">France</span></span>
                            </div>
                            <div className="hidden md:flex space-x-6">
                                {['home', 'nba', 'lnb', 'sklep'].map(t => (
                                    <button 
                                        key={t}
                                        onClick={() => setTab(t)}
                                        className={`text-[10px] font-black uppercase tracking-widest transition-colors ${tab === t ? 'text-red-600' : 'text-slate-400 hover:text-blue-900'}`}
                                    >
                                        {t === 'home' ? 'Start' : t === 'nba' ? 'Gwiazdy NBA' : t === 'lnb' ? 'Liga LNB' : 'Boutique'}
                                    </button>
                                ))}
                            </div>
                        </div>
                    </nav>

                    {/* Content */}
                    <main className="flex-grow">
                        {tab === 'home' && (
                            <div className="animate-fade-up">
                                {/* Hero */}
                                <header className="relative h-[80vh] flex items-center overflow-hidden bg-slate-900">
                                    <img src="https://images.unsplash.com/photo-1504450758481-7338eba7524a?q=80&w=1600" className="absolute inset-0 w-full h-full object-cover opacity-40 brightness-50" />
                                    <div className="relative z-10 max-w-7xl mx-auto px-4 text-white">
                                        <div className="inline-block bg-red-600 px-3 py-1 rounded-full text-[10px] font-black uppercase mb-6 tracking-widest">Wersja Elite 2024</div>
                                        <h1 className="text-7xl md:text-9xl font-black italic uppercase hero-title mb-8">
                                            L'Avenir<br/>Est <span className="text-red-600 underline">Bleu.</span>
                                        </h1>
                                        <p className="max-w-xl text-xl text-slate-300 font-medium mb-10 leading-relaxed">
                                            Od betonu Paryża po szczyty NBA. Francja redefiniuje światowy basket. Dołącz do elity.
                                        </p>
                                        <div className="flex space-x-4">
                                            <button onClick={() => setTab('nba')} className="bg-white text-blue-900 px-10 py-4 rounded-2xl font-black uppercase text-xs hover:scale-105 transition-transform">Śledź NBA</button>
                                            <button onClick={() => setTab('lnb')} className="bg-blue-900 text-white px-10 py-4 rounded-2xl font-black uppercase text-xs border border-blue-700 hover:scale-105 transition-transform">Liga LNB</button>
                                        </div>
                                    </div>
                                </header>

                                {/* Features */}
                                <section className="max-w-7xl mx-auto px-4 py-24 grid grid-cols-1 md:grid-cols-3 gap-12">
                                    {[
                                        { t: "Wemby Era", d: "Victor dominuje w defensywie San Antonio.", i: "https://images.unsplash.com/photo-1546519638-68e109498ffc?q=80&w=800" },
                                        { t: "Draft Dominance", d: "Historyczne #1 i #2 dla Francji w 2024.", i: "https://images.unsplash.com/photo-1519861531473-9200362f88bf?q=80&w=800" },
                                        { t: "EuroLeague Hub", d: "AS Monaco walczy o tron Europy.", i: "https://images.unsplash.com/photo-1574629810360-7efbbe195018?q=80&w=800" }
                                    ].map((f, i) => (
                                        <div key={i} className="group cursor-pointer">
                                            <div className="aspect-[4/5] rounded-[3rem] overflow-hidden mb-6 relative shadow-2xl">
                                                <img src={f.i} className="w-full h-full object-cover group-hover:scale-110 transition-transform duration-700" />
                                                <div className="absolute inset-0 bg-gradient-to-t from-blue-900/80 to-transparent"></div>
                                                <div className="absolute bottom-8 left-8 text-white">
                                                    <h3 className="text-3xl font-black uppercase italic mb-2">{f.t}</h3>
                                                    <p className="text-sm opacity-80">{f.d}</p>
                                                </div>
                                            </div>
                                        </div>
                                    ))}
                                </section>
                            </div>
                        )}

                        {tab === 'nba' && (
                            <div className="max-w-7xl mx-auto px-4 py-20 animate-fade-up">
                                <h2 className="text-6xl font-black italic uppercase text-center mb-16 tracking-tighter">Kings of the <span className="text-blue-900">League</span></h2>
                                <div className="grid grid-cols-1 md:grid-cols-2 gap-10">
                                    {[
                                        { n: "Victor Wembanyama", t: "Spurs", s: "21.4 PPG | 3.6 BPG", i: "https://images.unsplash.com/photo-1546519638-68e109498ffc?q=80&w=800" },
                                        { n: "Zaccharie Risacher", t: "Hawks", s: "15.1 PPG | #1 Pick", i: "https://images.unsplash.com/photo-1511447333015-45b65e60f6d5?q=80&w=800" }
                                    ].map(p => (
                                        <div key={p.n} className="bg-white p-10 rounded-[3.5rem] shadow-xl flex items-center space-x-8 border border-slate-100 hover:rotate-1 transition-all">
                                            <img src={p.i} className="w-32 h-32 rounded-3xl object-cover shadow-lg" />
                                            <div>
                                                <h3 className="text-3xl font-black uppercase italic tracking-tighter">{p.n}</h3>
                                                <p className="text-red-600 font-black text-[10px] uppercase mb-4">{p.t} Franchise Player</p>
                                                <div className="bg-slate-50 px-4 py-2 rounded-xl border font-bold text-blue-900 text-sm">{p.s}</div>
                                            </div>
                                        </div>
                                    ))}
                                </div>
                            </div>
                        )}

                        {tab === 'lnb' && (
                            <div className="max-w-4xl mx-auto px-4 py-20 animate-fade-up">
                                <div className="bg-white rounded-[3rem] shadow-2xl overflow-hidden border border-slate-100">
                                    <div className="bg-blue-900 p-8 text-white flex justify-between items-center">
                                        <h2 className="text-3xl font-black uppercase italic tracking-tighter">Betclic Élite Table</h2>
                                        <span className="text-[10px] font-bold opacity-50 uppercase tracking-widest">Sezon 24/25</span>
                                    </div>
                                    <div className="p-8">
                                        <table className="w-full text-left">
                                            <thead className="text-[10px] font-black uppercase text-slate-400 border-b">
                                                <tr><th className="py-4">Poz</th><th>Drużyna</th><th className="text-center">W-L</th></tr>
                                            </thead>
                                            <tbody className="divide-y">
                                                {[
                                                    { r: 1, t: "AS Monaco", s: "22-2" },
                                                    { r: 2, t: "Paris Basketball", s: "19-5" },
                                                    { r: 3, t: "LDLC ASVEL", s: "17-7" }
                                                ].map(row => (
                                                    <tr key={row.r} className="hover:bg-slate-50 transition-colors">
                                                        <td className="py-6 font-black text-slate-300">#{row.r}</td>
                                                        <td className="py-6 font-black uppercase italic text-xl">{row.t}</td>
                                                        <td className="py-6 text-center font-bold text-blue-900">{row.s}</td>
                                                    </tr>
                                                ))}
                                            </tbody>
                                        </table>
                                    </div>
                                </div>
                            </div>
                        )}

                        {tab === 'sklep' && (
                            <div className="py-40 text-center animate-fade-up">
                                <h2 className="text-5xl font-black uppercase italic text-slate-300">Coming Soon</h2>
                                <p className="text-slate-400 font-bold uppercase tracking-widest mt-4">Oficjalny Merch Les Bleus</p>
                            </div>
                        )}
                    </main>

                    {/* AI Chat Widget */}
                    <div className="fixed bottom-8 right-8 z-[100]">
                        {chatOpen ? (
                            <div className="bg-white w-80 md:w-96 h-[500px] shadow-2xl rounded-[2.5rem] flex flex-col border border-slate-100 overflow-hidden animate-fade-up">
                                <div className="bg-blue-900 p-6 text-white flex justify-between items-center">
                                    <div className="flex items-center space-x-2">
                                        <div className="w-2 h-2 bg-green-400 rounded-full animate-pulse"></div>
                                        <span className="font-black text-xs uppercase tracking-widest">Ekspert Basketu</span>
                                    </div>
                                    <button onClick={() => setChatOpen(false)} className="hover:rotate-90 transition-transform">✕</button>
                                </div>
                                <div className="flex-grow p-6 overflow-y-auto space-y-4 bg-slate-50">
                                    {messages.map((m, i) => (
                                        <div key={i} className={`flex ${m.role === 'user' ? 'justify-end' : 'justify-start'}`}>
                                            <div className={`max-w-[85%] p-4 rounded-2xl text-sm ${m.role === 'user' ? 'bg-red-600 text-white shadow-lg rounded-br-none' : 'bg-white shadow-sm border border-slate-100 rounded-bl-none'}`}>
                                                {m.text}
                                            </div>
                                        </div>
                                    ))}
                                    {isTyping && <div className="text-[10px] animate-pulse italic text-slate-400">Ekspert analizuje parkiet...</div>}
                                    <div ref={chatEndRef} />
                                </div>
                                <div className="p-4 bg-white border-t flex gap-2">
                                    <input 
                                        type="text" 
                                        value={input} 
                                        onChange={e => setInput(e.target.value)}
                                        onKeyDown={e => e.key === 'Enter' && handleAskAI()}
                                        placeholder="Zapytaj o Wemby'ego..." 
                                        className="flex-grow bg-slate-100 rounded-full px-5 py-3 text-sm outline-none focus:ring-2 focus:ring-blue-900" 
                                    />
                                    <button onClick={handleAskAI} className="bg-blue-900 text-white w-12 h-12 rounded-full shadow-lg hover:bg-red-600 transition-colors">➤</button>
                                </div>
                            </div>
                        ) : (
                            <button 
                                onClick={() => setChatOpen(true)}
                                className="bg-blue-900 text-white p-6 rounded-full shadow-2xl hover:scale-110 transition-transform border-4 border-white flex items-center space-x-3"
                            >
                                <span className="font-black text-xs uppercase hidden md:inline tracking-widest">Ekspert AI</span>
                                <svg className="w-6 h-6" fill="none" stroke="currentColor" viewBox="0 0 24 24"><path strokeLinecap="round" strokeLinejoin="round" strokeWidth="2" d="M8 10h.01M12 10h.01M16 10h.01M9 16H5a2 2 0 01-2-2V6a2 2 0 012-2h14a2 2 0 012 2v8a2 2 0 01-2 2h-5l-5 5v-5z"></path></svg>
                            </button>
                        )}
                    </div>

                    {/* Footer */}
                    <footer className="bg-slate-900 text-white py-16 border-t-8 border-red-600 text-center">
                        <h3 className="text-3xl font-black italic uppercase tracking-tighter mb-4">BasketFrance Hub</h3>
                        <div className="france-gradient h-1 w-32 mx-auto mb-8"></div>
                        <p className="text-slate-500 text-[10px] font-black uppercase tracking-[0.4em]">© 2024 Niezależne Centrum Koszykówki</p>
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
