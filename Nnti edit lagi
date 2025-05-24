<!DOCTYPE html>
<html lang="id">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Broiler Tracker</title>
    <style>
        body { font-family: Arial, sans-serif; padding: 20px; background: #f4f4f4; color: #333; }
        h1 { text-align: center; color: #0056b3; }
        .container { max-width: 900px; margin: 0 auto; background: #fff; padding: 25px; border-radius: 8px; box-shadow: 0 0 10px rgba(0,0,0,0.1); }
        .form-group { margin-bottom: 15px; }
        label { display: block; margin-bottom: 5px; font-weight: bold; }
        input[type="number"], input[type="date"], input[type="text"] {
            width: calc(100% - 16px);
            padding: 10px;
            margin-bottom: 10px;
            border: 1px solid #ddd;
            border-radius: 4px;
            box-sizing: border-box;
        }
        button {
            padding: 10px 20px;
            background: #28a745;
            color: white;
            border: none;
            border-radius: 4px;
            cursor: pointer;
            font-size: 16px;
            margin-right: 10px;
            transition: background-color 0.3s ease;
        }
        button:hover { background-color: #218838; }
        button.delete { background-color: #dc3545; }
        button.delete:hover { background-color: #c82333; }
        button.edit { background-color: #007bff; }
        button.edit:hover { background-color: #0056b3; }
        button.print { background-color: #6c757d; }
        button.print:hover { background-color: #5a6268; }

        table {
            width: 100%;
            border-collapse: collapse;
            margin-top: 25px;
            box-shadow: 0 0 8px rgba(0,0,0,0.05);
        }
        th, td {
            padding: 12px;
            border: 1px solid #eee;
            text-align: center;
            vertical-align: middle;
        }
        th {
            background-color: #f2f2f2;
            font-weight: bold;
            color: #555;
        }
        tr:nth-child(even) { background-color: #f9f9f9; }
        .summary {
            margin-top: 25px;
            padding: 20px;
            background: #e9f5ff;
            border: 1px solid #b8daff;
            border-radius: 8px;
            font-size: 1.1em;
            line-height: 1.6;
            color: #004085;
        }
        .summary strong { color: #002752; }
        .action-buttons {
            display: flex;
            justify-content: center;
            gap: 5px;
        }
        .initial-data-form {
            background: #f8f8f8;
            border: 1px solid #e0e0e0;
            padding: 20px;
            border-radius: 8px;
            margin-bottom: 20px;
        }
    </style>
</head>
<body>
    <div class="container">
        <h1>Broiler Tracker by IKROMIL HADI. SKM</h1>

        <div class="initial-data-form">
            <h2>Data Awal Peternakan</h2>
            <div class="form-group">
                <label for="namaPeternak">Nama Peternak:</label>
                <input type="text" id="namaPeternak">

                <label for="alamat">Alamat:</label>
                <input type="text" id="alamat">

                <label for="bermitraDengan">Bermitra dengan:</label>
                <input type="text" id="bermitraDengan">

                <label for="populasiAwal">Populasi Awal (ekor):</label>
                <input type="number" id="populasiAwal" min="0">
                <button onclick="simpanDataAwal()">Simpan Data Awal</button>
            </div>
        </div>

        <div class="form-group">
            <h2>Input Data Harian</h2>
            <label for="tanggal">Tanggal:</label>
            <input type="date" id="tanggal">

            <label for="abw">ABW (gram):</label>
            <input type="number" id="abw" min="0">

            <label for="pakanMasuk">Pakan Masuk (karung):</label>
            <input type="number" id="pakanMasuk" min="0">

            <label for="pakanTerpakai">Pakan Terpakai (karung):</label>
            <input type="number" id="pakanTerpakai" min="0">

            <label for="ayamMati">Ayam Mati (ekor):</label>
            <input type="number" id="ayamMati" min="0">

            <button onclick="simpanDataHarian()">Simpan Data Harian</button>
            <button class="print" onclick="cetakPDF()">Cetak PDF</button>
        </div>

        <div class="summary" id="ringkasan"></div>

        <table id="dataTable">
            <thead>
                <tr>
                    <th>Tanggal</th>
                    <th>ABW (g)</th>
                    <th>Pakan Masuk (Karung)</th>
                    <th>Pakan Masuk (Kg)</th>
                    <th>Pakan Terpakai (Karung)</th>
                    <th>Pakan Terpakai (Kg)</th>
                    <th>Sisa Pakan (Karung)</th>
                    <th>Sisa Pakan (Kg)</th>
                    <th>Ayam Mati (ekor)</th>
                    <th>Sisa Ayam (ekor)</th>
                    <th>FCR</th>
                    <th>Aksi</th>
                </tr>
            </thead>
            <tbody>
                </tbody>
        </table>
    </div>

    <script src="https://cdnjs.cloudflare.com/ajax/libs/html2pdf.js/0.10.1/html2pdf.bundle.min.js"></script>

    <script>
        const PAKAN_PER_KARUNG_KG = 50; // 1 karung = 50 kg

        let initialData = {
            namaPeternak: '',
            alamat: '',
            bermitraDengan: '',
            populasiAwal: 0
        };

        let dailyEntries = []; // Array untuk menyimpan semua data harian
        let editingIndex = -1; // Untuk melacak baris yang sedang diedit

        // Fungsi untuk memuat data dari localStorage saat halaman pertama kali dibuka
        document.addEventListener('DOMContentLoaded', () => {
            loadDataFromLocalStorage();
            renderInitialDataForm();
            renderTableAndSummary();
        });

        function loadDataFromLocalStorage() {
            const storedInitialData = localStorage.getItem('broilerTrackerInitialData');
            if (storedInitialData) {
                initialData = JSON.parse(storedInitialData);
            }

            const storedDailyData = localStorage.getItem('broilerTrackerDailyData');
            if (storedDailyData) {
                dailyEntries = JSON.parse(storedDailyData);
            }
        }

        function saveDataToLocalStorage() {
            localStorage.setItem('broilerTrackerInitialData', JSON.stringify(initialData));
            localStorage.setItem('broilerTrackerDailyData', JSON.stringify(dailyEntries));
        }

        function renderInitialDataForm() {
            document.getElementById('namaPeternak').value = initialData.namaPeternak;
            document.getElementById('alamat').value = initialData.alamat;
            document.getElementById('bermitraDengan').value = initialData.bermitraDengan;
            document.getElementById('populasiAwal').value = initialData.populasiAwal;
        }

        function simpanDataAwal() {
            initialData.namaPeternak = document.getElementById('namaPeternak').value;
            initialData.alamat = document.getElementById('alamat').value;
            initialData.bermitraDengan = document.getElementById('bermitraDengan').value;
            initialData.populasiAwal = parseInt(document.getElementById('populasiAwal').value) || 0;
            saveDataToLocalStorage();
            updateSummary(); // Perbarui ringkasan setelah data awal disimpan
            alert('Data Awal Peternakan berhasil disimpan!');
        }

        function simpanDataHarian() {
            const tanggalInput = document.getElementById('tanggal').value;
            if (!tanggalInput) {
                alert('Tanggal harus diisi!');
                return;
            }

            const abw = parseFloat(document.getElementById('abw').value) || 0;
            const pakanMasukKarung = parseFloat(document.getElementById('pakanMasuk').value) || 0;
            const pakanTerpakaiKarung = parseFloat(document.getElementById('pakanTerpakai').value) || 0;
            const ayamMati = parseInt(document.getElementById('ayamMati').value) || 0;

            const newEntry = {
                tanggal: tanggalInput,
                abw: abw,
                pakanMasukKarung: pakanMasukKarung,
                pakanTerpakaiKarung: pakanTerpakaiKarung,
                ayamMati: ayamMati
            };

            if (editingIndex === -1) {
                dailyEntries.push(newEntry);
            } else {
                dailyEntries[editingIndex] = newEntry;
                editingIndex = -1; // Reset editing mode
                document.querySelector('.form-group button').textContent = 'Simpan Data Harian'; // Ubah teks tombol
            }

            saveDataToLocalStorage();
            renderTableAndSummary();
            clearDailyForm();
        }

        function renderTableAndSummary() {
            const tbody = document.querySelector('#dataTable tbody');
            tbody.innerHTML = ''; // Kosongkan tabel sebelum merender ulang

            let cumulativePakanMasukKarung = 0;
            let cumulativePakanTerpakaiKarung = 0;
            let cumulativeAyamMati = 0;
            let lastABW = 0;

            dailyEntries.sort((a, b) => new Date(a.tanggal) - new Date(b.tanggal)); // Urutkan berdasarkan tanggal

            dailyEntries.forEach((entry, index) => {
                cumulativePakanMasukKarung += entry.pakanMasukKarung;
                cumulativePakanTerpakaiKarung += entry.pakanTerpakaiKarung;
                cumulativeAyamMati += entry.ayamMati;
                if (entry.abw > 0) { // Ambil ABW terakhir yang valid
                    lastABW = entry.abw;
                }

                const pakanMasukKg = entry.pakanMasukKarung * PAKAN_PER_KARUNG_KG;
                const pakanTerpakaiKg = entry.pakanTerpakaiKarung * PAKAN_PER_KARUNG_KG;

                const sisaPakanKarungHariIni = cumulativePakanMasukKarung - cumulativePakanTerpakaiKarung;
                const sisaPakanKgHariIni = sisaPakanKarungHariIni * PAKAN_PER_KARUNG_KG;

                const sisaAyamHariIni = initialData.populasiAwal - cumulativeAyamMati;

                // FCR Calculation: Total Feed Consumed (Kg) / (Estimated Live Weight (Kg))
                // Estimated Live Weight = Sisa Ayam * ABW terakhir (dalam Kg)
                const totalPakanTerpakaiKumulatifKg = cumulativePakanTerpakaiKarung * PAKAN_PER_KARUNG_KG;
                const estimatedLiveWeightKg = (sisaAyamHariIni * lastABW) / 1000; // ABW from gram to Kg

                let fcr = 'N/A';
                if (estimatedLiveWeightKg > 0) {
                    fcr = (totalPakanTerpakaiKumulatifKg / estimatedLiveWeightKg).toFixed(2);
                }


                const row = tbody.insertRow();
                row.innerHTML = `
                    <td>${entry.tanggal}</td>
                    <td>${entry.abw}</td>
                    <td>${entry.pakanMasukKarung}</td>
                    <td>${pakanMasukKg}</td>
                    <td>${entry.pakanTerpakaiKarung}</td>
                    <td>${pakanTerpakaiKg}</td>
                    <td>${sisaPakanKarungHariIni.toFixed(2)}</td>
                    <td>${sisaPakanKgHariIni.toFixed(2)}</td>
                    <td>${entry.ayamMati}</td>
                    <td>${sisaAyamHariIni}</td>
                    <td>${fcr}</td>
                    <td class="action-buttons">
                        <button class="edit" onclick="editData(${index})">Edit</button>
                        <button class="delete" onclick="hapusData(${index})">Hapus</button>
                    </td>
                `;
            });

            updateSummary();
        }

        function updateSummary() {
            const ringkasanDiv = document.getElementById('ringkasan');

            let cumulativePakanMasukKarung = 0;
            let cumulativePakanTerpakaiKarung = 0;
            let cumulativeAyamMati = 0;
            let lastABW = 0;

            dailyEntries.forEach(entry => {
                cumulativePakanMasukKarung += entry.pakanMasukKarung;
                cumulativePakanTerpakaiKarung += entry.pakanTerpakaiKarung;
                cumulativeAyamMati += entry.ayamMati;
                if (entry.abw > 0) {
                    lastABW = entry.abw;
                }
            });

            const totalSisaAyam = initialData.populasiAwal - cumulativeAyamMati;
            const sisaPakanGudangKarung = cumulativePakanMasukKarung - cumulativePakanTerpakaiKarung;
            const sisaPakanGudangKg = sisaPakanGudangKarung * PAKAN_PER_KARUNG_KG;

            const totalPakanTerpakaiKumulatifKg = cumulativePakanTerpakaiKarung * PAKAN_PER_KARUNG_KG;
            const estimatedLiveWeightKgOverall = (totalSisaAyam * lastABW) / 1000; // Convert ABW from gram to Kg

            let fcrOverall = 'N/A';
            if (estimatedLiveWeightKgOverall > 0) {
                fcrOverall = (totalPakanTerpakaiKumulatifKg / estimatedLiveWeightKgOverall).toFixed(2);
            } else if (dailyEntries.length > 0 && initialData.populasiAwal > 0) {
                 // Fallback FCR for cases with no ABW recorded yet but some activity
                 // This might be less accurate, but provides a value.
                 fcrOverall = (totalPakanTerpakaiKumulatifKg / (initialData.populasiAwal * (lastABW || 1) / 1000)).toFixed(2);
            }

            ringkasanDiv.innerHTML = `
                <h2>Ringkasan Keseluruhan</h2>
                <strong>Nama Peternak:</strong> ${initialData.namaPeternak || 'Belum diisi'}<br>
                <strong>Alamat:</strong> ${initialData.alamat || 'Belum diisi'}<br>
                <strong>Bermitra dengan:</strong> ${initialData.bermitraDengan || 'Belum diisi'}<br>
                <strong>Populasi Awal:</strong> ${initialData.populasiAwal} ekor<br>
                ---<br>
                <strong>Total Pakan Masuk Kumulatif:</strong> ${cumulativePakanMasukKarung.toFixed(2)} karung (${(cumulativePakanMasukKarung * PAKAN_PER_KARUNG_KG).toFixed(2)} kg)<br>
                <strong>Total Pakan Terpakai Kumulatif:</strong> ${cumulativePakanTerpakaiKarung.toFixed(2)} karung (${(cumulativePakanTerpakaiKarung * PAKAN_PER_KARUNG_KG).toFixed(2)} kg)<br>
                <strong>Sisa Pakan Gudang:</strong> ${sisaPakanGudangKarung.toFixed(2)} karung (${sisaPakanGudangKg.toFixed(2)} kg)<br>
                <strong>Total Kematian Kumulatif:</strong> ${cumulativeAyamMati} ekor<br>
                <strong>Sisa Ayam Hidup Saat Ini:</strong> ${totalSisaAyam} ekor<br>
                <strong>ABW Terakhir Tercatat:</strong> ${lastABW} gram<br>
                <strong>FCR Keseluruhan:</strong> ${fcrOverall}
            `;
        }

        function clearDailyForm() {
            document.getElementById('tanggal').value = '';
            document.getElementById('abw').value = '';
            document.getElementById('pakanMasuk').value = '';
            document.getElementById('pakanTerpakai').value = '';
            document.getElementById('ayamMati').value = '';
        }

        function editData(index) {
            const entry = dailyEntries[index];
            document.getElementById('tanggal').value = entry.tanggal;
            document.getElementById('abw').value = entry.abw;
            document.getElementById('pakanMasuk').value = entry.pakanMasukKarung;
            document.getElementById('pakanTerpakai').value = entry.pakanTerpakaiKarung;
            document.getElementById('ayamMati').value = entry.ayamMati;

            editingIndex = index; // Set index baris yang sedang diedit
            document.querySelector('.form-group button').textContent = 'Update Data Harian'; // Ubah teks tombol
        }

        function hapusData(index) {
            if (confirm('Anda yakin ingin menghapus data ini?')) {
                dailyEntries.splice(index, 1); // Hapus 1 elemen dari array di posisi 'index'
                saveDataToLocalStorage();
                renderTableAndSummary();
            }
        }

        function cetakPDF() {
            const element = document.querySelector('.container'); // Targetkan div .container untuk dicetak

            // Opsi untuk html2pdf
            const opt = {
                margin: 10,
                filename: 'Broiler_Tracker_Laporan.pdf',
                image: { type: 'jpeg', quality: 0.98 },
                html2canvas: { scale: 2, logging: true, dpi: 192, letterRendering: true },
                jsPDF: { unit: 'mm', format: 'a4', orientation: 'landscape' } // Ubah orientasi menjadi landscape
            };

            // Tambahkan judul ke elemen yang akan dicetak untuk laporan PDF yang lebih baik
            const titleElement = document.createElement('h2');
            titleElement.textContent = 'Laporan Broiler Tracker';
            element.prepend(titleElement); // Tambahkan judul di awal elemen kontainer

            html2pdf().set(opt).from(element).save().then(() => {
                element.removeChild(titleElement); // Hapus judul setelah PDF dibuat agar tidak mengganggu tampilan web
            });
        }
    </script>
</body>
</html>
