<!DOCTYPE html>
<html lang="id">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, user-scalable=yes">
    <title>Kasir Cucian - Stok, Gaji & Grafik (Fix + Edit + Keranjang)</title>
    <script src="https://cdn.jsdelivr.net/npm/chart.js@4.4.0/dist/chart.umd.min.js"></script>
    <style>
        * { box-sizing: border-box; }
        body {
            font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
            background: #f4f7f6;
            margin: 0;
            padding: 0;
            color: #333;
        }
        .menu-icon {
            position: fixed;
            top: 15px;
            left: 15px;
            font-size: 28px;
            cursor: pointer;
            background: #2c3e50;
            color: white;
            padding: 8px 12px;
            border-radius: 8px;
            z-index: 1001;
            box-shadow: 0 2px 5px rgba(0,0,0,0.2);
        }
        .side-menu {
            position: fixed;
            top: 0;
            left: -260px;
            width: 260px;
            height: 100%;
            background: #2c3e50;
            color: white;
            transition: 0.3s;
            z-index: 1000;
            padding-top: 60px;
        }
        .side-menu a {
            display: block;
            padding: 15px 20px;
            color: white;
            text-decoration: none;
            font-weight: bold;
            border-bottom: 1px solid #34495e;
        }
        .side-menu a:hover { background: #1abc9c; }
        .close-menu {
            position: absolute;
            top: 10px;
            right: 15px;
            font-size: 30px;
            cursor: pointer;
        }
        .container {
            max-width: 1200px;
            margin: 0 auto;
            padding: 20px;
        }
        .page { display: none; }
        .active-page { display: block; }
        h1 { text-align: center; color: #2c3e50; font-size: 24px; margin-top: 0; }
        .card {
            background: white;
            padding: 15px;
            border-radius: 12px;
            box-shadow: 0 4px 6px rgba(0,0,0,0.1);
            margin-bottom: 20px;
        }
        h3 {
            margin-top: 0;
            border-bottom: 2px solid #3498db;
            padding-bottom: 5px;
            color: #3498db;
        }
        .form-group { margin-bottom: 10px; }
        label { display: block; margin-bottom: 5px; font-weight: bold; }
        input, select, button {
            width: 100%;
            padding: 10px;
            border: 1px solid #ddd;
            border-radius: 8px;
        }
        button {
            cursor: pointer;
            font-weight: bold;
            transition: 0.3s;
            margin-top: 5px;
        }
        .btn-add { background: #3498db; color: white; }
        .btn-add:hover { background: #2980b9; }
        .btn-report { background: #2ecc71; color: white; }
        .btn-small { background: #95a5a6; color: white; font-size: 11px; padding: 4px 8px; width: auto; }
        .btn-delete { background: #e74c3c; color: white; font-size: 11px; padding: 5px 10px; width: auto; }
        .btn-promote { background: #f39c12; color: white; font-size: 11px; padding: 5px 10px; width: auto; }
        .btn-edit { background: #27ae60; color: white; font-size: 11px; padding: 5px 10px; width: auto; }
        .tabel-wrapper {
            overflow-x: auto;
            -webkit-overflow-scrolling: touch;
            margin-bottom: 15px;
        }
        table {
            width: 100%;
            border-collapse: collapse;
            font-size: 12px;
            min-width: 600px;
        }
        th, td {
            border: 1px solid #ddd;
            padding: 6px;
            text-align: left;
            vertical-align: top;
            white-space: nowrap;
        }
        th { background: #3498db; color: white; }
        .karyawan-check-group { display: flex; flex-wrap: wrap; gap: 8px; margin-top: 6px; }
        .karyawan-check-group label { background: #ecf0f1; padding: 2px 6px; border-radius: 12px; font-weight: normal; }
        .staf-badge { background: #2ecc71; color: white; padding: 2px 6px; border-radius: 12px; }
        .training-badge { background: #f39c12; color: white; padding: 2px 6px; border-radius: 12px; }
        .laporan-area {
            background: #2c3e50;
            color: #ecf0f1;
            padding: 15px;
            border-radius: 8px;
            font-family: 'Courier New', monospace;
            font-size: 13px;
            white-space: pre-wrap;
        }
        .modal {
            display: none;
            position: fixed;
            z-index: 1100;
            left: 0; top: 0;
            width: 100%; height: 100%;
            background: rgba(0,0,0,0.5);
        }
        .modal-content {
            background: white;
            margin: 50px auto;
            padding: 20px;
            width: 90%;
            max-width: 500px;
            border-radius: 12px;
        }
        .close-modal { float: right; font-size: 28px; cursor: pointer; }
        .item-list { max-height: 300px; overflow-y: auto; margin-top: 15px; }
        .item-row {
            display: flex;
            justify-content: space-between;
            align-items: center;
            padding: 8px;
            border-bottom: 1px solid #ddd;
        }
        .jumlah-input { width: 80px; margin-left: 10px; }
        .hidden { display: none; }
        canvas { max-width: 100%; margin-bottom: 30px; }
        .total-row { background: #e0e0e0; font-weight: bold; }
        .cart-item {
            display: flex;
            justify-content: space-between;
            margin-bottom: 8px;
            border-bottom: 1px dashed #ccc;
        }
    </style>
</head>
<body>
<div class="menu-icon" id="menuIcon">☰</div>
<div class="side-menu" id="sideMenu">
    <div class="close-menu" id="closeMenu">&times;</div>
    <a href="#" data-page="page1">🧼 Penjualan & Riwayat</a>
    <a href="#" data-page="page2">📊 Laporan & Grafik</a>
    <a href="#" data-page="page3">👥 Gaji & Karyawan</a>
    <a href="#" data-page="page4">🗄️ Data Master (Stok)</a>
</div>

<div class="container">
    <!-- Halaman 1 -->
    <div id="page1" class="page active-page">
        <h1>🧼 Cuci Mobil & Motor + Penjualan</h1>
        <div class="card">
            <label>📅 Tanggal Transaksi: </label>
            <input type="date" id="tanggalGlobal">
        </div>
        <div class="card">
            <h3>🚗 Form Jasa Cuci</h3>
            <div class="form-group"><input type="text" id="waktuAngka" placeholder="Jam (HH:MM) - Kosongkan otomatis"><div style="font-size:11px; color:#7f8c8d;">💡 Biarkan kosong, akan terisi jam sekarang</div></div>
            <div class="form-group"><input type="text" id="jenisWarna" placeholder="Nama Kendaraan & Warna (wajib)"></div>
            <div class="form-group">
                <select id="metodeMobil">
                    <option value="">-- Pilih Metode --</option>
                    <option value="Cash">Cash</option>
                    <option value="Qris">Qris</option>
                    <option value="Lainnya">Lainnya</option>
                </select>
            </div>
            <div class="form-group hidden" id="qrisKeteranganGroup"><input type="text" id="keteranganQris" placeholder="Keterangan Qris"></div>
            <div class="form-group hidden" id="lainnyaKeteranganGroup"><input type="text" id="keteranganLainnya" placeholder="Metode Lainnya"></div>
            <div class="form-group"><input type="number" id="jumlahMobil" placeholder="Harga Jasa Cuci"></div>
            <div class="form-group">
                <label>✅ Karyawan yang mengerjakan:</label>
                <div id="checklistPengerjaan" class="karyawan-check-group"></div>
            </div>
            <button id="btnTambahMobil" class="btn-add">Tambah Transaksi Cuci</button>
        </div>
        <div class="card">
            <h3>🍬 Jajan & ✨ Parfum (dengan Keranjang)</h3>
            <button id="btnTambahJajanCart" class="btn-add" style="background:#e67e22; margin-bottom:10px;">🛒 Belanja Jajan (Keranjang)</button>
            <button id="btnTambahParfumCart" class="btn-add" style="background:#9b59b6;">🛒 Belanja Parfum (Keranjang)</button>
        </div>
        <div class="card">
            <h3>📋 Riwayat Penjualan Hari Ini</h3>
            <div id="tabelRiwayatGabungan" class="tabel-wrapper"></div>
        </div>
    </div>

    <!-- Halaman 2 -->
    <div id="page2" class="page">
        <h1>📊 Laporan & Grafik Bulanan</h1>
        <div class="card">
            <h3>Laporan Keuangan Harian</h3>
            <div id="laporanContent" class="laporan-area">Belum ada data.</div>
            <button id="btnUpdateHasil" class="btn-report" style="margin-top:10px;">Perbarui Laporan</button>
        </div>
        <div class="card">
            <h3>📈 Grafik Penjualan Bulan Ini</h3>
            <canvas id="grafikJajan"></canvas>
            <canvas id="grafikParfum"></canvas>
            <canvas id="grafikMobilMotor"></canvas>
            <canvas id="grafikPendapatan"></canvas>
        </div>
    </div>

    <!-- Halaman 3 -->
    <div id="page3" class="page">
        <h1>👥 Gaji & Karyawan</h1>
        <div class="card">
            <h3>Daftar Karyawan</h3>
            <button id="btnTambahKaryawanForm" class="btn-add" style="background:#2c3e50;">+ Tambah Karyawan</button>
            <div id="daftarKaryawanTabel" class="tabel-wrapper"></div>
        </div>
        <div class="card">
            <h3>💰 Gaji Hari Ini</h3>
            <div id="gajiHarianContainer" class="tabel-wrapper"></div>
        </div>
        <div class="card">
            <h3>📅 Gaji Bulanan (Per Tanggal) - Horizontal</h3>
            <div id="tabelGajiPerTanggal" class="tabel-wrapper"></div>
        </div>
        <div class="card">
            <h3>📆 Gaji Minggu Ini (Minggu - Sabtu) + Kasbon</h3>
            <div id="tabelGajiMingguanIni" class="tabel-wrapper"></div>
        </div>
        <div class="card">
            <h3>💰 Kasbon Karyawan (Hari Ini)</h3>
            <button id="btnTambahKasbonUtama" class="btn-add" style="background:#e67e22;">Tambah Kasbon</button>
            <div id="kasbonList" class="tabel-wrapper"></div>
        </div>
    </div>

    <!-- Halaman 4 -->
    <div id="page4" class="page">
        <h1>🗄️ Data Master (Stok Jajan & Parfum)</h1>
        <div class="card">
            <h3>🍬 Master Jajan</h3>
            <div id="masterJajanList" class="tabel-wrapper"></div>
        </div>
        <div class="card">
            <h3>✨ Master Parfum</h3>
            <div id="masterParfumList" class="tabel-wrapper"></div>
        </div>
    </div>
</div>

<!-- Modal keranjang -->
<div id="cartModal" class="modal">
    <div class="modal-content">
        <span class="close-modal" id="closeCartModal">&times;</span>
        <h3 id="cartModalTitle">Pilih Item</h3>
        <div id="cartItemList" class="item-list"></div>
        <div id="cartSummary" style="margin-top: 15px; border-top: 1px solid #ccc; padding-top: 10px;"></div>
        <button id="checkoutCartBtn" class="btn-add" style="background:#2ecc71;">✅ Checkout</button>
        <button id="cancelCartBtn" class="btn-delete">Batal</button>
    </div>
</div>

<!-- Modal Edit Transaksi -->
<div id="editModal" class="modal">
    <div class="modal-content">
        <span class="close-modal" id="closeEditModal">&times;</span>
        <h3 id="editModalTitle">Edit Transaksi</h3>
        <div id="editFormContainer"></div>
        <button id="saveEditBtn" class="btn-add">Simpan Perubahan</button>
    </div>
</div>

<script>
    // ======================== DATA GLOBAL ========================
    let allData = {};
    let currentDate = new Date().toISOString().slice(0,10);
    let masterKaryawan = [];
    let karyawanStatusHistory = [];
    let masterJajan = [];
    let masterParfum = [];
    
    let currentCart = { type: '', items: [] };

    function formatLocalDate(date) {
        let year = date.getFullYear();
        let month = String(date.getMonth() + 1).padStart(2, '0');
        let day = String(date.getDate()).padStart(2, '0');
        return `${year}-${month}-${day}`;
    }

    function formatRupiah(angka) {
        return new Intl.NumberFormat('id-ID', { style:'currency', currency:'IDR', minimumFractionDigits:0 }).format(angka);
    }

    function getCurrentTime() {
        let now = new Date();
        return `${now.getHours().toString().padStart(2,'0')}:${now.getMinutes().toString().padStart(2,'0')}`;
    }

    function getKaryawanStatusAtDate(karyawanId, tanggal) {
        let relevant = karyawanStatusHistory.filter(h => h.karyawanId === karyawanId && h.tanggalMulai <= tanggal);
        if(relevant.length === 0) return "Training";
        relevant.sort((a,b) => b.tanggalMulai.localeCompare(a.tanggalMulai));
        return relevant[0].status;
    }

    function getTodayData() {
        if(!allData[currentDate]) {
            let previousSaldo = { anakSebelumnya:0, cashMingguanSebelumnya:0, parfumSebelumnya:0, jajanSebelumnya:0 };
            let dates = Object.keys(allData).sort();
            if(dates.length) {
                let prev = dates[dates.length-1];
                if(new Date(currentDate).getDay() !== 0) {
                    previousSaldo = { ...allData[prev].saldo };
                }
            }
            allData[currentDate] = {
                mobil: [], parfum: [], jajan: [], pengeluaran: [],
                saldo: previousSaldo, karyawan: [], kasbon: []
            };
        }
        allData[currentDate].karyawan = [...masterKaryawan];
        return allData[currentDate];
    }

    function saveAll() {
        localStorage.setItem('cucianAppFinal', JSON.stringify(allData));
        localStorage.setItem('masterKaryawanCucian', JSON.stringify(masterKaryawan));
        localStorage.setItem('karyawanStatusHistory', JSON.stringify(karyawanStatusHistory));
        localStorage.setItem('masterJajan', JSON.stringify(masterJajan));
        localStorage.setItem('masterParfum', JSON.stringify(masterParfum));
    }

    function loadAll() {
        const savedData = localStorage.getItem('cucianAppFinal');
        if(savedData) allData = JSON.parse(savedData);
        else allData = {};
        const savedKaryawan = localStorage.getItem('masterKaryawanCucian');
        if(savedKaryawan) masterKaryawan = JSON.parse(savedKaryawan);
        else masterKaryawan = [];
        const savedHistory = localStorage.getItem('karyawanStatusHistory');
        if(savedHistory) karyawanStatusHistory = JSON.parse(savedHistory);
        else karyawanStatusHistory = [];
        for(let k of masterKaryawan) {
            if(!karyawanStatusHistory.some(h => h.karyawanId === k.id)) {
                karyawanStatusHistory.push({ karyawanId: k.id, tanggalMulai: '2020-01-01', status: "Training" });
            }
        }
        const savedJajan = localStorage.getItem('masterJajan');
        if(savedJajan) masterJajan = JSON.parse(savedJajan);
        else masterJajan = [{ id:"j1", nama:"Keripik", harga:5000, stok:20 }, { id:"j2", nama:"Minuman", harga:8000, stok:15 }];
        const savedParfum = localStorage.getItem('masterParfum');
        if(savedParfum) masterParfum = JSON.parse(savedParfum);
        else masterParfum = [{ id:"p1", nama:"Parfum Mobil A", harga:25000, stok:10 }, { id:"p2", nama:"Pengharum Ruang", harga:15000, stok:8 }];
        if(!allData[currentDate]) getTodayData();
        else allData[currentDate].karyawan = [...masterKaryawan];
    }

    function hitungGajiPerMobil(harga, karyawanIds, tanggalTransaksi) {
        let hasilCucian = harga / 2;
        let trainingIds = [], stafIds = [];
        for(let id of karyawanIds) {
            let status = getKaryawanStatusAtDate(id, tanggalTransaksi);
            if(status === "Training") trainingIds.push(id);
            else stafIds.push(id);
        }
        let totalTrainingFlat = trainingIds.length * 2500;
        let result = {};
        if(hasilCucian <= totalTrainingFlat) {
            let perTraining = hasilCucian / (trainingIds.length || 1);
            for(let id of trainingIds) result[id] = perTraining;
            for(let id of stafIds) result[id] = 0;
            return result;
        }
        for(let id of trainingIds) result[id] = 2500;
        let sisa = hasilCucian - totalTrainingFlat;
        if(stafIds.length) {
            let perStaf = sisa / stafIds.length;
            for(let id of stafIds) result[id] = perStaf;
        } else {
            let tambahan = sisa / (trainingIds.length || 1);
            for(let id of trainingIds) result[id] += tambahan;
        }
        return result;
    }

    function getGajiKaryawanPerHari(karyawanId, tanggal) {
        let dataHari = allData[tanggal];
        if(!dataHari) return 0;
        let total = 0;
        for(let m of dataHari.mobil) {
            if(m.karyawanIds && m.karyawanIds.includes(karyawanId)) {
                let gajiMap = hitungGajiPerMobil(m.jumlah, m.karyawanIds, tanggal);
                total += gajiMap[karyawanId] || 0;
            }
        }
        return total;
    }

    function getTotalKasbonPerKaryawanDalamPeriode(karyawanId, tglMulai, tglSelesai) {
        let total = 0;
        let current = new Date(tglMulai);
        let end = new Date(tglSelesai);
        while(current <= end) {
            let tgl = formatLocalDate(current);
            let dataHari = allData[tgl];
            if(dataHari && dataHari.kasbon) {
                for(let kb of dataHari.kasbon) {
                    if(kb.idKaryawan === karyawanId) total += kb.jumlah;
                }
            }
            current.setDate(current.getDate() + 1);
        }
        return total;
    }

    // ======================== TABEL RIWAYAT + EDIT ========================
    function renderTabelGabungan() {
        let data = getTodayData();
        let rows = [];
        data.mobil.forEach((m, idx) => {
            rows.push({
                jam: m.waktu,
                nama: m.jenis || '-',
                metode: m.metode,
                tunai: m.metode === 'Cash' ? m.jumlah : 0,
                qris: m.metode === 'Qris' ? m.jumlah : 0,
                lainnya: (m.metode !== 'Cash' && m.metode !== 'Qris') ? m.jumlah : 0,
                jajanTotal: 0, jajanDetail: '',
                parfumTotal: 0, parfumDetail: '',
                raw: { type: 'mobil', idx }
            });
        });
        data.jajan.forEach((j, idx) => {
            let detail = j.items.map(it => `${it.nama} x${it.qty}`).join(', ');
            rows.push({
                jam: j.waktu || '-', nama: '-', metode: j.metode,
                tunai:0, qris:0, lainnya:0,
                jajanTotal: j.total, jajanDetail: detail,
                parfumTotal:0, parfumDetail:'',
                raw: { type: 'jajan', idx }
            });
        });
        data.parfum.forEach((p, idx) => {
            let detail = p.items.map(it => `${it.nama} x${it.qty}`).join(', ');
            rows.push({
                jam: p.waktu || '-', nama: '-', metode: p.metode,
                tunai:0, qris:0, lainnya:0,
                jajanTotal:0, jajanDetail:'',
                parfumTotal: p.total, parfumDetail: detail,
                raw: { type: 'parfum', idx }
            });
        });
        rows.sort((a,b)=> a.jam.localeCompare(b.jam));
        let html = '<table><thead><tr><th>Jam</th><th>Kendaraan</th><th>Metode</th><th>Cuci Tunai</th><th>Cuci QRIS</th><th>Cuci Lainnya</th><th>Total Jajan</th><th>Detail Jajan</th><th>Total Parfum</th><th>Detail Parfum</th><th>Aksi</th></tr></thead><tbody>';
        rows.forEach((r, i) => {
            html += `<tr>
                <td>${r.jam}</td>
                <td>${r.nama}</td>
                <td>${r.metode}</td>
                <td>${formatRupiah(r.tunai)}</td>
                <td>${formatRupiah(r.qris)}</td>
                <td>${formatRupiah(r.lainnya)}</td>
                <td>${formatRupiah(r.jajanTotal)}</td>
                <td>${r.jajanDetail}</td>
                <td>${formatRupiah(r.parfumTotal)}</td>
                <td>${r.parfumDetail}</td>
                <td>
                    <button class="btn-edit" onclick="editTransaksiGabungan(${i})">Edit</button>
                    <button class="btn-delete" onclick="hapusTransaksiGabungan(${i})">Hapus</button>
                </td>
            </tr>`;
        });
        html += '</tbody></table>';
        document.getElementById('tabelRiwayatGabungan').innerHTML = html;
        window.currentRows = rows;
    }

    window.editTransaksiGabungan = (idx) => {
        let rows = window.currentRows;
        if(!rows[idx]) return;
        let raw = rows[idx].raw;
        let data = getTodayData();
        let editData = null;
        let editType = raw.type;
        if(editType === 'mobil') editData = data.mobil[raw.idx];
        else if(editType === 'jajan') editData = data.jajan[raw.idx];
        else editData = data.parfum[raw.idx];
        
        let modal = document.getElementById('editModal');
        let container = document.getElementById('editFormContainer');
        let title = document.getElementById('editModalTitle');
        title.innerText = `Edit ${editType === 'mobil' ? 'Cucian' : (editType === 'jajan' ? 'Jajan' : 'Parfum')}`;
        
        if(editType === 'mobil') {
            container.innerHTML = `
                <div class="form-group"><label>Jam</label><input type="text" id="editJam" value="${editData.waktu}"></div>
                <div class="form-group"><label>Kendaraan & Warna</label><input type="text" id="editJenis" value="${editData.jenis}"></div>
                <div class="form-group"><label>Metode</label><select id="editMetode">
                    <option ${editData.metode==='Cash'?'selected':''}>Cash</option>
                    <option ${editData.metode==='Qris'?'selected':''}>Qris</option>
                    <option ${editData.metode==='Lainnya'?'selected':''}>Lainnya</option>
                </select></div>
                <div class="form-group"><label>Harga</label><input type="number" id="editHarga" value="${editData.jumlah}"></div>
                <div class="form-group"><label>Karyawan</label><div id="editKaryawanList"></div></div>
            `;
            let karyawanDiv = document.getElementById('editKaryawanList');
            let htmlCek = '';
            for(let k of masterKaryawan) {
                let checked = editData.karyawanIds.includes(k.id) ? 'checked' : '';
                htmlCek += `<label><input type="checkbox" class="editCekPekerja" value="${k.id}" ${checked}> ${k.nama}</label> `;
            }
            karyawanDiv.innerHTML = htmlCek;
        } 
        else {
            let itemsHtml = '<div class="form-group"><label>Metode</label><select id="editMetodeItem">';
            itemsHtml += `<option ${editData.metode==='Cash'?'selected':''}>Cash</option>`;
            itemsHtml += `<option ${editData.metode==='Qris'?'selected':''}>Qris</option>`;
            itemsHtml += `</select></div><div class="form-group"><label>Items (ubah jumlah)</label><div id="editItemsList"></div></div>`;
            container.innerHTML = itemsHtml;
            let itemsDiv = document.getElementById('editItemsList');
            let itemsHtmlList = '';
            editData.items.forEach((item, idxItem) => {
                itemsHtmlList += `<div style="margin-bottom:8px;"><strong>${item.nama}</strong> - Harga: ${formatRupiah(item.hargaSatuan)}<br>
                <input type="number" class="editQtyItem" data-id="${item.id}" data-idx="${idxItem}" value="${item.qty}" min="1" style="width:80px;"> 
                <span>Stok tersedia: ${(editType==='jajan'?masterJajan:masterParfum).find(i=>i.id===item.id)?.stok || 0}</span>
                </div>`;
            });
            itemsDiv.innerHTML = itemsHtmlList;
        }
        modal.style.display = 'block';
        window.currentEdit = { type: editType, idx: raw.idx, originalData: editData };
    };
    
    document.getElementById('saveEditBtn').onclick = () => {
        let edit = window.currentEdit;
        if(!edit) return;
        let data = getTodayData();
        let targetArr = edit.type === 'mobil' ? data.mobil : (edit.type === 'jajan' ? data.jajan : data.parfum);
        let oldData = targetArr[edit.idx];
        
        if(edit.type === 'mobil') {
            let newJam = document.getElementById('editJam').value;
            let newJenis = document.getElementById('editJenis').value;
            let newMetode = document.getElementById('editMetode').value;
            let newHarga = parseInt(document.getElementById('editHarga').value);
            let karyawanIds = Array.from(document.querySelectorAll('.editCekPekerja:checked')).map(cb => cb.value);
            if(!newJenis || isNaN(newHarga) || karyawanIds.length===0) { alert("Isi semua data"); return; }
            oldData.waktu = newJam;
            oldData.jenis = newJenis;
            oldData.metode = newMetode;
            oldData.jumlah = newHarga;
            oldData.karyawanIds = karyawanIds;
            if(newMetode === 'Qris') oldData.keteranganQris = prompt("Keterangan Qris:", oldData.keteranganQris||"");
            else if(newMetode === 'Lainnya') oldData.keteranganLainnya = prompt("Keterangan Lainnya:", oldData.keteranganLainnya||"");
        } 
        else {
            let newMetode = document.getElementById('editMetodeItem').value;
            let qtyInputs = document.querySelectorAll('.editQtyItem');
            let newItems = [];
            let valid = true;
            for(let inp of qtyInputs) {
                let itemId = inp.getAttribute('data-id');
                let newQty = parseInt(inp.value);
                let masterItem = (edit.type === 'jajan') ? masterJajan.find(i=>i.id===itemId) : masterParfum.find(i=>i.id===itemId);
                let oldItem = oldData.items.find(i=>i.id===itemId);
                if(!masterItem) continue;
                let stokSetelah = masterItem.stok + (oldItem.qty - newQty);
                if(newQty < 1 || stokSetelah < 0) { alert(`Stok tidak cukup untuk ${masterItem.nama}`); valid=false; break; }
                newItems.push({ id: itemId, nama: masterItem.nama, qty: newQty, hargaSatuan: masterItem.harga });
            }
            if(!valid) return;
            for(let inp of qtyInputs) {
                let itemId = inp.getAttribute('data-id');
                let newQty = parseInt(inp.value);
                let oldItem = oldData.items.find(i=>i.id===itemId);
                let masterItem = (edit.type === 'jajan') ? masterJajan.find(i=>i.id===itemId) : masterParfum.find(i=>i.id===itemId);
                if(masterItem) {
                    masterItem.stok += (oldItem.qty - newQty);
                }
            }
            let total = newItems.reduce((sum,it)=> sum + (it.qty * it.hargaSatuan),0);
            oldData.items = newItems;
            oldData.total = total;
            oldData.metode = newMetode;
            oldData.waktu = getCurrentTime();
        }
        saveAll();
        document.getElementById('editModal').style.display = 'none';
        refreshAll();
        alert("Transaksi berhasil diedit");
    };
    
    window.hapusTransaksiGabungan = (idx) => {
        let rows = window.currentRows;
        if(!rows[idx]) return;
        let data = getTodayData();
        let raw = rows[idx].raw;
        if(raw.type === 'mobil') {
            data.mobil.splice(raw.idx, 1);
        } else if(raw.type === 'jajan') {
            let trans = data.jajan[raw.idx];
            if(trans.items) {
                trans.items.forEach(item => {
                    let master = masterJajan.find(m => m.id === item.id);
                    if(master) master.stok += item.qty;
                });
                saveAll();
            }
            data.jajan.splice(raw.idx, 1);
        } else if(raw.type === 'parfum') {
            let trans = data.parfum[raw.idx];
            if(trans.items) {
                trans.items.forEach(item => {
                    let master = masterParfum.find(m => m.id === item.id);
                    if(master) master.stok += item.qty;
                });
                saveAll();
            }
            data.parfum.splice(raw.idx, 1);
        }
        saveAll();
        refreshAll();
    };
    
    // ======================== KERANJANG ========================
    function openCartModal(type) {
        currentCart = { type: type, items: [] };
        let master = (type === 'jajan') ? masterJajan : masterParfum;
        let title = (type === 'jajan') ? '🛒 Keranjang Jajan' : '🛒 Keranjang Parfum';
        document.getElementById('cartModalTitle').innerText = title;
        let container = document.getElementById('cartItemList');
        if(master.length === 0) {
            container.innerHTML = '<p>Belum ada data, tambahkan di Data Master.</p>';
        } else {
            let html = '';
            master.forEach(item => {
                html += `<div class="item-row">
                    <div><strong>${item.nama}</strong> - ${formatRupiah(item.harga)} (Stok: ${item.stok})</div>
                    <div>
                        <input type="number" id="cart_qty_${item.id}" placeholder="Jml" min="1" max="${item.stok}" class="jumlah-input">
                        <button class="btn-small" onclick="addToCart('${item.id}')">Tambah ke Keranjang</button>
                    </div>
                </div>`;
            });
            container.innerHTML = html;
        }
        updateCartSummary();
        document.getElementById('cartModal').style.display = 'block';
    }
    
    window.addToCart = (itemId) => {
        let qtyInput = document.getElementById(`cart_qty_${itemId}`);
        let qty = parseInt(qtyInput.value);
        if(isNaN(qty) || qty < 1) { alert('Masukkan jumlah yang valid'); return; }
        let masterList = currentCart.type === 'jajan' ? masterJajan : masterParfum;
        let item = masterList.find(i => i.id === itemId);
        if(!item) return;
        if(qty > item.stok) { alert(`Stok tidak cukup. Sisa: ${item.stok}`); return; }
        let existing = currentCart.items.find(i => i.id === itemId);
        if(existing) {
            if(existing.qty + qty > item.stok) { alert(`Total melebihi stok`); return; }
            existing.qty += qty;
        } else {
            currentCart.items.push({ id: item.id, nama: item.nama, harga: item.harga, qty: qty });
        }
        updateCartSummary();
        qtyInput.value = '';
    };
    
    function updateCartSummary() {
        let summaryDiv = document.getElementById('cartSummary');
        if(currentCart.items.length === 0) {
            summaryDiv.innerHTML = '<em>Keranjang kosong</em>';
            return;
        }
        let total = 0;
        let html = '<strong>Keranjang:</strong><br>';
        currentCart.items.forEach((item, idx) => {
            let subtotal = item.harga * item.qty;
            total += subtotal;
            html += `<div class="cart-item">${item.nama} x${item.qty} = ${formatRupiah(subtotal)} 
            <button class="btn-small" onclick="removeFromCart(${idx})">Hapus</button></div>`;
        });
        html += `<hr><strong>Total: ${formatRupiah(total)}</strong>`;
        summaryDiv.innerHTML = html;
    }
    
    window.removeFromCart = (idx) => {
        currentCart.items.splice(idx,1);
        updateCartSummary();
    };
    
    document.getElementById('checkoutCartBtn').onclick = () => {
        if(currentCart.items.length === 0) { alert("Keranjang kosong"); return; }
        let metode = confirm("Klik OK untuk Cash, Cancel untuk Qris") ? "Cash" : "Qris";
        let waktu = getCurrentTime();
        let total = 0;
        let itemsForTrans = [];
        for(let cartItem of currentCart.items) {
            let masterItem = (currentCart.type === 'jajan') ? masterJajan.find(m => m.id === cartItem.id) : masterParfum.find(m => m.id === cartItem.id);
            if(!masterItem) continue;
            if(cartItem.qty > masterItem.stok) { alert(`Stok ${masterItem.nama} tidak cukup`); return; }
            masterItem.stok -= cartItem.qty;
            total += cartItem.harga * cartItem.qty;
            itemsForTrans.push({ id: cartItem.id, nama: cartItem.nama, qty: cartItem.qty, hargaSatuan: cartItem.harga });
        }
        let data = getTodayData();
        let newTrans = { id: Date.now()+Math.random(), waktu, metode, items: itemsForTrans, total };
        if(currentCart.type === 'jajan') data.jajan.push(newTrans);
        else data.parfum.push(newTrans);
        saveAll();
        refreshAll();
        alert(`Berhasil checkout ${currentCart.items.length} item - ${formatRupiah(total)} (${metode})`);
        document.getElementById('cartModal').style.display = 'none';
        currentCart = { type: '', items: [] };
    };
    
    document.getElementById('cancelCartBtn').onclick = () => document.getElementById('cartModal').style.display = 'none';
    document.getElementById('closeCartModal').onclick = () => document.getElementById('cartModal').style.display = 'none';
    
    // ======================== TAMBAH CUCI ========================
    function getSelectedKaryawanIds() {
        let checkboxes = document.querySelectorAll('#checklistPengerjaan .cekPekerja');
        let ids = [];
        checkboxes.forEach(cb => { if(cb.checked) ids.push(cb.value); });
        return ids;
    }
    
    document.getElementById('btnTambahMobil').onclick = () => {
        let jenis = document.getElementById('jenisWarna').value.trim();
        let metode = document.getElementById('metodeMobil').value;
        let jumlah = parseInt(document.getElementById('jumlahMobil').value);
        let karyawanIds = getSelectedKaryawanIds();
        if(!jenis) { alert("Nama Kendaraan & Warna wajib diisi"); return; }
        if(!metode) { alert("Metode pembayaran wajib diisi"); return; }
        if(isNaN(jumlah) || jumlah <= 0) { alert("Harga jual wajib diisi"); return; }
        if(karyawanIds.length === 0) { alert("Pilih minimal 1 karyawan"); return; }
        if(metode === 'Lainnya' && !document.getElementById('keteranganLainnya').value.trim()) {
            alert("Isi keterangan untuk metode Lainnya");
            return;
        }
        if(metode === 'Qris' && !document.getElementById('keteranganQris').value.trim()) {
            alert("Isi keterangan untuk metode Qris");
            return;
        }
        let waktu = document.getElementById('waktuAngka').value.trim();
        if(waktu === "") waktu = getCurrentTime();
        let data = getTodayData();
        data.mobil.push({
            waktu, jenis, metode, jumlah,
            keteranganQris: metode==='Qris' ? document.getElementById('keteranganQris').value : '',
            keteranganLainnya: metode==='Lainnya' ? document.getElementById('keteranganLainnya').value : '',
            karyawanIds,
            tanggal: currentDate
        });
        saveAll();
        refreshAll();
        document.getElementById('jenisWarna').value = '';
        document.getElementById('metodeMobil').value = '';
        document.getElementById('jumlahMobil').value = '';
        document.getElementById('keteranganQris').value = '';
        document.getElementById('keteranganLainnya').value = '';
        document.querySelectorAll('#checklistPengerjaan .cekPekerja').forEach(cb => cb.checked = false);
        document.getElementById('waktuAngka').value = '';
    };
    
    // ======================== LAPORAN & GRAFIK (LENGKAP) ========================
    function hitungKendaraan(daftar) {
        let mobil=0, motor=0;
        for(let t of daftar) {
            if(t.jenis && t.jenis.toLowerCase().includes('motor')) motor++;
            else mobil++;
        }
        return {mobil, motor};
    }
    
    function generateLaporan() {
        const data = getTodayData();
        const today = currentDate.split('-').reverse().join('/');
        let totalMobilKotor = data.mobil.reduce((s,m)=>s+m.jumlah,0);
        let totalTunai = data.mobil.reduce((s,m)=> m.metode==="Cash" ? s+m.jumlah : s, 0);
        let totalQris = data.mobil.reduce((s,m)=> m.metode==="Qris" ? s+m.jumlah : s, 0);
        let totalLainnya = data.mobil.reduce((s,m)=> (m.metode!=='Qris' && m.metode!=='Cash') ? s+m.jumlah : s, 0);
        let totalPengeluaran = data.pengeluaran.reduce((s,p)=>s+p.jumlah,0);
        let hasilCucian = totalMobilKotor / 2;
        let hasilAnak = totalMobilKotor / 2;
        let totalUangAnak = hasilAnak + (data.saldo.anakSebelumnya || 0);
        let pendapatanCashFinal = hasilCucian - totalQris - totalLainnya - totalPengeluaran;
        let totalParfum = data.parfum.reduce((s,p)=>s+p.total,0);
        let totalQrisParfum = data.parfum.reduce((s,p)=>p.metode==='Qris'?s+p.total:s,0);
        let totalCashParfum = totalParfum - totalQrisParfum;
        let totalJajan = data.jajan.reduce((s,j)=>s+j.total,0);
        let totalQrisJajan = data.jajan.reduce((s,j)=>j.metode==='Qris'?s+j.total:s,0);
        let totalCashJajan = totalJajan - totalQrisJajan;
        let grandTotalQris = totalQris + totalQrisParfum + totalQrisJajan;
        const {mobil: unitMobil, motor: unitMotor} = hitungKendaraan(data.mobil);
        let laporan = `📅 UPDATE HASIL TANGGAL ${today}\n━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━\n\n`;
        laporan += `📊 Jumlah Kendaraan:\n   🚗 Mobil : ${unitMobil} unit\n   🏍️ Motor : ${unitMotor} unit\n\n`;
        laporan += `2. Total pendapatan cuci : ${formatRupiah(totalMobilKotor)}\n`;
        laporan += `3. Pembayaran Qris (Cuci) : ${formatRupiah(totalQris)}\n`;
        laporan += `4. Pembayaran Tunai/Cash : ${formatRupiah(totalTunai)}\n`;
        laporan += `5. Pembayaran Lainnya : ${formatRupiah(totalLainnya)}\n`;
        laporan += `6. Pengeluaran : ${formatRupiah(totalPengeluaran)}\n`;
        laporan += `7. Hasil cucian (50%) : ${formatRupiah(hasilCucian)}\n`;
        laporan += `8. Hasil Anak anak (50%) : ${formatRupiah(hasilAnak)}\n`;
        laporan += `9. Total uang anak anak : ${formatRupiah(totalUangAnak)}\n`;
        laporan += `10. Total uang cucian cash : ${formatRupiah(pendapatanCashFinal)}\n\n`;
        laporan += `━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━\n`;
        laporan += `💰 PENDAPATAN CASH FINAL 💰\n`;
        let label1 = "   Pendapatan tunai (50%)";
        let label2 = "   Pendapatan qris";
        let label3 = "   Pendapatan lainnya";
        let label4 = "   Pengeluaran";
        let maxLen = Math.max(label1.length, label2.length, label3.length, label4.length);
        let formatLine = (label, value) => {
            let spaces = " ".repeat(maxLen - label.length);
            return `${label}${spaces} : ${value}`;
        };
        laporan += formatLine(label1, formatRupiah(hasilCucian)) + "\n";
        laporan += formatLine(label2, formatRupiah(totalQris)) + "\n";
        laporan += formatLine(label3, formatRupiah(totalLainnya)) + "\n";
        laporan += formatLine(label4, formatRupiah(totalPengeluaran)) + "\n";
        let garis = "   " + " ".repeat(maxLen - 3) + "   ______________";
        laporan += garis + "\n";
        let hasil = hasilCucian - totalQris - totalLainnya - totalPengeluaran;
        let hasilLabel = "   HASIL";
        let spacesHasil = " ".repeat(maxLen - hasilLabel.length);
        laporan += `${hasilLabel}${spacesHasil} : ${formatRupiah(hasil)}\n`;
        laporan += `\n━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━\n🛍️ Parfum\n   Pendapatan cash parfum : ${formatRupiah(totalCashParfum)}\n   Pendapatan Qris parfum : ${formatRupiah(totalQrisParfum)}\n`;
        laporan += `\n━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━\n🍿 Jajan\n   Pendapatan cash jajan : ${formatRupiah(totalCashJajan)}\n   Pendapatan Qris jajan : ${formatRupiah(totalQrisJajan)}\n`;
        laporan += `\n━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━\n💰 RINGKASAN QRIS\n   🟢 QRIS Cuci    : ${formatRupiah(totalQris)}\n   🟢 QRIS Parfum  : ${formatRupiah(totalQrisParfum)}\n   🟢 QRIS Jajan   : ${formatRupiah(totalQrisJajan)}\n   ➕ TOTAL QRIS   : ${formatRupiah(grandTotalQris)}\n`;
        return laporan;
    }
    
    function renderLaporan() {
        document.getElementById('laporanContent').innerHTML = generateLaporan().replace(/\n/g,'<br>');
    }
    
    let chartJajan, chartParfum, chartMobilMotor, chartPendapatan;
    function renderGrafik() {
        let now = new Date(currentDate);
        let tahun = now.getFullYear();
        let bulan = now.getMonth();
        let hariAkhir = new Date(tahun, bulan+1, 0).getDate();
        let labels = [];
        for(let i=1; i<=hariAkhir; i++) labels.push(i.toString());
        let dataJajan = new Array(hariAkhir).fill(0);
        let dataParfum = new Array(hariAkhir).fill(0);
        let dataMobil = new Array(hariAkhir).fill(0);
        let dataMotor = new Array(hariAkhir).fill(0);
        let dataPendapatanMobil = new Array(hariAkhir).fill(0);
        let dataPendapatanMotor = new Array(hariAkhir).fill(0);
        let dataTotalPendapatan = new Array(hariAkhir).fill(0);
        for(let tgl in allData) {
            let date = new Date(tgl);
            if(date.getFullYear() === tahun && date.getMonth() === bulan) {
                let day = date.getDate() - 1;
                let dayData = allData[tgl];
                let totalJajan = dayData.jajan.reduce((s,j)=>s+j.total,0);
                let totalParfum = dayData.parfum.reduce((s,p)=>s+p.total,0);
                dataJajan[day] += totalJajan;
                dataParfum[day] += totalParfum;
                let countMobil=0, countMotor=0, pendapatanMobil=0, pendapatanMotor=0;
                for(let m of dayData.mobil) {
                    if(m.jenis && m.jenis.toLowerCase().includes('motor')) {
                        countMotor++;
                        pendapatanMotor += m.jumlah;
                    } else {
                        countMobil++;
                        pendapatanMobil += m.jumlah;
                    }
                }
                dataMobil[day] += countMobil;
                dataMotor[day] += countMotor;
                dataPendapatanMobil[day] += pendapatanMobil;
                dataPendapatanMotor[day] += pendapatanMotor;
                dataTotalPendapatan[day] += pendapatanMobil + pendapatanMotor + totalJajan + totalParfum;
            }
        }
        if(chartJajan) chartJajan.destroy();
        if(chartParfum) chartParfum.destroy();
        if(chartMobilMotor) chartMobilMotor.destroy();
        if(chartPendapatan) chartPendapatan.destroy();
        chartJajan = new Chart(document.getElementById('grafikJajan'), {
            type: 'line', data: { labels, datasets: [{ label: 'Penjualan Jajan (Rp)', data: dataJajan, borderColor: 'orange', fill: false }] }
        });
        chartParfum = new Chart(document.getElementById('grafikParfum'), {
            type: 'line', data: { labels, datasets: [{ label: 'Penjualan Parfum (Rp)', data: dataParfum, borderColor: 'purple', fill: false }] }
        });
        chartMobilMotor = new Chart(document.getElementById('grafikMobilMotor'), {
            type: 'line', data: { labels, datasets: [
                { label: 'Jumlah Mobil', data: dataMobil, borderColor: 'blue', fill: false },
                { label: 'Jumlah Motor', data: dataMotor, borderColor: 'red', fill: false }
            ]}
        });
        chartPendapatan = new Chart(document.getElementById('grafikPendapatan'), {
            type: 'line', data: { labels, datasets: [
                { label: 'Pendapatan Mobil', data: dataPendapatanMobil, borderColor: 'green', fill: false },
                { label: 'Pendapatan Motor', data: dataPendapatanMotor, borderColor: 'orange', fill: false },
                { label: 'Total Pendapatan', data: dataTotalPendapatan, borderColor: 'black', fill: false }
            ]}
        });
    }
    
    // ======================== TABEL GAJI (LENGKAP DARI KODE ASLI) ========================
    function renderTabelGajiPerTanggal() {
        let now = new Date(currentDate);
        let tahun = now.getFullYear();
        let bulan = now.getMonth();
        let tglAwal = new Date(tahun, bulan, 1);
        let tglAkhir = new Date(tahun, bulan+1, 0);
        if(masterKaryawan.length === 0) {
            document.getElementById('tabelGajiPerTanggal').innerHTML = '<p>Belum ada karyawan</p>';
            return;
        }
        let dates = [];
        let cur = new Date(tglAwal);
        while(cur <= tglAkhir) {
            dates.push(formatLocalDate(cur));
            cur.setDate(cur.getDate() + 1);
        }
        let html = '<table><thead><tr><th>Karyawan</th>';
        for(let t of dates) {
            let tglNum = parseInt(t.split('-')[2]);
            html += `<th>${tglNum}</th>`;
        }
        html += `<th>Total Bulan</th></tr></thead><tbody>`;
        let grandTotal = 0;
        for(let k of masterKaryawan) {
            html += `<tr><td>${k.nama}</td>`;
            let totalKaryawan = 0;
            for(let t of dates) {
                let gaji = getGajiKaryawanPerHari(k.id, t);
                totalKaryawan += gaji;
                html += `<td>${formatRupiah(gaji)}</td>`;
            }
            grandTotal += totalKaryawan;
            html += `<td><strong>${formatRupiah(totalKaryawan)}</strong></td></tr>`;
        }
        html += `<tr class="total-row"><td><strong>TOTAL SEMUA</strong></td>`;
        for(let t of dates) {
            let totalHari = 0;
            for(let k of masterKaryawan) totalHari += getGajiKaryawanPerHari(k.id, t);
            html += `<td><strong>${formatRupiah(totalHari)}</strong></td>`;
        }
        html += `<td><strong>${formatRupiah(grandTotal)}</strong></td></tr>`;
        html += `</tbody></table>`;
        document.getElementById('tabelGajiPerTanggal').innerHTML = html;
    }
    
    function getMingguIni(tanggal) {
        let d = new Date(tanggal);
        let day = d.getDay();
        let mingguMulai = new Date(d);
        mingguMulai.setDate(d.getDate() - day);
        let mingguSelesai = new Date(mingguMulai);
        mingguSelesai.setDate(mingguMulai.getDate() + 6);
        return { 
            mulai: formatLocalDate(mingguMulai), 
            selesai: formatLocalDate(mingguSelesai) 
        };
    }
    
    function renderTabelGajiMingguanIni() {
        let { mulai, selesai } = getMingguIni(currentDate);
        let days = ['Minggu', 'Senin', 'Selasa', 'Rabu', 'Kamis', 'Jumat', 'Sabtu'];
        let dates = [];
        let cur = new Date(mulai);
        for(let i=0; i<7; i++) {
            dates.push(formatLocalDate(cur));
            cur.setDate(cur.getDate() + 1);
        }
        if(masterKaryawan.length === 0) {
            document.getElementById('tabelGajiMingguanIni').innerHTML = '<p>Belum ada karyawan</p>';
            return;
        }
        let html = '<table><thead><tr><th>Karyawan</th>';
        for(let i=0; i<7; i++) html += `<th>${days[i]}<br>(${dates[i]})</th>`;
        html += `<th>Total Gaji</th><th>Kasbon</th><th>Total Setelah Kasbon</th></tr></thead><tbody>`;
        let totalAllGaji = 0, totalAllKasbon = 0, totalAllSetelah = 0;
        for(let k of masterKaryawan) {
            let totalGaji = 0;
            let daily = [];
            for(let i=0; i<7; i++) {
                let gaji = getGajiKaryawanPerHari(k.id, dates[i]);
                daily.push(gaji);
                totalGaji += gaji;
            }
            let kasbon = getTotalKasbonPerKaryawanDalamPeriode(k.id, mulai, selesai);
            let setelah = totalGaji - kasbon;
            totalAllGaji += totalGaji;
            totalAllKasbon += kasbon;
            totalAllSetelah += setelah;
            html += `<tr><td>${k.nama}</td>`;
            for(let i=0; i<7; i++) html += `<td>${formatRupiah(daily[i])}</td>`;
            html += `<td><strong>${formatRupiah(totalGaji)}</strong></td><td>${formatRupiah(kasbon)}</td><td>${formatRupiah(setelah)}</td></tr>`;
        }
        html += `<tr class="total-row"><td><strong>TOTAL</strong></td>`;
        for(let i=0; i<7; i++) {
            let totalHari = 0;
            for(let k of masterKaryawan) totalHari += getGajiKaryawanPerHari(k.id, dates[i]);
            html += `<td><strong>${formatRupiah(totalHari)}</strong></td>`;
        }
        html += `<td><strong>${formatRupiah(totalAllGaji)}</strong></td><td><strong>${formatRupiah(totalAllKasbon)}</strong></td><td><strong>${formatRupiah(totalAllSetelah)}</strong></td></tr>`;
        html += `</tbody></table>`;
        document.getElementById('tabelGajiMingguanIni').innerHTML = html;
    }
    
    function renderDaftarKaryawan() {
        let html = '<table><thead><tr><th>Nama</th><th>Status Saat Ini</th><th>Aksi</th></tr></thead><tbody>';
        for(let k of masterKaryawan) {
            let status = getKaryawanStatusAtDate(k.id, currentDate);
            let badge = status === 'Staf' ? 'staf-badge' : 'training-badge';
            html += `<tr>
                <td>${k.nama}</td>
                <td><span class="${badge}">${status}</span></td>
                <td>
                    <button class="btn-delete" onclick="hapusKaryawan('${k.id}')">Hapus</button>
                    ${status === 'Training' ? `<button class="btn-promote" onclick="naikkanJabatan('${k.id}', '${k.nama}')">Naik ke Staf</button>` : ''}
                </td>
            </tr>`;
        }
        html += `</tbody></table>`;
        document.getElementById('daftarKaryawanTabel').innerHTML = html;
        updateCeklis();
    }
    
    window.hapusKaryawan = (id) => {
        if(confirm('Hapus karyawan ini?')) {
            masterKaryawan = masterKaryawan.filter(k => k.id !== id);
            karyawanStatusHistory = karyawanStatusHistory.filter(h => h.karyawanId !== id);
            for(let tgl in allData) {
                if(allData[tgl].mobil) {
                    allData[tgl].mobil.forEach(m => {
                        if(m.karyawanIds) m.karyawanIds = m.karyawanIds.filter(kid => kid !== id);
                    });
                }
            }
            saveAll();
            refreshAll();
        }
    };
    
    window.naikkanJabatan = (id, nama) => {
        if(confirm(`Naikkan ${nama} dari Training ke Staf mulai hari ini (${currentDate})?`)) {
            karyawanStatusHistory.push({ karyawanId: id, tanggalMulai: currentDate, status: "Staf" });
            saveAll();
            refreshAll();
            alert(`${nama} sekarang Staf mulai ${currentDate}`);
        }
    };
    
    function updateCeklis() {
        let container = document.getElementById('checklistPengerjaan');
        if(!container) return;
        if(masterKaryawan.length === 0) { container.innerHTML = '<i>Belum ada karyawan</i>'; return; }
        let html = '';
        for(let k of masterKaryawan) {
            let status = getKaryawanStatusAtDate(k.id, currentDate);
            html += `<label><input type="checkbox" class="cekPekerja" value="${k.id}"> ${k.nama} (${status})</label>`;
        }
        container.innerHTML = html;
    }
    
    function hitungGajiHarian() {
        let data = getTodayData();
        let map = new Map();
        for(let k of masterKaryawan) {
            let status = getKaryawanStatusAtDate(k.id, currentDate);
            map.set(k.id, { total:0, nama:k.nama, status:status, detail:[] });
        }
        for(let m of data.mobil) {
            if(!m.karyawanIds) continue;
            let gajiPerId = hitungGajiPerMobil(m.jumlah, m.karyawanIds, currentDate);
            for(let [id, gaji] of Object.entries(gajiPerId)) {
                if(map.has(id)) {
                    let emp = map.get(id);
                    emp.total += gaji;
                    emp.detail.push({ jenis: m.jenis, harga: m.jumlah, gaji });
                }
            }
        }
        return Array.from(map.values());
    }
    
    function renderGajiHarian() {
        let list = hitungGajiHarian();
        let html = '<table><thead><tr><th>Nama</th><th>Status</th><th>Gaji Hari Ini</th><th>Detail</th></tr></thead><tbody>';
        for(let g of list) {
            let badge = g.status === 'Staf' ? 'staf-badge' : 'training-badge';
            let detail = g.detail.map(d => `${d.jenis}: ${formatRupiah(d.gaji)}`).join('<br>');
            html += `<tr>
                <td>${g.nama}</td>
                <td><span class="${badge}">${g.status}</span></td>
                <td><strong>${formatRupiah(g.total)}</strong></td>
                <td>${detail}</td>
            </tr>`;
        }
        html += `</tbody></table>`;
        document.getElementById('gajiHarianContainer').innerHTML = html;
    }
    
    function renderKasbonList() {
        let data = getTodayData();
        let totalKasbon = {};
        data.kasbon.forEach(kb => { totalKasbon[kb.idKaryawan] = (totalKasbon[kb.idKaryawan]||0) + kb.jumlah; });
        let html = '<table><thead><tr><th>Karyawan</th><th>Kasbon (hari ini)</th><th>Aksi</th></tr></thead><tbody>';
        for(let k of masterKaryawan) {
            let tot = totalKasbon[k.id] || 0;
            html += `<tr>
                <td>${k.nama}</td>
                <td>${formatRupiah(tot)}</td>
                <td><button class="btn-small" onclick="tambahKasbon('${k.id}','${k.nama}')">Tambah</button> <button class="btn-small" onclick="editKasbon('${k.id}','${k.nama}',${tot})">Edit</button></td>
            </tr>`;
        }
        html += `</tbody></table>`;
        document.getElementById('kasbonList').innerHTML = html;
    }
    
    window.tambahKasbon = (id, nama) => {
        let jml = prompt(`Kasbon untuk ${nama}:`);
        if(jml && !isNaN(parseInt(jml))) {
            let data = getTodayData();
            data.kasbon.push({ idKaryawan: id, jumlah: parseInt(jml), tanggal: currentDate });
            saveAll();
            refreshAll();
        }
    };
    
    window.editKasbon = (id, nama, current) => {
        let newVal = prompt(`Edit kasbon ${nama}:`, current);
        if(newVal !== null && !isNaN(parseInt(newVal))) {
            let data = getTodayData();
            data.kasbon = data.kasbon.filter(kb => kb.idKaryawan !== id);
            if(parseInt(newVal) !== 0) data.kasbon.push({ idKaryawan: id, jumlah: parseInt(newVal), tanggal: currentDate });
            saveAll();
            refreshAll();
        }
    };
    
    // ======================== MASTER STOK (DENGAN TOTAL NILAI) ========================
    function renderMasterStok() {
        let htmlJajan = '<table><thead><tr><th>Nama</th><th>Harga</th><th>Stok</th><th>Total Nilai</th><th>Aksi</th></tr></thead><tbody>';
        masterJajan.forEach((item, idx) => {
            let totalNilai = item.harga * item.stok;
            htmlJajan += `<tr>
                <td>${item.nama}</td>
                <td>${formatRupiah(item.harga)}</td>
                <td>${item.stok}</td>
                <td>${formatRupiah(totalNilai)}</td>
                <td><button class="btn-small" onclick="editMaster('jajan',${idx})">✏️ Edit</button> <button class="btn-delete" onclick="hapusMaster('jajan',${idx})">🗑️ Hapus</button></td>
            </tr>`;
        });
        htmlJajan += `</tbody></tr><button class="btn-add" onclick="tambahMaster('jajan')">+ Tambah Jajan</button>`;
        document.getElementById('masterJajanList').innerHTML = htmlJajan;
        
        let htmlParfum = '<table><thead><tr><th>Nama</th><th>Harga</th><th>Stok</th><th>Total Nilai</th><th>Aksi</th></tr></thead><tbody>';
        masterParfum.forEach((item, idx) => {
            let totalNilai = item.harga * item.stok;
            htmlParfum += `<tr>
                <td>${item.nama}</td>
                <td>${formatRupiah(item.harga)}</td>
                <td>${item.stok}</td>
                <td>${formatRupiah(totalNilai)}</td>
                <td><button class="btn-small" onclick="editMaster('parfum',${idx})">✏️ Edit</button> <button class="btn-delete" onclick="hapusMaster('parfum',${idx})">🗑️ Hapus</button></td>
            </tr>`;
        });
        htmlParfum += `</tbody></table><button class="btn-add" onclick="tambahMaster('parfum')">+ Tambah Parfum</button>`;
        document.getElementById('masterParfumList').innerHTML = htmlParfum;
    }
    
    window.tambahMaster = (type) => {
        let nama = prompt("Nama barang:");
        if(!nama) return;
        let harga = parseInt(prompt("Harga satuan:"));
        if(isNaN(harga)) return;
        let stok = parseInt(prompt("Stok awal:"));
        if(isNaN(stok)) stok = 0;
        let newId = Date.now()+Math.random();
        let item = { id: newId.toString(), nama, harga, stok };
        if(type === 'jajan') masterJajan.push(item);
        else masterParfum.push(item);
        saveAll();
        renderMasterStok();
    };
    
    window.editMaster = (type, idx) => {
        let item = type==='jajan' ? masterJajan[idx] : masterParfum[idx];
        let newNama = prompt("Nama baru:", item.nama);
        if(newNama) item.nama = newNama;
        let newHarga = parseInt(prompt("Harga baru:", item.harga));
        if(!isNaN(newHarga)) item.harga = newHarga;
        let newStok = parseInt(prompt("Stok baru:", item.stok));
        if(!isNaN(newStok)) item.stok = newStok;
        saveAll();
        renderMasterStok();
    };
    
    window.hapusMaster = (type, idx) => {
        if(confirm("Hapus item?")) {
            if(type==='jajan') masterJajan.splice(idx,1);
            else masterParfum.splice(idx,1);
            saveAll();
            renderMasterStok();
        }
    };
    
    // ======================== TAMBAH KARYAWAN ========================
    document.getElementById('btnTambahKaryawanForm').onclick = () => {
        let nama = prompt("Nama karyawan:");
        if(nama) {
            let statusAwal = confirm("OK = Staf, Cancel = Training") ? "Staf" : "Training";
            let newId = Date.now() + Math.random() + "";
            masterKaryawan.push({ id: newId, nama });
            karyawanStatusHistory.push({ karyawanId: newId, tanggalMulai: currentDate, status: statusAwal });
            saveAll();
            refreshAll();
        }
    };
    
    // ======================== REFRESH SEMUA ========================
    function refreshAll() {
        renderTabelGabungan();
        renderDaftarKaryawan();
        renderGajiHarian();
        renderKasbonList();
        renderLaporan();
        renderGrafik();
        renderMasterStok();
        renderTabelGajiPerTanggal();
        renderTabelGajiMingguanIni();
        updateCeklis();
    }
    
    // ======================== EVENT LISTENER ========================
    document.getElementById('menuIcon').onclick = () => document.getElementById('sideMenu').style.left = '0';
    document.getElementById('closeMenu').onclick = () => document.getElementById('sideMenu').style.left = '-260px';
    document.querySelectorAll('.side-menu a').forEach(link => {
        link.onclick = (e) => {
            e.preventDefault();
            let pageId = link.getAttribute('data-page');
            document.querySelectorAll('.page').forEach(p => p.classList.remove('active-page'));
            document.getElementById(pageId).classList.add('active-page');
            document.getElementById('sideMenu').style.left = '-260px';
            if(pageId === 'page4') renderMasterStok();
            if(pageId === 'page2') renderGrafik();
            if(pageId === 'page3') {
                renderTabelGajiPerTanggal();
                renderTabelGajiMingguanIni();
            }
        };
    });
    document.getElementById('tanggalGlobal').value = currentDate;
    document.getElementById('tanggalGlobal').addEventListener('change', (e) => {
        currentDate = e.target.value;
        refreshAll();
    });
    document.getElementById('btnUpdateHasil').onclick = () => renderLaporan();
    document.getElementById('btnTambahJajanCart').onclick = () => openCartModal('jajan');
    document.getElementById('btnTambahParfumCart').onclick = () => openCartModal('parfum');
    document.getElementById('btnTambahKasbonUtama').onclick = () => {
        if(masterKaryawan.length) tambahKasbon(masterKaryawan[0].id, masterKaryawan[0].nama);
        else alert("Tambah karyawan dulu");
    };
    document.getElementById('metodeMobil').addEventListener('change', function() {
        document.getElementById('qrisKeteranganGroup').classList.toggle('hidden', this.value !== 'Qris');
        document.getElementById('lainnyaKeteranganGroup').classList.toggle('hidden', this.value !== 'Lainnya');
    });
    document.getElementById('closeEditModal').onclick = () => document.getElementById('editModal').style.display = 'none';
    window.onclick = (e) => { 
        if(e.target === document.getElementById('editModal')) document.getElementById('editModal').style.display = 'none';
        if(e.target === document.getElementById('cartModal')) document.getElementById('cartModal').style.display = 'none';
    };
    
    loadAll();
    refreshAll();
</script>
</body>
</html>
