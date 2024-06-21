<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Controle de Manutenção de Equipamentos</title>
    <style>
        body {
            font-family: Arial, sans-serif;
            margin: 20px;
        }

        h1 {
            text-align: center;
        }

        form {
            margin-bottom: 20px;
            display: flex;
            flex-direction: column;
            align-items: center;
        }

        label {
            display: block;
            margin-top: 10px;
        }

        input, select, textarea {
            width: 300px;
            padding: 8px;
            margin-top: 5px;
        }

        button {
            display: inline-block;
            margin-top: 10px;
            padding: 10px 15px;
            background-color: #4CAF50;
            color: white;
            border: none;
            cursor: pointer;
        }

        button:hover {
            background-color: #45a049;
        }

        .lists {
            display: flex;
            flex-direction: column;
            align-items: center;
            margin-top: 20px;
        }

        .list {
            width: 90%;
            margin-bottom: 20px;
        }

        ul {
            list-style-type: none;
            padding: 0;
        }

        li {
            padding: 10px;
            background-color: #f4f4f4;
            margin-bottom: 5px;
            display: flex;
            justify-content: space-between;
            align-items: center;
        }

        li span {
            flex-grow: 1;
        }

        li button {
            margin-left: 10px;
            background-color: #2196F3;
            color: white;
            border: none;
            cursor: pointer;
        }

        li button:hover {
            background-color: #0b7dda;
        }

        .copy-buttons, .save-load-buttons {
            text-align: center;
            margin-top: 20px;
        }

        .copy-buttons button, .save-load-buttons button {
            margin: 5px;
            background-color: #2196F3;
            color: white;
            border: none;
            cursor: pointer;
        }

        .copy-buttons button:hover, .save-load-buttons button:hover {
            background-color: #0b7dda;
        }

        .navigation {
            text-align: center;
            margin-bottom: 20px;
        }

        .navigation button {
            margin: 5px;
            background-color: #2196F3;
            color: white;
            border: none;
            cursor: pointer;
        }

        .navigation button:hover {
            background-color: #0b7dda;
        }

        .footer {
            text-align: center;
            margin-top: 40px;
            font-size: 14px;
            color: #555;
        }
    </style>
</head>
<body>
    <div class="navigation">
        <button onclick="window.location.href='relato-de-desvios.html'">Relato de Desvios</button>
        <button onclick="window.location.href='dds.html'">DDS</button>
        <button onclick="window.location.href='matriz-de-bloqueio.html'">Matriz de Bloqueio</button>
    </div>

    <h1>Controle de Manutenção de Equipamentos</h1>
    <form id="equipment-form">
        <label for="prefix">Prefixo do Equipamento:</label>
        <input type="text" id="prefix" name="prefix" required>

        <label for="status">Status:</label>
        <select id="status" name="status">
            <option value="maintenance">Manutenção</option>
            <option value="released">Liberado</option>
        </select>

        <label for="diagnosis">Diagnóstico:</label>
        <textarea id="diagnosis" name="diagnosis" required></textarea>

        <button type="submit">Adicionar Equipamento</button>
    </form>

    <div class="lists">
        <div class="list">
            <h2>Equipamentos Liberados</h2>
            <ul id="released-list"></ul>
        </div>
        <div class="list">
            <h2>Equipamentos em Manutenção</h2>
            <ul id="maintenance-list"></ul>
        </div>
    </div>

    <div class="copy-buttons">
        <button id="copy-maintenance">Copiar Manutenção</button>
        <button id="copy-released">Copiar Liberados</button>
        <button id="copy-all">Copiar Ambos</button>
    </div>
    <div class="footer">
        Criado por Samuel Fernandes
    </div>

    <script>
        document.addEventListener('DOMContentLoaded', () => {
            const form = document.getElementById('equipment-form');
            const maintenanceList = document.getElementById('maintenance-list');
            const releasedList = document.getElementById('released-list');

            form.addEventListener('submit', (event) => {
                event.preventDefault();

                const prefix = form.prefix.value;
                const status = form.status.value;
                const diagnosis = form.diagnosis.value;

                const equipment = {
                    prefix,
                    status,
                    diagnosis
                };

                addEquipmentToList(equipment);
                form.reset();
                saveEquipments();
            });

            function addEquipmentToList(equipment) {
                const listItem = document.createElement('li');
                const textSpan = document.createElement('span');
                textSpan.textContent = `${equipment.prefix} - ${equipment.diagnosis}`;

                listItem.appendChild(textSpan);

                const moveButton = document.createElement('button');
                moveButton.textContent = equipment.status === 'maintenance' ? 'Liberar' : 'Manutenção';
                moveButton.addEventListener('click', () => {
                    equipment.status = equipment.status === 'maintenance' ? 'released' : 'maintenance';
                    moveButton.textContent = equipment.status === 'maintenance' ? 'Liberar' : 'Manutenção';

                    if (equipment.status === 'maintenance') {
                        maintenanceList.appendChild(listItem);
                    } else {
                        releasedList.appendChild(listItem);
                    }
                    saveEquipments();
                });

                listItem.appendChild(moveButton);

                const editButton = document.createElement('button');
                editButton.textContent = 'Editar';
                editButton.addEventListener('click', () => {
                    const newDiagnosis = prompt('Editar Diagnóstico:', equipment.diagnosis);
                    if (newDiagnosis !== null) {
                        equipment.diagnosis = newDiagnosis;
                        textSpan.textContent = `${equipment.prefix} - ${equipment.diagnosis}`;
                        saveEquipments();
                    }
                });
                listItem.appendChild(editButton);

                const deleteButton = document.createElement('button');
                deleteButton.textContent = 'Apagar';
                deleteButton.addEventListener('click', () => {
                    listItem.remove();
                    saveEquipments();
                });
                listItem.appendChild(deleteButton);

                if (equipment.status === 'maintenance') {
                    maintenanceList.appendChild(listItem);
                } else {
                    releasedList.appendChild(listItem);
                }
            }

            function saveEquipments() {
                const equipments = {
                    maintenance: [],
                    released: []
                };

                maintenanceList.querySelectorAll('li').forEach(item => {
                    const text = item.querySelector('span').textContent;
                    const [prefix, diagnosis] = text.split(' - ');
                    equipments.maintenance.push({ prefix, status: 'maintenance', diagnosis });
                });

                releasedList.querySelectorAll('li').forEach(item => {
                    const text = item.querySelector('span').textContent;
                    const [prefix, diagnosis] = text.split(' - ');
                    equipments.released.push({ prefix, status: 'released', diagnosis });
                });

                localStorage.setItem('equipments', JSON.stringify(equipments));
            }

            function loadEquipments() {
                const savedEquipments = localStorage.getItem('equipments');
                if (savedEquipments) {
                    const equipments = JSON.parse(savedEquipments);
                    maintenanceList.innerHTML = '';
                    releasedList.innerHTML = '';

                    equipments.maintenance.forEach(equipment => addEquipmentToList(equipment));
                    equipments.released.forEach(equipment => addEquipmentToList(equipment));
                }
            }

            function copyListToClipboard(list) {
                const items = list.querySelectorAll('li');
                let text = '';
                items.forEach(item => {
                    text += item.querySelector('span').textContent + '\n\n';
                });
                navigator.clipboard.writeText(text.trim())
                    .then(() => alert('Texto copiado com sucesso!'))
                    .catch(err => alert('Erro ao copiar texto: ' + err));
            }

            function copyBothListsToClipboard() {
                let text = '*EQUIPAMENTOS LIBERADOS*\n\n';
                text += getTextFromList(releasedList) + '\n\n';
                text += '*EQUIPAMENTOS EM MANUTENÇÃO*\n\n';
                text += getTextFromList(maintenanceList);
                navigator.clipboard.writeText(text.trim())
                    .then(() => alert('Texto copiado com sucesso!'))
                    .catch(err => alert('Erro ao copiar texto: ' + err));
            }

            function getTextFromList(list) {
                const items = list.querySelectorAll('li');
                let text = '';
                items.forEach(item => {
                    text += item.querySelector('span').textContent + '\n\n';
                });
                return text.trim();
            }

            const copyMaintenanceButton = document.getElementById('copy-maintenance');
            const copyReleasedButton = document.getElementById('copy-released');
            const copyAllButton = document.getElementById('copy-all');

            copyMaintenanceButton.addEventListener('click', () => {
                copyListToClipboard(maintenanceList);
            });

            copyReleasedButton.addEventListener('click', () => {
                copyListToClipboard(releasedList);
            });

            copyAllButton.addEventListener('click', () => {
                copyBothListsToClipboard();
            });

            loadEquipments(); // Carregar os dados ao iniciar a página
        });
    </script>
</body>
</html>
