# relatoriodemetas<!DOCTYPE html>
<html lang="pt">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Dashboard de Metas - Processos e Contratos</title>
    
    <!-- Tailwind CSS para os estilos -->
    <script src="https://cdn.tailwindcss.com"></script>
    
    <!-- FontAwesome para os ícones -->
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/css/all.min.css">
    
    <!-- Bibliotecas React e ReactDOM -->
    <script crossorigin src="https://unpkg.com/react@18/umd/react.production.min.js"></script>
    <script crossorigin src="https://unpkg.com/react-dom@18/umd/react-dom.production.min.js"></script>
    
    <!-- Babel para compilar o código React no navegador -->
    <script src="https://unpkg.com/babel-standalone@6/babel.min.js"></script>
</head>
<body class="bg-slate-50 text-slate-800 font-sans">
    
    <div id="root"></div>

    <script type="text/babel">
        const { useState } = React;

        function DashboardMetas() {
            // Dados extraídos do PDF
            const [data, setData] = useState({
                distribuidosGeral: 20,
                producaoGeral: 14,
                onboardingGeral: 9,
                distribuidosMateus: 6,
                producaoMateus: 10,
                onboardingMateus: 8
            });

            const [isEditing, setIsEditing] = useState(false);

            // Metas
            const METAS = {
                distribuidosGeral: 40,
                contratosGeral: 40,
                contratosMateus: 30
            };

            // Cálculos - Cenário Atual
            const contratosFechadosGeral = data.distribuidosGeral + data.producaoGeral;
            const contratosFechadosMateus = data.distribuidosMateus + data.producaoMateus;

            // Cálculos - Suposição (Com Onboarding)
            const projDistribuidosGeral = data.distribuidosGeral + data.onboardingGeral;
            const projContratosGeral = contratosFechadosGeral + data.onboardingGeral;
            const projContratosMateus = contratosFechadosMateus + data.onboardingMateus;

            const handleInputChange = (e) => {
                const { name, value } = e.target;
                setData(prev => ({
                    ...prev,
                    [name]: parseInt(value) || 0
                }));
            };

            // Componente de Barra de Progresso
            const ProgressBar = ({ current, projected, goal, colorClass }) => {
                const currentPercent = Math.min((current / goal) * 100, 100);
                const projectedPercent = Math.min(((projected - current) / goal) * 100, 100 - currentPercent);
                
                return (
                    <div className="w-full bg-slate-200 rounded-full h-3.5 mt-2 overflow-hidden flex">
                        <div 
                            className={`h-3.5 ${colorClass} transition-all duration-500`} 
                            style={{ width: `${currentPercent}%` }}
                            title={`Atual: ${current}`}
                        ></div>
                        <div 
                            className={`h-3.5 ${colorClass} opacity-40 transition-all duration-500`} 
                            style={{ width: `${projectedPercent}%` }}
                            title={`Projeção: +${projected - current}`}
                        ></div>
                    </div>
                );
            };

            return (
                <div className="min-h-screen p-4 md:p-8">
                    <div className="max-w-6xl mx-auto">
                        
                        {/* Cabeçalho */}
                        <div className="flex flex-col md:flex-row justify-between items-start md:items-center mb-8">
                            <div>
                                <h1 className="text-3xl font-bold text-slate-900 flex items-center gap-3">
                                    <i className="fas fa-bullseye text-blue-600"></i>
                                    Acompanhamento de Metas
                                </h1>
                                <p className="text-slate-500 mt-1">Visão geral, contratos e desempenho individual.</p>
                            </div>
                            <button 
                                onClick={() => setIsEditing(!isEditing)}
                                className="mt-4 md:mt-0 flex items-center gap-2 px-4 py-2 bg-white border border-slate-300 rounded-lg shadow-sm hover:bg-slate-100 transition-colors text-sm font-medium"
                            >
                                <i className={`fas ${isEditing ? 'fa-times' : 'fa-pen'}`}></i>
                                {isEditing ? 'Fechar Edição' : 'Atualizar Números'}
                            </button>
                        </div>

                        {/* Painel de Edição */}
                        {isEditing && (
                            <div className="bg-white p-6 rounded-xl shadow-sm border border-slate-200 mb-8 grid grid-cols-1 md:grid-cols-2 gap-6 transition-all">
                                <div>
                                    <h3 className="font-semibold text-slate-800 border-b pb-2 mb-4">Geral da Empresa</h3>
                                    <div className="space-y-3">
                                        <div className="flex justify-between items-center">
                                            <label className="text-sm">Processos Distribuídos</label>
                                            <input type="number" name="distribuidosGeral" value={data.distribuidosGeral} onChange={handleInputChange} className="w-20 border rounded p-1 text-center" />
                                        </div>
                                        <div className="flex justify-between items-center">
                                            <label className="text-sm">Em Produção</label>
                                            <input type="number" name="producaoGeral" value={data.producaoGeral} onChange={handleInputChange} className="w-20 border rounded p-1 text-center" />
                                        </div>
                                        <div className="flex justify-between items-center">
                                            <label className="text-sm">Em Onboarding</label>
                                            <input type="number" name="onboardingGeral" value={data.onboardingGeral} onChange={handleInputChange} className="w-20 border rounded p-1 text-center" />
                                        </div>
                                    </div>
                                </div>
                                <div>
                                    <h3 className="font-semibold text-slate-800 border-b pb-2 mb-4">Números do Mateus</h3>
                                    <div className="space-y-3">
                                        <div className="flex justify-between items-center">
                                            <label className="text-sm">Processos Distribuídos (Mateus)</label>
                                            <input type="number" name="distribuidosMateus" value={data.distribuidosMateus} onChange={handleInputChange} className="w-20 border rounded p-1 text-center" />
                                        </div>
                                        <div className="flex justify-between items-center">
                                            <label className="text-sm">Em Produção (Mateus)</label>
                                            <input type="number" name="producaoMateus" value={data.producaoMateus} onChange={handleInputChange} className="w-20 border rounded p-1 text-center" />
                                        </div>
                                        <div className="flex justify-between items-center">
                                            <label className="text-sm">Em Onboarding (Mateus)</label>
                                            <input type="number" name="onboardingMateus" value={data.onboardingMateus} onChange={handleInputChange} className="w-20 border rounded p-1 text-center" />
                                        </div>
                                    </div>
                                </div>
                            </div>
                        )}

                        {/* Cards de Metas */}
                        <div className="grid grid-cols-1 lg:grid-cols-3 gap-6">
                            
                            {/* Card 1: Processos Distribuídos */}
                            <div className="bg-white rounded-2xl shadow-sm border border-slate-200 p-6 relative overflow-hidden">
                                <div className="absolute top-0 left-0 w-1 h-full bg-blue-500"></div>
                                <div className="flex justify-between items-start mb-4">
                                    <div>
                                        <p className="text-sm font-semibold text-blue-600 uppercase tracking-wider">Meta Geral</p>
                                        <h2 className="text-xl font-bold text-slate-800">Processos Distribuídos</h2>
                                    </div>
                                    <div className="p-3 bg-blue-50 rounded-lg text-blue-500">
                                        <i className="fas fa-check-circle text-xl"></i>
                                    </div>
                                </div>
                                
                                <div className="flex items-end gap-2 mb-2">
                                    <span className="text-4xl font-extrabold text-slate-900">{data.distribuidosGeral}</span>
                                    <span className="text-slate-500 mb-1">/ {METAS.distribuidosGeral}</span>
                                </div>
                                
                                <ProgressBar current={data.distribuidosGeral} projected={projDistribuidosGeral} goal={METAS.distribuidosGeral} colorClass="bg-blue-500" />
                                
                                <div className="mt-4 bg-slate-50 rounded-lg p-3 text-sm">
                                    <div className="flex justify-between mb-1">
                                        <span className="text-slate-600">Faltam hoje:</span>
                                        <span className="font-semibold">{Math.max(0, METAS.distribuidosGeral - data.distribuidosGeral)}</span>
                                    </div>
                                </div>

                                <div className="mt-4 border-t border-slate-100 pt-4">
                                    <p className="text-sm font-semibold text-slate-700 mb-2 flex items-center gap-2">
                                        <i className="fas fa-arrow-trend-up text-emerald-500"></i> Suposição (com Onboarding)
                                    </p>
                                    <div className="grid grid-cols-2 gap-2 text-sm">
                                        <div className="bg-emerald-50 text-emerald-800 p-2 rounded">
                                            <span className="block text-xs opacity-80">Projeção Total</span>
                                            <span className="font-bold text-lg">{projDistribuidosGeral}</span>
                                        </div>
                                        <div className={`p-2 rounded ${projDistribuidosGeral >= METAS.distribuidosGeral ? 'bg-emerald-100 text-emerald-800' : 'bg-orange-50 text-orange-800'}`}>
                                            <span className="block text-xs opacity-80">Faltarão</span>
                                            <span className="font-bold text-lg">{Math.max(0, METAS.distribuidosGeral - projDistribuidosGeral)}</span>
                                        </div>
                                    </div>
                                </div>
                            </div>

                            {/* Card 2: Contratos Fechados Geral */}
                            <div className="bg-white rounded-2xl shadow-sm border border-slate-200 p-6 relative overflow-hidden">
                                <div className="absolute top-0 left-0 w-1 h-full bg-indigo-500"></div>
                                <div className="flex justify-between items-start mb-4">
                                    <div>
                                        <p className="text-sm font-semibold text-indigo-600 uppercase tracking-wider">Meta Geral</p>
                                        <h2 className="text-xl font-bold text-slate-800">Contratos Fechados</h2>
                                        <p className="text-xs text-slate-500">Distribuídos + Em Produção</p>
                                    </div>
                                    <div className="p-3 bg-indigo-50 rounded-lg text-indigo-500">
                                        <i className="fas fa-users text-xl"></i>
                                    </div>
                                </div>
                                
                                <div className="flex items-end gap-2 mb-2">
                                    <span className="text-4xl font-extrabold text-slate-900">{contratosFechadosGeral}</span>
                                    <span className="text-slate-500 mb-1">/ {METAS.contratosGeral}</span>
                                </div>
                                
                                <ProgressBar current={contratosFechadosGeral} projected={projContratosGeral} goal={METAS.contratosGeral} colorClass="bg-indigo-500" />
                                
                                <div className="mt-4 bg-slate-50 rounded-lg p-3 text-sm">
                                    <div className="flex justify-between mb-1">
                                        <span className="text-slate-600">Faltam hoje:</span>
                                        <span className="font-semibold">{Math.max(0, METAS.contratosGeral - contratosFechadosGeral)}</span>
                                    </div>
                                </div>

                                <div className="mt-4 border-t border-slate-100 pt-4">
                                    <p className="text-sm font-semibold text-slate-700 mb-2 flex items-center gap-2">
                                        <i className="fas fa-arrow-trend-up text-emerald-500"></i> Suposição (com Onboarding)
                                    </p>
                                    <div className="grid grid-cols-2 gap-2 text-sm">
                                        <div className="bg-emerald-50 text-emerald-800 p-2 rounded">
                                            <span className="block text-xs opacity-80">Projeção Total</span>
                                            <span className="font-bold text-lg">{projContratosGeral}</span>
                                        </div>
                                        <div className={`p-2 rounded ${projContratosGeral >= METAS.contratosGeral ? 'bg-emerald-100 text-emerald-700' : 'bg-orange-50 text-orange-800'}`}>
                                            <span className="block text-xs opacity-80">Faltarão</span>
                                            <span className="font-bold text-lg">
                                                {projContratosGeral >= METAS.contratosGeral ? 'BATEU!' : Math.max(0, METAS.contratosGeral - projContratosGeral)}
                                            </span>
                                        </div>
                                    </div>
                                </div>
                            </div>

                            {/* Card 3: Meta Mateus */}
                            <div className="bg-slate-900 rounded-2xl shadow-lg border border-slate-800 p-6 relative overflow-hidden text-white">
                                <div className="absolute top-0 right-0 w-32 h-32 bg-emerald-500 rounded-full blur-3xl opacity-20 -mr-10 -mt-10"></div>
                                
                                <div className="flex justify-between items-start mb-4 relative z-10">
                                    <div>
                                        <p className="text-sm font-semibold text-emerald-400 uppercase tracking-wider">Meta Individual</p>
                                        <h2 className="text-xl font-bold">Mateus</h2>
                                        <p className="text-xs text-slate-400">Distribuídos + Produção</p>
                                    </div>
                                    <div className="p-3 bg-slate-800 rounded-lg border border-slate-700 text-emerald-400">
                                        <i className="fas fa-bullseye text-xl"></i>
                                    </div>
                                </div>
                                
                                <div className="flex items-end gap-2 mb-2 relative z-10">
                                    <span className="text-4xl font-extrabold">{contratosFechadosMateus}</span>
                                    <span className="text-slate-400 mb-1">/ {METAS.contratosMateus}</span>
                                </div>
                                
                                <div className="w-full bg-slate-800 rounded-full h-3.5 mt-2 overflow-hidden flex relative z-10">
                                    <div 
                                        className="h-3.5 bg-emerald-500 transition-all duration-500 shadow-[0_0_10px_rgba(16,185,129,0.5)]" 
                                        style={{ width: `${Math.min((contratosFechadosMateus / METAS.contratosMateus) * 100, 100)}%` }}
                                    ></div>
                                    <div 
                                        className="h-3.5 bg-emerald-500 opacity-30 transition-all duration-500" 
                                        style={{ width: `${Math.min(((projContratosMateus - contratosFechadosMateus) / METAS.contratosMateus) * 100, 100 - Math.min((contratosFechadosMateus / METAS.contratosMateus) * 100, 100))}%` }}
                                    ></div>
                                </div>
                                
                                <div className="mt-4 bg-slate-800/50 rounded-lg p-3 text-sm relative z-10 border border-slate-700/50">
                                    <div className="flex justify-between mb-1">
                                        <span className="text-slate-300">Faltam hoje:</span>
                                        <span className="font-bold text-white">{Math.max(0, METAS.contratosMateus - contratosFechadosMateus)}</span>
                                    </div>
                                </div>

                                <div className="mt-4 border-t border-slate-700 pt-4 relative z-10">
                                    <p className="text-sm font-semibold text-emerald-400 mb-2 flex items-center gap-2">
                                        <i className="fas fa-arrow-trend-up"></i> Suposição (com Onboarding)
                                    </p>
                                    <div className="grid grid-cols-2 gap-2 text-sm">
                                        <div className="bg-slate-800 p-2 rounded border border-slate-700">
                                            <span className="block text-xs text-slate-400">Projeção</span>
                                            <span className="font-bold text-lg">{projContratosMateus}</span>
                                        </div>
                                        <div className={`p-2 rounded border ${projContratosMateus >= METAS.contratosMateus ? 'bg-emerald-900/50 border-emerald-500/30 text-emerald-400' : 'bg-slate-800 border-slate-700 text-amber-400'}`}>
                                            <span className="block text-xs opacity-80">Faltarão</span>
                                            <span className="font-bold text-lg">
                                                {projContratosMateus >= METAS.contratosMateus ? 'BATEU!' : Math.max(0, METAS.contratosMateus - projContratosMateus)}
                                            </span>
                                        </div>
                                    </div>
                                </div>
                            </div>

                        </div>

                        <div className="mt-8 flex items-start gap-3 bg-blue-50 p-4 rounded-xl border border-blue-100">
                            <i className="fas fa-exclamation-circle text-blue-500 text-xl flex-shrink-0 mt-0.5"></i>
                            <div className="text-sm text-slate-700">
                                <p><strong>Como a projeção funciona?</strong> A área "Suposição" soma o número do cenário atual com a quantidade de clientes que estão na fase de <em>Onboarding</em>. Ela simula o cenário ideal onde todos os clientes em Onboarding enviam os documentos e os contratos são assinados/distribuídos com sucesso.</p>
                                <p className="mt-2 text-xs text-slate-500">Para garantir o correto acompanhamento antes do recesso (de 19/12 a 20/01), atualiza estes números regularmente clicando no botão "Atualizar Números" no topo.</p>
                            </div>
                        </div>

                    </div>
                </div>
            );
        }

        const root = ReactDOM.createRoot(document.getElementById('root'));
        root.render(<DashboardMetas />);
    </script>
</body>
</html>
