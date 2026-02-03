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
</head>
<body class="bg-slate-50">
    <div id="root"></div>
    <script type="text/babel">
        const { useState } = React;

        const App = () => {
          return (
            <div className="min-h-screen">
              <header className="bg-blue-900 text-white p-6 shadow-xl border-b-4 border-red-600">
                <h1 className="text-3xl font-black italic uppercase tracking-tighter">
                  Basket<span className="text-red-500">France</span> Hub
                </h1>
              </header>
              <main className="max-w-4xl mx-auto py-20 px-4 text-center">
                <h2 className="text-7xl font-black text-blue-900 uppercase italic leading-none">
                  Le Basket <br/><span className="text-red-600">C'est Nous.</span>
                </h2>
                <p className="mt-10 text-xl font-bold text-slate-500 uppercase tracking-widest">
                  Strona została naprawiona i działa!
                </p>
                <div className="mt-16 bg-white p-10 rounded-[3rem] shadow-2xl border border-slate-100">
                   <p className="text-blue-900 font-black text-2xl uppercase">Witamy w świecie francuskiego basketu</p>
                </div>
              </main>
            </div>
          );
        };

        const root = ReactDOM.createRoot(document.getElementById('root'));
        root.render(<App />);
    </script>
</body>
</html>
