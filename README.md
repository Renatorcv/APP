<!DOCTYPE html>
<html lang="pt-BR">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Checklist de Vistoria Veicular</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/css/all.min.css">
    <style>
        .photo-preview {
            width: 60px;
            height: 60px;
            object-fit: cover;
            border-radius: 4px;
            display: none;
        }
        .add-row-btn:hover {
            transform: scale(1.05);
        }
        .print-only {
            display: none;
        }
        @media print {
            .no-print {
                display: none;
            }
            .print-only {
                display: block;
            }
            body {
                background-color: white;
                color: black;
            }
            .container {
                box-shadow: none;
                border: none;
            }
        }
    </style>
</head>
<body class="bg-blue-50 min-h-screen">
    <div class="container mx-auto px-4 py-8 max-w-5xl">
        <!-- Header -->
        <header class="bg-blue-600 text-white rounded-lg shadow-md mb-8">
            <div class="p-6 text-center">
                <h1 class="text-3xl font-bold">LISTA DE VERIFICAÇÃO VEICULAR</h1>
                <p class="text-blue-100 mt-2">Preencha todos os campos necessários</p>
            </div>
        </header>

        <!-- Main Form -->
        <section class="bg-white rounded-xl shadow-lg overflow-hidden mb-8">
            <!-- Vehicle Info -->
            <div class="p-6 border-b border-gray-200">
                <h2 class="text-xl font-semibold text-blue-700 mb-4">
                    <i class="fas fa-car mr-2"></i>INFORMAÇÕES DO VEÍCULO
                </h2>
                
                <div class="grid grid-cols-1 md:grid-cols-2 gap-4">
                    <div>
                        <label class="block text-gray-700 font-medium mb-1">NOME DO MOTORISTA</label>
                        <input type="text" name="motorista" class="w-full px-4 py-2 border border-gray-300 rounded-lg focus:ring-2 focus:ring-blue-500 focus:border-blue-500">
                    </div>
                    <div>
                        <label class="block text-gray-700 font-medium mb-1">DATA</label>
                        <input type="date" name="data" class="w-full px-4 py-2 border border-gray-300 rounded-lg focus:ring-2 focus:ring-blue-500 focus:border-blue-500">
                    </div>
                    <div>
                        <label class="block text-gray-700 font-medium mb-1">CHASSI DO VEÍCULO</label>
                        <input type="text" name="chassi" class="w-full px-4 py-2 border border-gray-300 rounded-lg focus:ring-2 focus:ring-blue-500 focus:border-blue-500">
                    </div>
                    <div>
                        <label class="block text-gray-700 font-medium mb-1">MODELO DO VEÍCULO</label>
                        <input type="text" name="modelo" class="w-full px-4 py-2 border border-gray-300 rounded-lg focus:ring-2 focus:ring-blue-500 focus:border-blue-500">
                    </div>
                    <div>
                        <label class="block text-gray-700 font-medium mb-1">ORIGEM</label>
                        <input type="text" name="origem" class="w-full px-4 py-2 border border-gray-300 rounded-lg focus:ring-2 focus:ring-blue-500 focus:border-blue-500">
                    </div>
                    <div>
                        <label class="block text-gray-700 font-medium mb-1">DESTINO</label>
                        <input type="text" name="destino" class="w-full px-4 py-2 border border-gray-300 rounded-lg focus:ring-2 focus:ring-blue-500 focus:border-blue-500">
                    </div>
                </div>
            </div>

            <!-- Inspection Items -->
            <div class="p-6 border-b border-gray-200 bg-blue-50">
                <h2 class="text-xl font-semibold text-blue-700 mb-4">
                    <i class="fas fa-clipboard-check mr-2"></i>ITENS A SEREM INSPECIONADOS
                </h2>
                
                <div class="overflow-x-auto">
                    <table class="w-full">
                        <thead class="bg-blue-100">
                            <tr>
                                <th class="px-4 py-2 text-left">SIM</th>
                                <th class="px-4 py-2 text-left">NÃO</th>
                                <th class="px-4 py-2 text-left">QUANT.</th>
                                <th class="px-4 py-2 text-left">ITEM</th>
                                <th class="px-4 py-2 text-left">FOTO</th>
                            </tr>
                        </thead>
                        <tbody id="inspection-items">
                            <!-- Sample Item -->
                            <tr class="border-b border-gray-200">
                                <td class="px-4 py-3">
                                    <input type="checkbox" name="sim_chave_ignicao" class="h-5 w-5 text-blue-600 rounded focus:ring-blue-500">
                                </td>
                                <td class="px-4 py-3">
                                    <input type="checkbox" name="nao_chave_ignicao" class="h-5 w-5 text-blue-600 rounded focus:ring-blue-500">
                                </td>
                                <td class="px-4 py-3">
                                    <input type="number" name="quant_chave_ignicao" class="w-16 px-2 py-1 border border-gray-300 rounded">
                                </td>
                                <td class="px-4 py-3 font-medium">CHAVE DE IGNIÇÃO</td>
                                <td class="px-4 py-3">
                                    <div class="flex items-center">
                                        <input type="file" name="foto_chave_ignicao" class="hidden" accept="image/*" onchange="previewPhoto(this)">
                                        <button type="button" onclick="triggerFileInput(this)" class="bg-blue-100 text-blue-700 px-3 py-1 rounded-lg text-sm hover:bg-blue-200">
                                            <i class="fas fa-camera mr-1"></i>Adicionar
                                        </button>
                                        <img class="photo-preview ml-2" alt="Preview">
                                    </div>
                                </td>
                            </tr>
                            <!-- Add more items here -->
                        </tbody>
                    </table>
                </div>
            </div>

            <!-- Damage Check -->
            <div class="p-6 border-b border-gray-200">
                <h2 class="text-xl font-semibold text-blue-700 mb-4">
                    <i class="fas fa-car-crash mr-2"></i>MARQUE (SIM) SE HOUVER AVARIAS
                </h2>
                
                <div class="overflow-x-auto">
                    <table class="w-full">
                        <thead class="bg-blue-100">
                            <tr>
                                <th class="px-4 py-2 text-left">SIM</th>
                                <th class="px-4 py-2 text-left">NÃO</th>
                                <th class="px-4 py-2 text-left">OBSERVAÇÃO</th>
                                <th class="px-4 py-2 text-left">ITEM</th>
                                <th class="px-4 py-2 text-left">FOTO</th>
                            </tr>
                        </thead>
                        <tbody id="damage-items">
                            <!-- Sample Item -->
                            <tr class="border-b border-gray-200">
                                <td class="px-4 py-3">
                                    <input type="checkbox" name="sim_para-choque_traseiro" class="h-5 w-5 text-blue-600 rounded focus:ring-blue-500">
                                </td>
                                <td class="px-4 py-3">
                                    <input type="checkbox" name="nao_para-choque_traseiro" class="h-5 w-5 text-blue-600 rounded focus:ring-blue-500">
                                </td>
                                <td class="px-4 py-3">
                                    <input type="text" name="obs_para-choque_traseiro" class="w-full px-2 py-1 border border-gray-300 rounded" placeholder="Observação">
                                </td>
                                <td class="px-4 py-3 font-medium">PARA-CHOQUE TRASEIRO</td>
                                <td class="px-4 py-3">
                                    <div class="flex items-center">
                                        <input type="file" name="foto_para-choque_traseiro" class="hidden" accept="image/*" onchange="previewPhoto(this)">
                                        <button type="button" onclick="triggerFileInput(this)" class="bg-blue-100 text-blue-700 px-3 py-1 rounded-lg text-sm hover:bg-blue-200">
                                            <i class="fas fa-camera mr-1"></i>Adicionar
                                        </button>
                                        <img class="photo-preview ml-2" alt="Preview">
                                    </div>
                                </td>
                            </tr>
                            <!-- Add more items here -->
                        </tbody>
                    </table>
                </div>
            </div>

            <!-- Tools Section -->
            <div class="p-6 border-b border-gray-200 bg-blue-50">
                <div class="flex justify-between items-center mb-4">
                    <h2 class="text-xl font-semibold text-blue-700">
                        <i class="fas fa-tools mr-2"></i>FERRAMENTAS AVULSAS
                    </h2>
                    <button type="button" onclick="addToolRow()" class="add-row-btn bg-blue-600 text-white px-4 py-2 rounded-lg hover:bg-blue-700 transition-transform">
                        <i class="fas fa-plus mr-1"></i>Adicionar Ferramenta
                    </button>
                </div>
                
                <div class="overflow-x-auto">
                    <table class="w-full">
                        <thead class="bg-blue-100">
                            <tr>
                                <th class="px-4 py-2 text-left">SIM</th>
                                <th class="px-4 py-2 text-left">NÃO</th>
                                <th class="px-4 py-2 text-left">QUANT.</th>
                                <th class="px-4 py-2 text-left">FERRAMENTA</th>
                                <th class="px-4 py-2 text-left">FOTO</th>
                                <th class="px-4 py-2 text-left"></th>
                            </tr>
                        </thead>
                        <tbody id="tools-items">
                            <!-- Sample Tool Row -->
                            <tr class="border-b border-gray-200">
                                <td class="px-4 py-3">
                                    <input type="checkbox" name="sim_ferramentas1" class="h-5 w-5 text-blue-600 rounded focus:ring-blue-500">
                                </td>
                                <td class="px-4 py-3">
                                    <input type="checkbox" name="nao_ferramentas1" class="h-5 w-5 text-blue-600 rounded focus:ring-blue-500">
                                </td>
                                <td class="px-4 py-3">
                                    <input type="number" name="quant_ferramentas1" class="w-16 px-2 py-1 border border-gray-300 rounded" value="1">
                                </td>
                                <td class="px-4 py-3">
                                    <input type="text" name="ferramentas1" class="w-full px-2 py-1 border border-gray-300 rounded" placeholder="Nome da ferramenta">
                                </td>
                                <td class="px-4 py-3">
                                    <div class="flex items-center">
                                        <input type="file" name="foto_ferramentas1" class="hidden" accept="image/*" onchange="previewPhoto(this)">
                                        <button type="button" onclick="triggerFileInput(this)" class="bg-blue-100 text-blue-700 px-3 py-1 rounded-lg text-sm hover:bg-blue-200">
                                            <i class="fas fa-camera mr-1"></i>Adicionar
                                        </button>
                                        <img class="photo-preview ml-2" alt="Preview">
                                    </div>
                                </td>
                                <td class="px-4 py-3">
                                    <button type="button" onclick="removeRow(this)" class="text-red-500 hover:text-red-700">
                                        <i class="fas fa-trash"></i>
                                    </button>
                                </td>
                            </tr>
                        </tbody>
                    </table>
                </div>
            </div>

            <!-- Observations and Fuel Level -->
            <div class="p-6">
                <div class="mb-6">
                    <label class="block text-gray-700 font-medium mb-2">OBSERVAÇÕES</label>
                    <textarea name="observacoes" class="w-full px-4 py-2 border border-gray-300 rounded-lg h-32 focus:ring-2 focus:ring-blue-500 focus:border-blue-500" placeholder="Digite aqui quaisquer observações relevantes..."></textarea>
                </div>
                
                <div class="grid grid-cols-1 md:grid-cols-2 gap-6">
                    <div>
                        <h3 class="text-lg font-semibold text-gray-700 mb-3">NÍVEL DE COMBUSTÍVEL</h3>
                        <div class="grid grid-cols-1 gap-3">
                            <div>
                                <label class="block text-gray-700 mb-1">MÉDIA</label>
                                <input type="text" name="nivel_combustivel_media" class="w-full px-4 py-2 border border-gray-300 rounded-lg">
                            </div>
                            <div>
                                <label class="block text-gray-700 mb-1">DIESEL %</label>
                                <input type="number" name="nivel_combustivel_diesel" min="0" max="100" class="w-full px-4 py-2 border border-gray-300 rounded-lg">
                            </div>
                            <div>
                                <label class="block text-gray-700 mb-1">ARLA %</label>
                                <input type="number" name="nivel_combustivel_arla" min="0" max="100" class="w-full px-4 py-2 border border-gray-300 rounded-lg">
                            </div>
                        </div>
                    </div>
                    
                    <div class="flex flex-col justify-end">
                        <div class="mb-4">
                            <label class="block text-gray-700 font-medium mb-1">RESPONSÁVEL PELA VISTORIA</label>
                            <input type="text" name="responsavel_vistoria" class="w-full px-4 py-2 border border-gray-300 rounded-lg">
                        </div>
                        
                        <div class="flex flex-wrap gap-3 mt-4 no-print">
                            <button type="button" onclick="printForm()" class="bg-gray-200 text-gray-800 px-6 py-2 rounded-lg hover:bg-gray-300 flex items-center">
                                <i class="fas fa-print mr-2"></i>Imprimir
                            </button>
                            <button type="button" onclick="saveForm()" class="bg-blue-600 text-white px-6 py-2 rounded-lg hover:bg-blue-700 flex items-center">
                                <i class="fas fa-save mr-2"></i>Salvar
                            </button>
                            <a href="https://drive.google.com/drive/folders/1lwNNoFyt0IS2fDzX73xWC2Y7T7DJG-xv" target="_blank" class="bg-green-600 text-white px-6 py-2 rounded-lg hover:bg-green-700 flex items-center">
                                <i class="fas fa-cloud-upload-alt mr-2"></i>Enviar
                            </a>
                        </div>
                    </div>
                </div>
            </div>
        </section>

        <!-- Print Header (only visible when printing) -->
        <div class="print-only bg-white p-4 mb-4 rounded-lg">
            <h1 class="text-2xl font-bold text-center">LISTA DE VERIFICAÇÃO VEICULAR</h1>
            <div class="flex justify-between mt-4">
                <div>
                    <p><strong>Motorista:</strong> <span id="print-motorista"></span></p>
                    <p><strong>Veículo:</strong> <span id="print-veiculo"></span></p>
                </div>
                <div>
                    <p><strong>Data:</strong> <span id="print-data"></span></p>
                    <p><strong>Chassi:</strong> <span id="print-chassi"></span></p>
                </div>
            </div>
        </div>
    </div>

    <script>
        // Photo preview functionality
        function previewPhoto(input) {
            const file = input.files[0];
            if (file) {
                const reader = new FileReader();
                const preview = input.parentElement.querySelector('.photo-preview');
                
                reader.onload = function(e) {
                    preview.src = e.target.result;
                    preview.style.display = 'block';
                }
                
                reader.readAsDataURL(file);
            }
        }
        
        // Trigger file input when camera button is clicked
        function triggerFileInput(button) {
            const fileInput = button.parentElement.querySelector('input[type="file"]');
            fileInput.click();
        }
        
        // Add new tool row
        function addToolRow() {
            const toolsTable = document.getElementById('tools-items');
            const rowCount = toolsTable.querySelectorAll('tr').length + 1;
            
            const newRow = document.createElement('tr');
            newRow.className = 'border-b border-gray-200';
            newRow.innerHTML = `
                <td class="px-4 py-3">
                    <input type="checkbox" name="sim_ferramentas${rowCount}" class="h-5 w-5 text-blue-600 rounded focus:ring-blue-500">
                </td>
                <td class="px-4 py-3">
                    <input type="checkbox" name="nao_ferramentas${rowCount}" class="h-5 w-5 text-blue-600 rounded focus:ring-blue-500">
                </td>
                <td class="px-4 py-3">
                    <input type="number" name="quant_ferramentas${rowCount}" class="w-16 px-2 py-1 border border-gray-300 rounded" value="1">
                </td>
                <td class="px-4 py-3">
                    <input type="text" name="ferramentas${rowCount}" class="w-full px-2 py-1 border border-gray-300 rounded" placeholder="Nome da ferramenta">
                </td>
                <td class="px-4 py-3">
                    <div class="flex items-center">
                        <input type="file" name="foto_ferramentas${rowCount}" class="hidden" accept="image/*" onchange="previewPhoto(this)">
                        <button type="button" onclick="triggerFileInput(this)" class="bg-blue-100 text-blue-700 px-3 py-1 rounded-lg text-sm hover:bg-blue-200">
                            <i class="fas fa-camera mr-1"></i>Adicionar
                        </button>
                        <img class="photo-preview ml-2" alt="Preview">
                    </div>
                </td>
                <td class="px-4 py-3">
                    <button type="button" onclick="removeRow(this)" class="text-red-500 hover:text-red-700">
                        <i class="fas fa-trash"></i>
                    </button>
                </td>
            `;
            
            toolsTable.appendChild(newRow);
        }
        
        // Remove row
        function removeRow(button) {
            const row = button.closest('tr');
            row.remove();
        }
        
        // Print form
        function printForm() {
            // Update print preview fields
            document.getElementById('print-motorista').textContent = document.querySelector('input[name="motorista"]').value;
            document.getElementById('print-veiculo').textContent = document.querySelector('input[name="modelo"]').value;
            document.getElementById('print-data').textContent = document.querySelector('input[name="data"]').value;
            document.getElementById('print-chassi').textContent = document.querySelector('input[name="chassi"]').value;
            
            window.print();
        }
        
        // Save form data to localStorage
        function saveForm() {
            const formData = {
                motorista: document.querySelector('input[name="motorista"]').value,
                data: document.querySelector('input[name="data"]').value,
                chassi: document.querySelector('input[name="chassi"]').value,
                modelo: document.querySelector('input[name="modelo"]').value,
                origem: document.querySelector('input[name="origem"]').value,
                destino: document.querySelector('input[name="destino"]').value,
                responsavel: document.querySelector('input[name="responsavel_vistoria"]').value,
                observacoes: document.querySelector('textarea[name="observacoes"]').value,
                // Add more fields as needed
            };
            
            localStorage.setItem('vehicleInspectionData', JSON.stringify(formData));
            
            alert('Dados salvos com sucesso!');
        }
        
        // Load saved data if available
        window.addEventListener('DOMContentLoaded', () => {
            const savedData = localStorage.getItem('vehicleInspectionData');
            if (savedData) {
                const formData = JSON.parse(savedData);
                
                document.querySelector('input[name="motorista"]').value = formData.motorista || '';
                document.querySelector('input[name="data"]').value = formData.data || '';
                document.querySelector('input[name="chassi"]').value = formData.chassi || '';
                document.querySelector('input[name="modelo"]').value = formData.modelo || '';
                document.querySelector('input[name="origem"]').value = formData.origem || '';
                document.querySelector('input[name="destino"]').value = formData.destino || '';
                document.querySelector('input[name="responsavel_vistoria"]').value = formData.responsavel || '';
                document.querySelector('textarea[name="observacoes"]').value = formData.observacoes || '';
            }
        });
    </script>
</body>
</html>
