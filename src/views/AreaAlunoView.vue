<template>
    <div class="upload-container">
        <div id="preview" class="upload-icon"></div>
        <label for="file-input" class="upload-iconmais"><i class="fas fa-plus-circle"></i></label>
        <input type="file" id="file-input" accept="image/*" />
        <div id="nome"></div>
    </div>
    <div class="container">
        <h1 class="organize">Organize seu cronograma</h1>

        <form id="schedule-form">
            <div>
                <label for="date-input">Data:</label>
                <input type="text" id="date-input" name="date-input">
            </div>

            <div>
                <label for="content-input">Conteúdo:</label>
                <input type="text" id="content-input" name="content-input">
            </div>

            <div>
                <label for="link-input">Link arquivo:</label>
                <input type="text" id="link-input" name="link-input">
            </div>

            <div>
                <label for="video-input">Link vídeo:</label>
                <input type="text" id="video-input" name="video-input">
            </div>

            <button class="btn botao" type="button" @click="addEntry">Adicionar</button>
        </form>

        <table id="schedule-table">
            <thead>
                <tr>
                    <th>Data</th>
                    <th>Conteúdo</th>
                    <th>Arquivo</th>
                    <th>Vídeo</th>
                    <th></th>
                </tr>
            </thead>
            <tbody>
            </tbody>
        </table>
    </div>
</template>

<script setup>
    import { onMounted } from 'vue';

    function addEntry() {
        const dateInput = document.getElementById("date-input").value;
        const contentInput = document.getElementById("content-input").value;
        const linkInput = document.getElementById("link-input").value;
        const videoInput = document.getElementById("video-input").value;

        const entry = {
            date: dateInput,
            content: contentInput,
            link: linkInput,
            video: videoInput
        };

        let scheduleData = JSON.parse(localStorage.getItem('scheduleData')) || [];
        scheduleData.push(entry);

        localStorage.setItem('scheduleData', JSON.stringify(scheduleData));

        renderScheduleData(scheduleData);

        document.getElementById("date-input").value = '';
        document.getElementById("content-input").value = '';
        document.getElementById("link-input").value = '';
        document.getElementById("video-input").value = '';
    }

    function renderScheduleData(data) {
        const table = document.getElementById("schedule-table").getElementsByTagName('tbody')[0];
        table.innerHTML = '';

        for (let i = 0; i < data.length; i++) {
            const entry = data[i];

            const newRow = table.insertRow();

            const dateCell = newRow.insertCell();
            const contentCell = newRow.insertCell();
            const linkCell = newRow.insertCell();
            const videoCell = newRow.insertCell();
            const removeCell = newRow.insertCell();

            dateCell.textContent = entry.date;
            contentCell.textContent = entry.content;

            const linkBtn = document.createElement('a');
            linkBtn.classList.add('link-btn');
            linkBtn.href = entry.link;
            linkBtn.target = "_blank";
            linkBtn.textContent = 'Link';
            linkCell.appendChild(linkBtn);

            const videoBtn = document.createElement('button');
            videoBtn.classList.add('video-btn');
            videoBtn.textContent = 'Vídeo';
            videoBtn.addEventListener('click', function () {
                openVideo(entry.video);
            });
            videoBtn.classList.add("btn");
            videoCell.appendChild(videoBtn);

            const removeBtn = document.createElement('button');
            removeBtn.classList.add('remove-btn');
            removeBtn.textContent = 'Remover';
            removeBtn.addEventListener('click', function () {
                removeEntry(newRow, i);
            });
            removeBtn.classList.add("btn");
            removeCell.appendChild(removeBtn);
        }
    }

    function openVideo(url) {
        window.open(url);
    }

    function removeEntry(row, index) {
        let scheduleData = JSON.parse(localStorage.getItem('scheduleData')) || [];
        scheduleData.splice(index, 1);

        localStorage.setItem('scheduleData', JSON.stringify(scheduleData));

        const table = document.getElementById("schedule-table").getElementsByTagName('tbody')[0];
        table.removeChild(row);
    }

    onMounted(() => {
        renderScheduleData(JSON.parse(localStorage.getItem('scheduleData')) || [])

        /*foto*/
        document.getElementById('file-input').addEventListener('change', function (event) {
            var file = event.target.files[0];
            var reader = new FileReader();

            reader.onload = function (e) {
                var preview = document.getElementById('preview');
                preview.style.backgroundImage = 'url(' + e.target.result + ')';
                preview.innerHTML = '';
            };

            reader.readAsDataURL(file);
        });


        var imagePath = localStorage.getItem('selectedImagePath');
        if (imagePath) {
            var preview = document.getElementById('preview');
            preview.style.backgroundImage = 'url(' + imagePath + ')';
        }

        // Evento disparado quando o usuário seleciona uma imagem
        document.getElementById('file-input').addEventListener('change', function (event) {
            var file = event.target.files[0];
            var reader = new FileReader();

            reader.onload = function (e) {
                var preview = document.getElementById('preview');
                preview.style.backgroundImage = 'url(' + e.target.result + ')';
                preview.innerHTML = '';

                // Armazena o caminho da imagem selecionada localmente
                localStorage.setItem('selectedImagePath', e.target.result);
            };

            reader.readAsDataURL(file);
        });

    })
</script>