<!DOCTYPE html>
<html lang="pl">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Basket France Hub - Oficjalne Centrum</title>
    <!-- Biblioteki ładowane bezpośrednio (Bulletproof method) -->
    <script src="https://cdn.tailwindcss.com?plugins=typography"></script>
    <script src="https://unpkg.com/react@18/umd/react.production.min.js"></script>
    <script src="https://unpkg.com/react-dom@18/umd/react-dom.production.min.js"></script>
    <script src="https://unpkg.com/@babel/standalone/babel.min.js"></script>
    <link href="https://fonts.googleapis.com/css2?family=Inter:wght@400;700;900&display=swap" rel="stylesheet">
    
    <style>
        body { font-family: 'Inter', sans-serif; }
        .france-gradient { background: linear-gradient(90deg, #002395 0%, #002395 33%, #ffffff 33%, #ffffff 66%, #ed2939 66%, #ed2939 100%); }
        .animate-fade-in { animation: fadeIn 0.6s ease-out forwards; }
        @keyframes fadeIn { from { opacity: 0; transform: translateY(10px); } to { opacity: 1; transform: translateY(0); } }
        ::-webkit-scrollbar { width: 8px; }
        ::-webkit-scrollbar-thumb { background: #002395; border-radius: 4px; }
    </style>
<script type="importmap">
{
  "imports": {
    "@google/genai": "https://esm.sh/@google/genai@^1.39.0",
    "react": "https://esm.sh/react@^19.2.4",
    "react/": "https://esm.sh/react@^19.2.4/"
  }
}
</script>
</head>
<body class="bg-slate-50">
    <div id="root"></div>

    <!-- Logika aplikacji (React + AI) -->
    <script type="text/babel">
        const { useState, useEffect, useRef } = React;

        // Dynamiczne ładowanie SDK Gemini (bezpieczna metoda dla single-file)
        let aiClient = null;
        const initAI = async () => {
            if (!aiClient) {
                const module = await import('https://esm.sh/@google/genai@^1.39.0');
                aiClient = new module.GoogleGenAI({ apiKey: window.process?.env?.API_KEY || "" });
            }
            return aiClient;
        };

        const App = () => {
            const [tab, setTab] = useState('news');
            const [chatOpen, setChatOpen] = useState(false);
            const [messages, setMessages] = useState([{ role: 'ai', text: 'Bonjour! Jestem Twoim ekspertem od francuskiej koszykówki. O czym pogadamy?' }]);
            const [input, setInput] = useState('');
            const [loading, setLoading] = useState(false);
            const chatRef = useRef(null);

            useEffect(() => {
                if (chatRef.current) chatRef.current.scrollTop = chatRef.current.scrollHeight;
            }, [messages]);

            const handleSend = async () => {
                if (!input.trim()) return;
                const userText = input;
                setInput('');
                setMessages(prev => [...prev, { role: 'user', text: userText }]);
                setLoading(true);

                try {
                    const ai = await initAI();
                    const response = await ai.models.generateContent({
                        model: 'gemini-3-flash-preview',
                        contents: userText,
                        config: {
                            systemInstruction: "Jesteś ekspertem francuskiej koszykówki. Odpowiadaj z pasją, po polsku, używając terminów sportowych.",
                        }
                    });
                    setMessages(prev => [...prev, { role: 'ai', text: response.text }]);
                } catch (e) {
                    setMessages(prev => [...prev, { role: 'ai', text: 'Błąd połączenia z ekspertem AI.' }]);
                } finally {
                    setLoading(false);
                }
            };

            const news = [
                { id: 1, title: "Wembanyama: Król Bloków", cat: "NBA", img: "https://images.unsplash.com/photo-1546519638-68e109498ffc?q=80&w=800", text: "Victor prowadzi w klasyfikacji defensywnej NBA." },
                { id: 2, title: "Monaco vs Paris", cat: "LNB", img: "https://images.unsplash.com/photo-1504450758481-7338eba7524a?q=80&w=800", text: "Wielkie starcie gigantów o fotel lidera Betclic Élite." },
                { id: 3, title: "Draft 2024: Wyniki", cat: "Draft", img: "https://images.unsplash.com/photo-1519861531473-9200362f88bf?q=80&w=800", text: "Historyczny moment dla francuskiego basketu." }
            ];

            return (
                <div className="min-h-screen flex flex-col">
                    {/* Header */}
                    <nav className="sticky top-0 z-50 bg-white shadow-md border-b-4 border-blue-900">
                        <div className="max-w-7xl mx-auto px-4 h-20 flex justify-between items-center">
                            <div className="flex items-center space-x-2 cursor-pointer" onClick={() => setTab('news')}>
                                <div className="w-10 h-10 bg-blue-900 text-white rounded-full flex items-center justify-center font-bold text-xl">F</div>
                                <h1 className="text-2xl font-black text-blue-900 uppercase tracking-tighter">Basket<span className="text-red-600">France</span></h1>
                            </div>
                            <div className="hidden md:flex space-x-6">
                                {['news', 'players', 'standings', 'shop'].map(t => (
                                    <button 
                                        key={t} 
                                        onClick={() => setTab(t)}
                                        className={`text-[10px] font-black uppercase tracking-widest ${tab === t ? 'text-blue-900 border-b-2 border-red-600' : 'text-slate-400 hover:text-blue-900'}`}
                                    >
                                        {t === 'news' ? 'Aktualności' : t === 'players' ? 'Gwiazdy' : t === 'standings' ? 'Tabela' : 'Sklep'}
                                    </button>
                                ))}
                            </div>
                        </div>
                    </nav>

                    {/* Content */}
                    <main className="flex-grow">
                        {tab === 'news' && (
                            <div className="animate-fade-in">
                                <div className="relative h-[60vh] flex items-center overflow-hidden">
                                    <img src="https://images.unsplash.com/photo-1504450758481-7338eba7524a?q=80&w=1600" className="absolute inset-0 w-full h-full object-cover brightness-[0.3]" />
                                    <div className="relative z-10 max-w-7xl mx-auto px-4 text-white">
                                        <span className="bg-red-600 px-3 py-1 rounded-full text-[10px] font-black uppercase mb-4 inline-block">Sezon 2024/25</span>
                                        <h2 className="text-7xl font-black italic uppercase leading-none tracking-tighter mb-6">Le Basket<br/>C'est <span className="text-red-600">Nous.</span></h2>
                                        <button onClick={() => setTab('players')} className="bg-white text-blue-900 px-8 py-4 rounded-xl font-black uppercase text-xs hover:bg-slate-100 transition-all">Śledź gwiazdy NBA</button>
                                    </div>
                                </div>
                                <div className="max-w-7xl mx-auto px-4 py-16 grid grid-cols-1 md:grid-cols-3 gap-8">
                                    {news.map(n => (
                                        <div key={n.id} className="bg-white rounded-[2rem] overflow-hidden shadow-lg border border-slate-100 group cursor-pointer">
                                            <div className="h-48 overflow-hidden relative">
                                                <img src={n.img} className="w-full h-full object-cover group-hover:scale-105 transition-all" />
                                                <div className="absolute top-4 left-4 bg-white/90 px-2 py-1 rounded-md text-[8px] font-black text-blue-900">{n.cat}</div>
                                            </div>
                                            <div className="p-6">
                                                <h3 className="text-xl font-black uppercase mb-2">{n.title}</h3>
                                                <p className="text-slate-500 text-sm">{n.text}</p>
                                            </div>
                                        </div>
                                    ))}
                                </div>
                            </div>
                        )}

                        {tab === 'players' && (
                            <div className="max-w-7xl mx-auto px-4 py-16 animate-fade-in">
                                <h2 className="text-5xl font-black text-center mb-12 uppercase italic tracking-tighter">Królowie <span className="text-blue-900">NBA</span></h2>
                                <div className="grid grid-cols-1 lg:grid-cols-2 gap-8">
                                    {[
                                        { n: "Victor Wembanyama", t: "Spurs", s: "21.4 PPG", i: "https://images.unsplash.com/photo-1546519638-68e109498ffc?q=80&w=800" },
                                        { n: "Zaccharie Risacher", t: "Hawks", s: "15.1 PPG", i: "https://images.unsplash.com/photo-1511447333015-45b65e60f6d5?q=80&w=800" }
                                    ].map(p => (
                                        <div key={p.n} className="bg-white p-8 rounded-[3rem] shadow-xl flex items-center space-x-6 border border-slate-100">
                                            <img src={p.i} className="w-32 h-32 rounded-3xl object-cover" />
                                            <div>
                                                <h3 className="text-2xl font-black uppercase italic tracking-tighter">{p.n}</h3>
                                                <p className="text-red-600 font-bold text-xs uppercase">{p.t}</p>
                                                <div className="mt-4 inline-block bg-blue-900 text-white px-4 py-1 rounded-full text-[10px] font-black">{p.s}</div>
                                            </div>
                                        </div>
                                    ))}
                                </div>
                            </div>
                        )}
                        
                        {(tab === 'standings' || tab === 'shop') && (
                            <div className="py-32 text-center text-slate-300 animate-fade-in">
                                <p className="text-4xl font-black uppercase italic">Sekcja w budowie</p>
                            </div>
                        )}
                    </main>

                    {/* Chat AI */}
                    <div className="fixed bottom-6 right-6 z-[100]">
                        {chatOpen ? (
                            <div className="bg-white w-80 md:w-96 h-[500px] shadow-2xl rounded-[2rem] flex flex-col border border-slate-200 overflow-hidden animate-fade-in">
                                <div className="bg-blue-900 p-4 text-white flex justify-between items-center">
                                    <span className="font-black text-xs uppercase tracking-widest">Ekspert AI</span>
                                    <button onClick={() => setChatOpen(false)}>✕</button>
                                </div>
                                <div ref={chatRef} className="flex-grow p-4 overflow-y-auto space-y-4 bg-slate-50">
                                    {messages.map((m, i) => (
                                        <div key={i} className={`flex ${m.role === 'user' ? 'justify-end' : 'justify-start'}`}>
                                            <div className={`max-w-[85%] p-3 rounded-2xl text-sm ${m.role === 'user' ? 'bg-red-600 text-white' : 'bg-white shadow-sm border border-slate-100'}`}>
                                                {m.text}
                                            </div>
                                        </div>
                                    ))}
                                    {loading && <div className="text-[10px] animate-pulse italic text-slate-400">Ekspert analizuje dane...</div>}
                                </div>
                                <div className="p-4 bg-white border-t flex gap-2">
                                    <input type="text" value={input} onChange={e => setInput(e.target.value)} onKeyDown={e => e.key === 'Enter' && handleSend()} placeholder="NBA, LNB?" className="flex-grow bg-slate-100 rounded-full px-4 py-2 text-sm outline-none focus:ring-2 focus:ring-blue-900" />
                                    <button onClick={handleSend} className="bg-blue-900 text-white p-2 rounded-full px-4">➤</button>
                                </div>
                            </div>
                        ) : (
                            <button onClick={() => setChatOpen(true)} className="bg-blue-900 text-white p-5 rounded-full shadow-2xl hover:scale-110 transition-transform border-4 border-white flex items-center space-x-2">
                                <span className="font-black text-[10px] uppercase">Ekspert AI</span>
                                <svg className="w-5 h-5" fill="none" stroke="currentColor" viewBox="0 0 24 24"><path strokeLinecap="round" strokeLinejoin="round" strokeWidth="2" d="M8 10h.01M12 10h.01M16 10h.01M9 16H5a2 2 0 01-2-2V6a2 2 0 012-2h14a2 2 0 012 2v8a2 2 0 01-2 2h-5l-5 5v-5z"></path></svg>
                            </button>
                        )}
                    </div>

                    {/* Footer */}
                    <footer className="bg-slate-900 text-white py-12 border-t-8 border-red-600 text-center">
                        <p className="text-2xl font-black uppercase tracking-tighter mb-4">BasketFrance Hub</p>
                        <div className="france-gradient h-1 w-32 mx-auto mb-6"></div>
                        <p className="text-slate-500 text-[10px] uppercase font-bold tracking-[0.3em]">© 2024 Wszystkie prawa zastrzeżone.</p>
                    </footer>
                </div>
            );
        };

        const root = ReactDOM.createRoot(document.getElementById('root'));
        root.render(<App />);
    </script>
</body>
</html>
