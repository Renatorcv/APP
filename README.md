<!DOCTYPE html>
<html lang="pt-br">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Check List Pro - Vistoria & Rastreamento</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <script src="https://unpkg.com/lucide@latest"></script>
    <script src="https://cdn.jsdelivr.net/npm/qrcode@1.5.1/build/qrcode.min.js"></script>
    <script src="https://maps.googleapis.com/maps/api/js?key=YOUR_API_KEY&libraries=places"></script>
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Inter:wght@300;400;500;600;700&display=swap');
        
        body {
            font-family: 'Inter', sans-serif;
            background-color: #f3f4f6;
        }
        
        .dark-gradient {
            background: linear-gradient(135deg, #1A237E 0%, #0d123f 100%);
        }
        
        .highlight-gradient {
            background: linear-gradient(90deg, #00BFA5 0%, #00897B 100%);
        }
        
        .map-container {
            height: 500px;
            border-radius: 12px;
            overflow: hidden;
            box-shadow: 0 10px 15px -3px rgba(0, 0, 0, 0.1);
        }
        
        .speech-bubble {
            position: relative;
            background: #ffffff;
            border-radius: 12px;
            padding: 16px;
            box-shadow: 0 4px 6px -1px rgba(0, 0, 0, 0.1);
        }
        
        .speech-bubble:after {
            content: '';
            position: absolute;
            bottom: -10px;
            left: 20px;
            border-width: 10px 10px 0;
            border-style: solid;
            border-color: #ffffff transparent;
        }
        
        @media (max-width: 768px) {
            .map-container {
                height: 350px;
            }
        }

        /* Estilo para o item ativo da barra lateral */
        .sidebar-item.active {
            @apply highlight-gradient;
        }

        /* Efeito de esqueleto para o QR Code */
        .skeleton {
            animation: pulse 2s cubic-bezier(0.4, 0, 0.6, 1) infinite;
        }
        
        @keyframes pulse {
            0%, 100% {
                opacity: 1;
            }
            50% {
                opacity: .5;
            }
        }
    </style>
</head>
<body class="bg-gray-100">
    <div class="min-h-screen flex flex-col md:flex-row">
        <div id="sidebar" class="w-64 dark-gradient text-white fixed h-full hidden md:block">
            <div class="p-6">
                <div class="flex items-center space-x-3 mb-10">
                    <i data-lucide="truck" class="w-8 h-8 text-teal-400"></i>
                    <h1 class="text-xl font-bold">Check List Pro</h1>
                </div>
                <nav>
                    <ul class="space-y-3">
                        <li>
                            <a href="#" class="sidebar-item active flex items-center space-x-3 p-3 rounded-lg text-white">
                                <i data-lucide="layout-dashboard" class="w-5 h-5"></i>
                                <span>Dashboard</span>
                            </a>
                        </li>
                        <li>
                            <a href="#" class="sidebar-item flex items-center space-x-3 p-3 rounded-lg hover:bg-blue-900 text-blue-100 hover:text-white transition">
                                <i data-lucide="clipboard-list" class="w-5 h-5"></i>
                                <span>Novo Checklist</span>
                            </a>
                        </li>
                        <li>
                            <a href="#" class="sidebar-item flex items-center space-x-3 p-3 rounded-lg hover:bg-blue-900 text-blue-100 hover:text-white transition">
                                <i data-lucide="history" class="w-5 h-5"></i>
                                <span>Histórico</span>
                            </a>
                        </li>
                        <li>
                            <a href="#" class="sidebar-item flex items-center space-x-3 p-3 rounded-lg hover:bg-blue-900 text-blue-100 hover:text-white transition">
                                <i data-lucide="settings" class="w-5 h-5"></i>
                                <span>Configurações</span>
                            </a>
                        </li>
                    </ul>
                </nav>
            </div>
        </div>
        
        <header class="md:hidden dark-gradient text-white p-4 flex justify-between items-center">
            <button id="mobile-menu-button" class="p-2 rounded-md hover:bg-blue-900">
                <i data-lucide="menu" class="w-6 h-6"></i>
            </button>
            <div class="flex items-center space-x-2">
                <i data-lucide="truck" class="w-6 h-6 text-teal-400"></i>
                <h1 class="text-lg font-bold">Check List Pro</h1>
            </div>
            <div class="w-6"></div> </header>
        
        <div id="mobile-sidebar" class="fixed inset-0 bg-black bg-opacity-50 z-50 hidden md:hidden">
            <div class="w-64 h-full dark-gradient text-white">
                <div class="p-4 flex justify-between items-center border-b border-blue-800">
                    <div class="flex items-center space-x-2">
                        <i data-lucide="truck" class="w-6 h-6 text-teal-400"></i>
                        <h1 class="text-lg font-bold">Check List Pro</h1>
                    </div>
                    <button id="close-mobile-menu" class="p-1 rounded-md hover:bg-blue-900">
                        <i data-lucide="x" class="w-5 h-5"></i>
                    </button>
                </div>
                <nav class="p-4">
                    <ul class="space-y-3">
                        <li>
                            <a href="#" class="sidebar-item active flex items-center space-x-3 p-3 rounded-lg text-white">
                                <i data-lucide="layout-dashboard" class="w-5 h-5"></i>
                                <span>Dashboard</span>
                            </a>
                        </li>
                        <li>
                            <a href="#" class="sidebar-item flex items-center space-x-3 p-3 rounded-lg hover:bg-blue-900 text-blue-100 hover:text-white transition">
                                <i data-lucide="clipboard-list" class="w-5 h-5"></i>
                                <span>Novo Checklist</span>
                            </a>
                        </li>
                        <li>
                            <a href="#" class="sidebar-item flex items-center space-x-3 p-3 rounded-lg hover:bg-blue-900 text-blue-100 hover:text-white transition">
                                <i data-lucide="history" class="w-5 h-5"></i>
                                <span>Histórico</span>
                            </a>
                        </li>
                        <li>
                            <a href="#" class="sidebar-item flex items-center space-x-3 p-3 rounded-lg hover:bg-blue-900 text-blue-100 hover:text-white transition">
                                <i data-lucide="settings" class="w-5 h-5"></i>
                                <span>Configurações</span>
                            </a>
                        </li>
                    </ul>
                </nav>
            </div>
        </div>

        <main class="flex-1 md:ml-64 p-6" id="main-content">
            <div id="dashboard-content" class="content-section">
                <div class="flex justify-between items-center mb-8">
                    <h2 class="text-2xl font-bold text-gray-800">Dashboard</h2>
                    <div class="flex items-center space-x-4">
                        <div class="relative hidden md:block">
                            <input type="text" placeholder="Search..." class="pl-10 pr-4 py-2 rounded-lg border border-gray-300 focus:outline-none focus:ring-2 focus:ring-teal-500 focus:border-transparent">
                            <i data-lucide="search" class="absolute left-3 top-2.5 text-gray-400"></i>
                        </div>
                        <div class="flex items-center space-x-2">
                            <div class="w-8 h-8 rounded-full bg-teal-500 flex items-center justify-center text-white font-semibold">JD</div>
                            <span class="hidden md:inline text-gray-700">John Driver</span>
                        </div>
                    </div>
                </div>
                
                <div class="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-4 gap-6 mb-8">
                    <div class="bg-white rounded-xl shadow-md p-6 flex items-center space-x-4">
                        <div class="p-3 rounded-full bg-blue-100 text-blue-600">
                            <i data-lucide="clipboard-list" class="w-6 h-6"></i>
                        </div>
                        <div>
                            <p class="text-gray-500 text-sm">Checklists de Hoje</p>
                            <p class="text-2xl font-bold">12</p>
                        </div>
                    </div>
                    <div class="bg-white rounded-xl shadow-md p-6 flex items-center space-x-4">
                        <div class="p-3 rounded-full bg-green-100 text-green-600">
                            <i data-lucide="map-pin" class="w-6 h-6"></i>
                        </div>
                        <div>
                            <p class="text-gray-500 text-sm">Viagens Ativas</p>
                            <p class="text-2xl font-bold">3</p>
                        </div>
                    </div>
                    <div class="bg-white rounded-xl shadow-md p-6 flex items-center space-x-4">
                        <div class="p-3 rounded-full bg-yellow-100 text-yellow-600">
                            <i data-lucide="clock" class="w-6 h-6"></i>
                        </div>
                        <div>
                            <p class="text-gray-500 text-sm">Tempo Médio</p>
                            <p class="text-2xl font-bold">4h 22m</p>
                        </div>
                    </div>
                    <div class="bg-white rounded-xl shadow-md p-6 flex items-center space-x-4">
                        <div class="p-3 rounded-full bg-purple-100 text-purple-600">
                            <i data-lucide="route" class="w-6 h-6"></i>
                        </div>
                        <div>
                            <p class="text-gray-500 text-sm">Distância Média</p>
                            <p class="text-2xl font-bold">142 km</p>
                        </div>
                    </div>
                </div>

                <div class="bg-white rounded-xl shadow-md p-6">
                    <div class="flex justify-between items-center mb-6">
                        <h3 class="text-xl font-semibold text-gray-800">Checklists Recentes</h3>
                        <button class="text-teal-600 hover:text-teal-700 flex items-center space-x-1">
                            <i data-lucide="list" class="w-4 h-4"></i>
                            <span>Ver Todos</span>
                        </button>
                    </div>
                    
                    <div class="overflow-x-auto">
                        <table class="min-w-full divide-y divide-gray-200">
                            <thead class="bg-gray-50">
                                <tr>
                                    <th class="px-6 py-3 text-left text-xs font-medium text-gray-500 uppercase tracking-wider">Veículo</th>
                                    <th class="px-6 py-3 text-left text-xs font-medium text-gray-500 uppercase tracking-wider">Data</th>
                                    <th class="px-6 py-3 text-left text-xs font-medium text-gray-500 uppercase tracking-wider">Status</th>
                                    <th class="px-6 py-3 text-left text-xs font-medium text-gray-500 uppercase tracking-wider">Detalhes da Viagem</th>
                                    <th class="px-6 py-3 text-left text-xs font-medium text-gray-500 uppercase tracking-wider">Ações</th>
                                </tr>
                            </thead>
                            <tbody class="bg-white divide-y divide-gray-200">
                                <tr>
                                    <td class="px-6 py-4 whitespace-nowrap">
                                        <div class="flex items-center">
                                            <i data-lucide="truck" class="w-5 h-5 text-blue-600 mr-2"></i>
                                            <div>
                                                <div class="font-medium text-gray-900">Volvo FH16</div>
                                                <div class="text-gray-500">ABC-1234</div>
                                            </div>
                                        </div>
                                    </td>
                                    <td class="px-6 py-4 whitespace-nowrap">
                                        <div class="text-gray-900">Hoje, 08:45</div>
                                    </td>
                                    <td class="px-6 py-4 whitespace-nowrap">
                                        <span class="px-2 inline-flex text-xs leading-5 font-semibold rounded-full bg-green-100 text-green-800">Concluído</span>
                                    </td>
                                    <td class="px-6 py-4 whitespace-nowrap">
                                        <div class="text-gray-900">342 km • 5h 12m</div>
                                    </td>
                                    <td class="px-6 py-4 whitespace-nowrap text-right text-sm font-medium">
                                        <button class="text-teal-600 hover:text-teal-900 mr-3">Ver</button>
                                        <button class="text-gray-600 hover:text-gray-900">Compartilhar</button>
                                    </td>
                                </tr>
                                <tr>
                                    <td class="px-6 py-4 whitespace-nowrap">
                                        <div class="flex items-center">
                                            <i data-lucide="bus" class="w-5 h-5 text-green-600 mr-2"></i>
                                            <div>
                                                <div class="font-medium text-gray-900">Mercedes Tourismo</div>
                                                <div class="text-gray-500">XYZ-9876</div>
                                            </div>
                                        </div>
                                    </td>
                                    <td class="px-6 py-4 whitespace-nowrap">
                                        <div class="text-gray-900">Ontem, 14:30</div>
                                    </td>
                                    <td class="px-6 py-4 whitespace-nowrap">
                                        <span class="px-2 inline-flex text-xs leading-5 font-semibold rounded-full bg-green-100 text-green-800">Concluído</span>
                                    </td>
                                    <td class="px-6 py-4 whitespace-nowrap">
                                        <div class="text-gray-900">278 km • 4h 45m</div>
                                    </td>
                                    <td class="px-6 py-4 whitespace-nowrap text-right text-sm font-medium">
                                        <button class="text-teal-600 hover:text-teal-900 mr-3">Ver</button>
                                        <button class="text-gray-600 hover:text-gray-900">Compartilhar</button>
                                    </td>
                                </tr>
                                <tr>
                                    <td class="px-6 py-4 whitespace-nowrap">
                                        <div class="flex items-center">
                                            <i data-lucide="truck" class="w-5 h-5 text-blue-600 mr-2"></i>
                                            <div>
                                                <div class="font-medium text-gray-900">Scania R450</div>
                                                <div class="text-gray-500">DEF-5678</div>
                                            </div>
                                        </div>
                                    </td>
                                    <td class="px-6 py-4 whitespace-nowrap">
                                        <div class="text-gray-900">Ontem, 09:15</div>
                                    </td>
                                    <td class="px-6 py-4 whitespace-nowrap">
                                        <span class="px-2 inline-flex text-xs leading-5 font-semibold rounded-full bg-yellow-100 text-yellow-800">Em Andamento</span>
                                    </td>
                                    <td class="px-6 py-4 whitespace-nowrap">
                                        <div class="text-gray-900">156 km • 2h 38m</div>
                                    </td>
                                    <td class="px-6 py-4 whitespace-nowrap text-right text-sm font-medium">
                                        <button class="text-teal-600 hover:text-teal-900 mr-3">Ver</button>
                                        <button class="text-gray-600 hover:text-gray-900">Compartilhar</button>
                                    </td>
                                </tr>
                            </tbody>
                        </table>
                    </div>
                </div>
            </div>

            <div id="new-checklist-content" class="content-section hidden">
                <h2 class="text-2xl font-bold text-gray-800 mb-8">Novo Checklist</h2>
                <div class="grid grid-cols-1 lg:grid-cols-3 gap-6 mb-8">
                    <div class="lg:col-span-2 bg-white rounded-xl shadow-md p-6">
                        <div class="flex justify-between items-center mb-6">
                            <h3 class="text-xl font-semibold text-gray-800">Vistoria do Veículo</h3>
                            <button class="highlight-gradient text-white px-4 py-2 rounded-lg flex items-center space-x-2">
                                <i data-lucide="save" class="w-4 h-4"></i>
                                <span>Salvar Rascunho</span>
                            </button>
                        </div>
                        
                        <div class="space-y-6">
                            <div>
                                <label class="block text-gray-700 mb-2">Tipo de Veículo</label>
                                <select id="vehicle-type" class="w-full px-4 py-2 border border-gray-300 rounded-lg focus:outline-none focus:ring-2 focus:ring-teal-500 focus:border-transparent">
                                    <option>Selecionar tipo de veículo</option>
                                    <option>Caminhão</option>
                                    <option>Ônibus</option>
                                    <option>Van</option>
                                    <option>Outro</option>
                                </select>
                            </div>
                            
                            <div>
                                <label class="block text-gray-700 mb-2">Placa</label>
                                <input id="license-plate" type="text" class="w-full px-4 py-2 border border-gray-300 rounded-lg focus:outline-none focus:ring-2 focus:ring-teal-500 focus:border-transparent" placeholder="ABC-1234">
                            </div>
                            
                            <div class="grid grid-cols-1 md:grid-cols-2 gap-4">
                                <div>
                                    <label class="block text-gray-700 mb-2">Odômetro (km)</label>
                                    <input id="odometer" type="number" class="w-full px-4 py-2 border border-gray-300 rounded-lg focus:outline-none focus:ring-2 focus:ring-teal-500 focus:border-transparent" placeholder="12345">
                                </div>
                                <div>
                                    <label class="block text-gray-700 mb-2">Nível de Combustível</label>
                                    <select id="fuel-level" class="w-full px-4 py-2 border border-gray-300 rounded-lg focus:outline-none focus:ring-2 focus:ring-teal-500 focus:border-transparent">
                                        <option>Selecionar nível de combustível</option>
                                        <option>Cheio</option>
                                        <option>3/4</option>
                                        <option>1/2</option>
                                        <option>1/4</option>
                                        <option>Vazio</option>
                                    </select>
                                </div>
                            </div>
                            
                            <div>
                                <label class="block text-gray-700 mb-2">Itens de Vistoria</label>
                                <div class="space-y-3">
                                    <div class="flex items-center justify-between p-3 bg-gray-50 rounded-lg">
                                        <span>Pneus e pressão</span>
                                        <div class="flex space-x-2">
                                            <button class="px-3 py-1 bg-green-100 text-green-800 rounded-full text-sm">OK</button>
                                            <button class="px-3 py-1 bg-red-100 text-red-800 rounded-full text-sm">Problema</button>
                                        </div>
                                    </div>
                                    <div class="flex items-center justify-between p-3 bg-gray-50 rounded-lg">
                                        <span>Luzes e sinais</span>
                                        <div class="flex space-x-2">
                                            <button class="px-3 py-1 bg-green-100 text-green-800 rounded-full text-sm">OK</button>
                                            <button class="px-3 py-1 bg-red-100 text-red-800 rounded-full text-sm">Problema</button>
                                        </div>
                                    </div>
                                    <div class="flex items-center justify-between p-3 bg-gray-50 rounded-lg">
                                        <span>Sistema de freios</span>
                                        <div class="flex space-x-2">
                                            <button class="px-3 py-1 bg-green-100 text-green-800 rounded-full text-sm">OK</button>
                                            <button class="px-3 py-1 bg-red-100 text-red-800 rounded-full text-sm">Problema</button>
                                        </div>
                                    </div>
                                    <div class="flex items-center justify-between p-3 bg-gray-50 rounded-lg">
                                        <span>Níveis de fluído</span>
                                        <div class="flex space-x-2">
                                            <button class="px-3 py-1 bg-green-100 text-green-800 rounded-full text-sm">OK</button>
                                            <button class="px-3 py-1 bg-red-100 text-red-800 rounded-full text-sm">Problema</button>
                                        </div>
                                    </div>
                                </div>
                            </div>
                            
                            <div>
                                <label class="block text-gray-700 mb-2">Notas</label>
                                <textarea id="checklist-notes" class="w-full px-4 py-2 border border-gray-300 rounded-lg focus:outline-none focus:ring-2 focus:ring-teal-500 focus:border-transparent" rows="3" placeholder="Notas adicionais..."></textarea>
                            </div>
                            
                            <div>
                                <label class="block text-gray-700 mb-2">Carregar Fotos</label>
                                <div class="border-2 border-dashed border-gray-300 rounded-lg p-6 text-center">
                                    <i data-lucide="upload" class="w-8 h-8 mx-auto text-gray-400 mb-2"></i>
                                    <p class="text-gray-500">Arraste e solte fotos aqui ou clique para procurar</p>
                                </div>
                            </div>
                        </div>
                    </div>
                    
                    <div class="space-y-6">
                        <div class="bg-white rounded-xl shadow-md p-6">
                            <h3 class="text-xl font-semibold text-gray-800 mb-4">Destino da Viagem</h3>
                            
                            <div class="space-y-4">
                                <div>
                                    <label class="block text-gray-700 mb-2">Endereço de Destino</label>
                                    <div class="relative">
                                        <input id="destination-input" type="text" class="w-full px-4 py-2 border border-gray-300 rounded-lg focus:outline-none focus:ring-2 focus:ring-teal-500 focus:border-transparent" placeholder="Digite o destino">
                                        <i data-lucide="map-pin" class="absolute right-3 top-2.5 text-gray-400"></i>
                                    </div>
                                </div>
                                
                                <div class="grid grid-cols-2 gap-4">
                                    <div>
                                        <label class="block text-gray-700 mb-2">Distância Estimada</label>
                                        <div class="px-4 py-2 bg-gray-50 rounded-lg">
                                            <p class="text-gray-800" id="estimated-distance">-- km</p>
                                        </div>
                                    </div>
                                    <div>
                                        <label class="block text-gray-700 mb-2">Tempo Estimado</label>
                                        <div class="px-4 py-2 bg-gray-50 rounded-lg">
                                            <p class="text-gray-800" id="estimated-time">-- h -- m</p>
                                        </div>
                                    </div>
                                </div>
                                
                                <button id="start-trip-btn" class="w-full highlight-gradient text-white px-4 py-3 rounded-lg flex items-center justify-center space-x-2 font-medium">
                                    <i data-lucide="check-circle" class="w-5 h-5"></i>
                                    <span>Finalizar Checklist & Iniciar Viagem</span>
                                </button>
                            </div>
                        </div>
                        
                        <div class="bg-white rounded-xl shadow-md p-6">
                            <div class="flex items-center space-x-3 mb-4">
                                <div class="p-2 rounded-full bg-blue-100 text-blue-600">
                                    <i data-lucide="mic" class="w-5 h-5"></i>
                                </div>
                                <h3 class="text-xl font-semibold text-gray-800">Anúncio por Voz</h3>
                            </div>
                            
                            <div class="speech-bubble">
                                <p class="text-gray-700" id="speech-text">Pressione o botão para iniciar a viagem e ouvir o anúncio.</p>
                            </div>
                        </div>
                    </div>
                </div>
            </div>

            <div id="trip-history-content" class="content-section hidden">
                <h2 class="text-2xl font-bold text-gray-800 mb-8">Histórico de Viagens</h2>

                <div class="bg-white rounded-xl shadow-md p-6 mb-8">
                    <div class="flex justify-between items-center mb-6">
                        <h3 class="text-xl font-semibold text-gray-800">Detalhes da Viagem e QR Code</h3>
                    </div>
                    
                    <div class="text-center">
                        <div id="qrcode-container" class="mx-auto w-40 h-40 mb-4 flex items-center justify-center bg-gray-100 rounded-lg">
                            <p class="text-gray-400 text-sm">Selecione uma viagem para gerar o QR Code</p>
                        </div>
                        <p class="text-gray-600 text-sm mb-4">Escaneie este código para visualizar o relatório de vistoria.</p>
                        <button id="download-qr-btn" class="text-teal-600 hover:text-teal-700 flex items-center justify-center space-x-1 mx-auto" style="display: none;">
                            <i data-lucide="download" class="w-4 h-4"></i>
                            <span>Baixar QR Code</span>
                        </button>
                    </div>
                </div>

                <div class="bg-white rounded-xl shadow-md p-6 mb-8">
                    <div class="flex justify-between items-center mb-6">
                        <h3 class="text-xl font-semibold text-gray-800">Rastreamento da Viagem</h3>
                        <div class="flex space-x-2">
                            <button class="px-4 py-2 border border-gray-300 rounded-lg flex items-center space-x-2">
                                <i data-lucide="refresh-cw" class="w-4 h-4"></i>
                                <span>Atualizar</span>
                            </button>
                            <button class="px-4 py-2 bg-gray-800 text-white rounded-lg flex items-center space-x-2">
                                <i data-lucide="share-2" class="w-4 h-4"></i>
                                <span>Compartilhar</span>
                            </button>
                        </div>
                    </div>
                    
                    <div class="map-container" id="map">
                        </div>
                    
                    <div class="mt-6 grid grid-cols-1 md:grid-cols-3 gap-4">
                        <div class="bg-gray-50 p-4 rounded-lg">
                            <div class="flex items-center space-x-3 mb-2">
                                <i data-lucide="map-pin" class="w-5 h-5 text-blue-600"></i>
                                <h4 class="font-medium text-gray-800">Localização Atual</h4>
                            </div>
                            <p class="text-gray-600" id="current-location">Não rastreando</p>
                        </div>
                        <div class="bg-gray-50 p-4 rounded-lg">
                            <div class="flex items-center space-x-3 mb-2">
                                <i data-lucide="clock" class="w-5 h-5 text-yellow-600"></i>
                                <h4 class="font-medium text-gray-800">Duração da Viagem</h4>
                            </div>
                            <p class="text-gray-600" id="trip-duration">-- h -- m</p>
                        </div>
                        <div class="bg-gray-50 p-4 rounded-lg">
                            <div class="flex items-center space-x-3 mb-2">
                                <i data-lucide="route" class="w-5 h-5 text-purple-600"></i>
                                <h4 class="font-medium text-gray-800">Distância Percorrida</h4>
                            </div>
                            <p class="text-gray-600" id="distance-traveled">-- km</p>
                        </div>
                    </div>
                </div>
            </div>

            <div id="settings-content" class="content-section hidden">
                <h2 class="text-2xl font-bold text-gray-800">Configurações</h2>
                <div class="mt-8 bg-white p-6 rounded-xl shadow-md">
                    <p class="text-gray-700">Conteúdo de configurações...</p>
                </div>
            </div>

        </main>
    </div>

    <script>
        // Inicializa os ícones Lucide
        lucide.createIcons();

        // ------------------------------------
        // Lógica do Menu Mobile
        // ------------------------------------
        const mobileMenuButton = document.getElementById('mobile-menu-button');
        const closeMobileMenuButton = document.getElementById('close-mobile-menu');
        const mobileSidebar = document.getElementById('mobile-sidebar');

        mobileMenuButton.addEventListener('click', () => {
            mobileSidebar.classList.remove('hidden');
        });

        closeMobileMenuButton.addEventListener('click', () => {
            mobileSidebar.classList.add('hidden');
        });

        // ------------------------------------
        // Lógica de Navegação da Aplicação (Simulada)
        // ------------------------------------
        const sidebarItems = document.querySelectorAll('.sidebar-item');
        const contentSections = document.querySelectorAll('.content-section');

        function hideAllSections() {
            contentSections.forEach(section => {
                section.classList.add('hidden');
            });
        }

        function setActiveItem(clickedItem) {
            sidebarItems.forEach(item => {
                item.classList.remove('active');
                item.classList.remove('highlight-gradient');
                item.classList.add('hover:bg-blue-900', 'text-blue-100', 'hover:text-white');
            });
            clickedItem.classList.add('active', 'highlight-gradient');
            clickedItem.classList.remove('hover:bg-blue-900', 'text-blue-100', 'hover:text-white');
        }

        sidebarItems.forEach(item => {
            item.addEventListener('click', (e) => {
                e.preventDefault();
                setActiveItem(item);
                
                const linkText = item.querySelector('span').textContent.trim();
                hideAllSections();

                if (linkText === 'Dashboard') {
                    document.getElementById('dashboard-content').classList.remove('hidden');
                } else if (linkText === 'Novo Checklist') {
                    document.getElementById('new-checklist-content').classList.remove('hidden');
                } else if (linkText === 'Histórico') {
                    document.getElementById('trip-history-content').classList.remove('hidden');
                    // Exemplo de como um QR Code seria gerado com um ID fictício
                    generateQrCode('checklist-id-12345');
                } else if (linkText === 'Configurações') {
                    document.getElementById('settings-content').classList.remove('hidden');
                }
            });
        });

        // ------------------------------------
        // Lógica de Rastreamento e Google Maps
        // ------------------------------------
        let map;
        let originMarker, destinationMarker;
        let directionsService, directionsRenderer;
        let watchId;
        const speechTextElement = document.getElementById('speech-text');

        function initMap() {
            const mapElement = document.getElementById('map');
            if (mapElement) {
                map = new google.maps.Map(mapElement, {
                    center: { lat: -23.5505, lng: -46.6333 }, // São Paulo, Brasil
                    zoom: 12
                });
                directionsService = new google.maps.DirectionsService();
                directionsRenderer = new google.maps.DirectionsRenderer();
                directionsRenderer.setMap(map);
            }

            const input = document.getElementById('destination-input');
            if (input) {
                const autocomplete = new google.maps.places.Autocomplete(input);
                autocomplete.addListener('place_changed', () => {
                    const place = autocomplete.getPlace();
                    if (!place.geometry) {
                        window.alert("No details available for input: '" + place.name + "'");
                        return;
                    }
                    if (destinationMarker) {
                        destinationMarker.setMap(null);
                    }
                    destinationMarker = new google.maps.Marker({
                        position: place.geometry.location,
                        map: map,
                        title: place.name
                    });
                    map.setCenter(place.geometry.location);
                });
            }
        }

        // Lógica de rastreamento simulada
        function startTracking() {
            if (navigator.geolocation) {
                speechTextElement.textContent = "Iniciando a viagem...";
                speak('Iniciando a viagem.');
                
                // Exemplo de rastreamento GPS
                watchId = navigator.geolocation.watchPosition((position) => {
                    const lat = position.coords.latitude;
                    const lng = position.coords.longitude;
                    document.getElementById('current-location').textContent = `Lat: ${lat.toFixed(4)}, Lng: ${lng.toFixed(4)}`;

                    // Simula a atualização do mapa
                    if (!originMarker) {
                        originMarker = new google.maps.Marker({
                            position: { lat, lng },
                            map: map,
                            title: 'Origem'
                        });
                    }

                    // Lógica para desenhar o trajeto e calcular distância...
                }, (error) => {
                    console.error("Geolocation error: ", error);
                    speechTextElement.textContent = "Erro ao obter localização. Verifique as permissões.";
                });
            } else {
                speechTextElement.textContent = "Geolocation não é suportado por este navegador.";
            }
        }

        document.getElementById('start-trip-btn').addEventListener('click', () => {
            const destination = document.getElementById('destination-input').value;
            if (destination) {
                startTracking();
            } else {
                alert('Por favor, insira um destino para a viagem.');
            }
        });

        // ------------------------------------
        // Lógica de Anúncio por Voz
        // ------------------------------------
        function speak(text) {
            const utterance = new SpeechSynthesisUtterance(text);
            utterance.lang = 'pt-BR';
            window.speechSynthesis.speak(utterance);
        }

        // ------------------------------------
        // Lógica do QR Code
        // ------------------------------------
        function generateQrCode(checklistId) {
            const qrContainer = document.getElementById('qrcode-container');
            const downloadBtn = document.getElementById('download-qr-btn');
            qrContainer.innerHTML = ''; // Limpa o conteúdo
            downloadBtn.style.display = 'block';

            const qrData = `https://checklist-pro.app/view?id=${checklistId}`; // URL de exemplo
            
            new QRCode(qrContainer, {
                text: qrData,
                width: 160,
                height: 160,
                colorDark : "#1A237E",
                colorLight : "#f3f4f6",
                correctLevel : QRCode.CorrectLevel.H
            });
            
            downloadBtn.onclick = () => {
                const qrCanvas = qrContainer.querySelector('canvas');
                if (qrCanvas) {
                    const link = document.createElement('a');
                    link.href = qrCanvas.toDataURL('image/png');
                    link.download = `checklist-qrcode-${checklistId}.png`;
                    link.click();
                }
            };
        }
        
        // Chamada da função de inicialização do mapa ao carregar a página
        window.addEventListener('load', initMap);

    </script>
</body>
</html>
