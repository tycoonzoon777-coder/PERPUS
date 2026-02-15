<!DOCTYPE html>
<html lang="id">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>DigiLib - Integrated Library System</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Inter:wght@300;400;600;700;800&display=swap');
        body { font-family: 'Inter', sans-serif; transition: background 0.3s; }
        .hidden { display: none !important; }
        
        @keyframes fadeIn { from { opacity: 0; } to { opacity: 1; } }
        @keyframes popIn { 
            from { opacity: 0; transform: scale(0.9) translateY(20px); } 
            to { opacity: 1; transform: scale(1) translateY(0); } 
        }
        .animate-fadeIn { animation: fadeIn 0.3s ease-out; }
        .animate-popIn { animation: popIn 0.4s cubic-bezier(0.175, 0.885, 0.32, 1.275); }
    </style>
</head>
<body id="bodyTag" class="bg-gray-50 text-gray-800">

    <div id="customModal" class="hidden fixed inset-0 z-[999] flex items-center justify-center bg-black/60 backdrop-blur-sm p-4 animate-fadeIn">
        <div class="bg-white rounded-[2.5rem] p-8 max-w-sm w-full shadow-2xl transform transition-all animate-popIn">
            <div class="flex flex-col items-center text-center">
                <div id="modalIcon" class="w-20 h-20 rounded-full flex items-center justify-center text-4xl mb-6 shadow-inner"></div>
                <h3 id="modalTag" class="text-[10px] font-black uppercase tracking-[0.2em] mb-2"></h3>
                <h2 id="modalTitle" class="text-xl font-extrabold text-slate-800 leading-tight mb-4"></h2>
                <p id="modalMessage" class="text-slate-500 text-sm leading-relaxed mb-8"></p>
                <button onclick="closeModal()" class="w-full bg-slate-900 text-white p-4 rounded-2xl font-bold hover:bg-indigo-600 transition-all active:scale-95 shadow-lg shadow-slate-200 uppercase tracking-wider text-xs">Tutup</button>
            </div>
        </div>
    </div>

    <nav id="navBar" class="hidden p-4 text-white shadow-lg transition-colors duration-500 bg-blue-600">
        <div class="container mx-auto flex justify-between items-center">
            <h1 class="text-xl font-bold flex items-center gap-2">
                <span id="brandIcon">📚</span> DigiLib Kampus
            </h1>
            <div class="flex items-center gap-4">
                <div id="statusBadge" class="hidden bg-red-500 text-[10px] px-2 py-1 rounded-md font-black animate-pulse">AKUN TERBLOKIR (DENDA)</div>
                <div class="flex items-center bg-black bg-opacity-20 rounded-full px-3 py-1 border border-white border-opacity-30">
                    <span class="text-[10px] md:text-xs mr-2 font-bold uppercase tracking-wider" id="modeLabel">Mode Mahasiswa</span>
                    <label class="relative inline-flex items-center cursor-pointer">
                        <input type="checkbox" id="modeToggle" class="sr-only peer" onchange="switchMode()">
                        <div class="w-9 h-5 bg-gray-300 peer-focus:outline-none rounded-full peer peer-checked:after:translate-x-full peer-checked:after:border-white after:content-[''] after:absolute after:top-[2px] after:left-[2px] after:bg-white after:border-gray-300 after:border after:rounded-full after:h-4 after:w-4 after:transition-all peer-checked:bg-yellow-400"></div>
                    </label>
                </div>
                <div id="userInfo" class="text-sm border-l pl-4 border-white border-opacity-30">
                    <span id="userName" class="font-semibold italic">User</span> | 
                    <button onclick="location.reload()" class="hover:underline font-bold">Logout</button>
                </div>
            </div>
        </div>
    </nav>

    <div id="loginSection" class="container mx-auto mt-20 max-w-md bg-white p-8 rounded-2xl shadow-2xl border border-gray-100">
        <div class="text-center mb-8">
            <div class="text-5xl mb-4">📖</div>
            <h2 class="text-2xl font-black text-gray-800">Selamat Datang</h2>
            <p class="text-gray-500 text-sm">Integrasi Sistem Akademik & Keuangan</p>
        </div>
        <div class="space-y-4">
            <input type="text" id="nim" value="21040101" placeholder="NIM" class="w-full p-3 border rounded-xl focus:ring-2 focus:ring-blue-500 outline-none">
            <input type="password" id="pass" value="password" placeholder="Password" class="w-full p-3 border rounded-xl focus:ring-2 focus:ring-blue-500 outline-none">
            <button onclick="prosesLogin()" class="w-full bg-blue-600 text-white p-4 rounded-xl font-bold hover:bg-blue-700 transform active:scale-95 transition shadow-lg">MASUK KE SISTEM</button>
        </div>
        <p class="mt-6 text-[10px] text-center text-gray-400 uppercase tracking-widest font-bold">Tanpa Jaminan KTP - Terkoneksi Data Mahasiswa</p>
    </div>

    <main id="userContent" class="hidden container mx-auto mt-10 px-4 pb-20">
        <div id="penaltyAlert" class="hidden mb-8 bg-red-50 border-2 border-red-200 p-4 rounded-2xl flex justify-between items-center">
            <div>
                <h4 class="text-red-700 font-bold text-sm">Peringatan Sistem Keuangan!</h4>
                <p class="text-red-600 text-xs">Anda memiliki denda yang belum dibayar. Akses peminjaman diblokir sementara.</p>
            </div>
            <div class="text-right">
                <span id="totalPenaltyText" class="block text-xl font-black text-red-700">Rp 0</span>
                <button class="text-[10px] bg-red-600 text-white px-3 py-1 rounded-full font-bold uppercase mt-1">Bayar via Virtual Account</button>
            </div>
        </div>

        <h2 class="text-2xl font-bold border-l-4 border-blue-600 pl-3 uppercase mb-6">Katalog Buku</h2>
        <div id="bookGrid" class="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-4 gap-6 mb-12"></div>

        <h2 class="text-2xl font-bold border-l-4 border-orange-500 pl-3 uppercase mb-6 text-orange-600">Buku Yang Sedang Anda Pinjam</h2>
        <div id="borrowedGrid" class="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-4 gap-6"></div>
    </main>

    <main id="adminContent" class="hidden container mx-auto mt-10 px-4 pb-20">
        <div class="flex justify-between items-end mb-6">
            <h2 class="text-2xl font-bold border-l-4 border-red-600 pl-3 uppercase text-red-700">Monitor Real-Time & Denda</h2>
            <div class="bg-gray-100 p-2 rounded-lg text-[10px] font-bold text-gray-500 uppercase">Tarif Denda: Rp 2.000 / Hari</div>
        </div>
        
        <div class="bg-white rounded-2xl shadow-xl overflow-hidden border border-gray-100">
            <table class="w-full text-left text-sm">
                <thead class="bg-gray-800 text-white">
                    <tr>
                        <th class="p-4 text-xs font-bold uppercase">Mahasiswa</th>
                        <th class="p-4 text-xs font-bold uppercase text-center">Jatuh Tempo</th>
                        <th class="p-4 text-xs font-bold uppercase text-center">Keterlambatan</th>
                        <th class="p-4 text-xs font-bold uppercase text-center">Denda Sistem</th>
                        <th class="p-4 text-xs font-bold uppercase text-right">Status Akun</th>
                    </tr>
                </thead>
                <tbody class="divide-y divide-gray-100" id="adminTableBody">
                    </tbody>
            </table>
        </div>
    </main>

    <script>
        // Data Dummy
        const books = [
            { id: 1, judul: "Arsitektur IoT", penulis: "Reny Azraeny", status: "Tersedia", cat: "IoT" },
            { id: 2, judul: "Pemrograman Web Laravel", penulis: "Luluk Suryani", status: "Tersedia", cat: "Web Dev" },
            { id: 3, judul: "UI/UX Design Modern", penulis: "Siska Amelia", status: "Tersedia", cat: "Design" },
            { id: 4, judul: "Kecerdasan Buatan (AI)", penulis: "Budi Santoso", status: "Tersedia", cat: "AI" }
        ];

        // Simulasi Data Mahasiswa Terintegrasi
        const studentData = [
            { nama: "Isak Homer", nim: "12345", tglTempo: "2026-02-10", kembali: false },
            { nama: "Edom Howay", nim: "22334", tglTempo: "2026-02-14", kembali: false },
            { nama: "Sandra Saweki", nim: "22335", tglTempo: "2026-02-15", kembali: true },
            { nama: "Alif Siregar", nim: "21040101", tglTempo: "2026-02-18", kembali: false } // User yang login
        ];

        let borrowedBooks = []; 
        let userPenalty = 0;
        let isBlocked = false;
        const dendaPerHari = 2000;

        function prosesLogin() {
            const nimInput = document.getElementById('nim').value;
            if(nimInput.trim() !== "") {
                document.getElementById('loginSection').classList.add('hidden');
                document.getElementById('navBar').classList.remove('hidden');
                document.getElementById('userContent').classList.remove('hidden');
                document.getElementById('userName').innerText = nimInput;
                
                checkUserStatus(nimInput);
                renderBooks();
                renderAdminTable();
            }
        }

        function checkUserStatus(nim) {
            // Simulasi pengecekan ke sistem keuangan & akademik
            const today = new Date("2026-02-15"); // Simulasi tanggal hari ini
            const userLoan = studentData.find(s => s.nim === nim);
            
            if (userLoan && !userLoan.kembali) {
                const tempo = new Date(userLoan.tglTempo);
                const diffTime = today - tempo;
                const diffDays = Math.ceil(diffTime / (1000 * 60 * 60 * 24));
                
                if (diffDays > 0) {
                    userPenalty = diffDays * dendaPerHari;
                    isBlocked = true;
                    document.getElementById('penaltyAlert').classList.remove('hidden');
                    document.getElementById('totalPenaltyText').innerText = `Rp ${userPenalty.toLocaleString()}`;
                    document.getElementById('statusBadge').classList.remove('hidden');
                }

                // Simulasi Fitur Notifikasi Otomatis H-2
                if (diffDays >= -2 && diffDays <= 0) {
                    showModal('INFO', 'NOTIFIKASI EMAIL', 'Pengingat Otomatis', 'Email pengingat H-2 telah dikirim ke alamat terdaftar Anda.', 'bg-yellow-100 text-yellow-600');
                }
            }
        }

        function switchMode() {
            const isChecked = document.getElementById('modeToggle').checked;
            document.getElementById('userContent').classList.toggle('hidden', isChecked);
            document.getElementById('adminContent').classList.toggle('hidden', !isChecked);
            document.getElementById('navBar').classList.replace(isChecked ? 'bg-blue-600' : 'bg-red-800', isChecked ? 'bg-red-800' : 'bg-blue-600');
            document.getElementById('modeLabel').innerText = isChecked ? "Mode Admin" : "Mode Mahasiswa";
            if(isChecked) renderAdminTable();
        }

        function renderBooks() {
            const gridAvailable = document.getElementById('bookGrid');
            gridAvailable.innerHTML = books.filter(b => b.status === "Tersedia").map(book => `
                <div class="bg-white p-6 rounded-2xl shadow-sm border border-gray-100 hover:shadow-xl transition-all">
                    <span class="text-[9px] font-black px-2 py-1 rounded bg-gray-100 text-gray-400 uppercase tracking-widest">${book.cat}</span>
                    <h3 class="text-lg font-bold mt-2 leading-tight h-12">${book.judul}</h3>
                    <p class="text-xs text-gray-500 mb-4 italic">Oleh: ${book.penulis}</p>
                    <button onclick="pinjamBuku(${book.id})" ${isBlocked ? 'disabled' : ''} 
                        class="w-full ${isBlocked ? 'bg-gray-300 cursor-not-allowed' : 'bg-blue-600 hover:bg-blue-700'} text-white py-3 rounded-xl font-bold text-xs transition">
                        ${isBlocked ? 'AKSES DIBLOKIR' : 'PINJAM BUKU'}
                    </button>
                </div>
            `).join('');
        }

        function renderAdminTable() {
            const tbody = document.getElementById('adminTableBody');
            const today = new Date("2026-02-15");

            tbody.innerHTML = studentData.map(s => {
                const tempo = new Date(s.tglTempo);
                const diffTime = today - tempo;
                const diffDays = Math.ceil(diffTime / (1000 * 60 * 60 * 24));
                const denda = (diffDays > 0 && !s.kembali) ? diffDays * dendaPerHari : 0;
                
                return `
                    <tr class="${denda > 0 ? 'bg-red-50' : 'hover:bg-gray-50'}">
                        <td class="p-4 font-bold text-slate-700">${s.nama}<br><span class="text-[10px] text-gray-400 font-normal">${s.nim}</span></td>
                        <td class="p-4 text-center text-gray-500 font-mono">${s.tglTempo}</td>
                        <td class="p-4 text-center ${denda > 0 ? 'text-red-600 font-bold' : 'text-gray-400'}">
                            ${s.kembali ? '-' : (diffDays > 0 ? diffDays + ' Hari' : 'Lancar')}
                        </td>
                        <td class="p-4 text-center font-black ${denda > 0 ? 'text-red-600' : 'text-green-600'}">
                            Rp ${denda.toLocaleString()}
                        </td>
                        <td class="p-4 text-right">
                            ${denda > 0 ? 
                                '<span class="bg-red-600 text-white px-2 py-1 rounded text-[9px] font-black uppercase">Sistem Diblokir</span>' : 
                                '<span class="bg-green-100 text-green-600 px-2 py-1 rounded text-[9px] font-black uppercase">Aktif</span>'}
                        </td>
                    </tr>
                `;
            }).join('');
        }

        function pinjamBuku(id) {
            if(isBlocked) return;
            const book = books.find(b => b.id === id);
            book.status = "Dipinjam";
            borrowedBooks.push(book);
            renderBooks();
            renderBorrowed();
            showModal('SUKSES', 'SISTEM AKADEMIK', 'Berhasil Pinjam', `Data peminjaman "${book.judul}" otomatis tercatat di akun mahasiswa Anda. Tanpa perlu KTP.`, 'bg-green-100 text-green-600');
        }

        function renderBorrowed() {
            const grid = document.getElementById('borrowedGrid');
            if (borrowedBooks.length === 0) {
                grid.innerHTML = `<p class="col-span-full text-gray-400 italic text-sm">Anda belum meminjam buku apapun.</p>`;
                return;
            }
            grid.innerHTML = borrowedBooks.map(book => `
                <div class="bg-orange-50 p-6 rounded-2xl border border-orange-200 shadow-sm relative overflow-hidden">
                    <div class="absolute top-0 right-0 bg-orange-500 text-white px-3 py-1 text-[8px] font-bold">MILIK ANDA</div>
                    <span class="text-[9px] font-black px-2 py-1 rounded bg-white text-orange-400 uppercase tracking-widest">${book.cat}</span>
                    <h3 class="text-lg font-bold mt-2 leading-tight h-12 text-slate-800">${book.judul}</h3>
                    <button onclick="kembalikanBuku(${book.id})" class="w-full bg-white border border-orange-500 text-orange-600 py-2 rounded-xl font-bold text-xs hover:bg-orange-500 hover:text-white transition">KEMBALIKAN</button>
                </div>
            `).join('');
        }

        function kembalikanBuku(id) {
            const book = books.find(b => b.id === id);
            book.status = "Tersedia";
            borrowedBooks = borrowedBooks.filter(b => b.id !== id);
            renderBooks();
            renderBorrowed();
            showModal('INFO', 'PENGEMBALIAN', 'Selesai', 'Data peminjaman telah diperbarui di server pusat.', 'bg-blue-100 text-blue-600');
        }

        function showModal(type, tag, title, msg, colorClass) {
            const modal = document.getElementById('customModal');
            document.getElementById('modalIcon').className = `w-20 h-20 rounded-full flex items-center justify-center text-4xl mb-6 shadow-inner ${colorClass}`;
            document.getElementById('modalIcon').innerText = type === 'SUKSES' ? '✓' : 'ℹ';
            document.getElementById('modalTag').className = `text-[10px] font-black uppercase tracking-[0.2em] mb-2 ${colorClass.split(' ')[1]}`;
            document.getElementById('modalTag').innerText = tag;
            document.getElementById('modalTitle').innerText = title;
            document.getElementById('modalMessage').innerHTML = msg;
            modal.classList.remove('hidden');
        }

        function closeModal() {
            document.getElementById('customModal').classList.add('hidden');
        }
    </script>
</body>
</html>
