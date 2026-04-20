<!DOCTYPE html>
<html lang="id">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>SDN 22 Tanah Keras</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <link href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.0.0/css/all.min.css" rel="stylesheet">
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Inter:wght@300;400;500;600;700&display=swap');
        body { font-family: 'Inter', sans-serif; background-color: #f8fafc; }
        .glass-effect { background: rgba(255, 255, 255, 0.9); backdrop-filter: blur(10px); }
        .login-gradient { background: linear-gradient(135deg, #4f46e5 0%, #7c3aed 100%); }
        .hide-scroll::-webkit-scrollbar { display: none; }
        @media print {
            @page { size: 330mm 215mm; margin: 10mm; }
            body { -webkit-print-color-adjust: exact; }
        }
        .loading-overlay { position: fixed; inset: 0; background: rgba(255,255,255,0.8); z-index: 9999; display: flex; align-items: center; justify-content: center; flex-direction: column; gap: 1rem; }
        
        /* Iframe for print without popup */
        #print-frame { position: absolute; width: 0; height: 0; border: none; visibility: hidden; }
    </style>
</head>
<body class="text-slate-800">

    <!-- HIDDEN IFRAME FOR PRINTING -->
    <iframe id="print-frame"></iframe>

    <!-- LOADING OVERLAY -->
    <div id="loading-overlay" class="loading-overlay hidden">
        <div class="w-12 h-12 border-4 border-indigo-600 border-t-transparent rounded-full animate-spin"></div>
        <p class="font-bold text-indigo-600 animate-pulse">Sedang Singkron </p>
    </div>

    <!-- LOGIN PAGE -->
    <div id="login-page" class="min-h-screen flex items-center justify-center p-4 login-gradient">
        <div class="bg-white w-full max-w-md rounded-3xl shadow-2xl overflow-hidden">
            <div class="p-8 text-center bg-slate-50 border-b">
                <div class="mb-4 mx-auto">
                    <img src="https://iili.io/3b1SedN.png" alt="Logo AbsenKU" class="h-28 w-auto mx-auto object-contain drop-shadow-md">
                </div>
                <h2 class="text-2xl font-bold text-slate-800">PRESENSI ONLINE</h2>
                <p class="text-slate-500 text-sm">Sistem Absensi Kehadiran</p>
            </div>
            <div class="flex border-b">
                <button onclick="switchLoginForm('pegawai')" id="tab-login-pegawai" class="flex-1 py-4 text-sm font-bold border-b-2 border-indigo-600 text-indigo-600 transition">Pegawai</button>
                <button onclick="switchLoginForm('admin')" id="tab-login-admin" class="hidden md:flex flex-1 py-4 text-sm font-bold border-b-2 border-transparent text-slate-400 hover:text-slate-600 transition items-center justify-center">Admin</button>
            </div>
            <div class="p-8">
                <div id="login-error" class="hidden mb-4 p-3 bg-red-50 text-red-600 text-xs font-bold rounded-xl border border-red-100 flex items-center gap-2"><i class="fas fa-exclamation-circle"></i> Username atau sandi salah!</div>
                <form id="form-pegawai" onsubmit="handleLogin(event, 'pegawai')" class="space-y-5">
                    <div class="space-y-1">
                        <label class="text-xs font-bold text-slate-500 uppercase">Username Pegawai</label>
                        <div class="relative">
                            <i class="fas fa-user absolute left-4 top-1/2 -translate-y-1/2 text-slate-400"></i>
                            <input type="text" id="p-user" placeholder="Username (NIP/NUPT)" required class="w-full pl-11 pr-4 py-3 bg-slate-50 border border-slate-200 rounded-xl focus:ring-2 focus:ring-indigo-500 outline-none transition">
                        </div>
                    </div>
                    <div class="space-y-1">
                        <label class="text-xs font-bold text-slate-500 uppercase">Kata Sandi</label>
                        <div class="relative">
                            <i class="fas fa-lock absolute left-4 top-1/2 -translate-y-1/2 text-slate-400"></i>
                            <input type="password" id="p-pass" placeholder="••••••••" required class="w-full pl-11 pr-4 py-3 bg-slate-50 border border-slate-200 rounded-xl focus:ring-2 focus:ring-indigo-500 outline-none transition">
                        </div>
                    </div>
                    <button type="submit" class="w-full py-4 bg-indigo-600 text-white rounded-xl font-bold shadow-lg shadow-indigo-200 hover:bg-indigo-700 transition-all active:scale-[0.98]">Masuk Pegawai</button>
                </form>
                <form id="form-admin" onsubmit="handleLogin(event, 'admin')" class="hidden space-y-5">
                    <div class="space-y-1">
                        <label class="text-xs font-bold text-slate-500 uppercase">Username Admin</label>
                        <div class="relative">
                            <i class="fas fa-user-shield absolute left-4 top-1/2 -translate-y-1/2 text-slate-400"></i>
                            <input type="text" id="a-user" placeholder="admin" required class="w-full pl-11 pr-4 py-3 bg-slate-50 border border-slate-200 rounded-xl focus:ring-2 focus:ring-purple-500 outline-none transition">
                        </div>
                    </div>
                    <div class="space-y-1">
                        <label class="text-xs font-bold text-slate-500 uppercase">Kode Akses</label>
                        <div class="relative">
                            <i class="fas fa-key absolute left-4 top-1/2 -translate-y-1/2 text-slate-400"></i>
                            <input type="password" id="a-pass" placeholder="12345" required class="w-full pl-11 pr-4 py-3 bg-slate-50 border border-slate-200 rounded-xl focus:ring-2 focus:ring-purple-500 outline-none transition">
                        </div>
                    </div>
                    <button type="submit" class="w-full py-4 bg-purple-600 text-white rounded-xl font-bold shadow-lg shadow-purple-200 hover:bg-purple-700 transition-all active:scale-[0.98]">Login Admin</button>
                </form>
            </div>
        </div>
    </div>

    <!-- MAIN APP -->
    <div id="main-app" class="hidden min-h-screen flex flex-col md:flex-row">
        <!-- SIDEBAR -->
        <aside class="hidden md:flex w-64 bg-indigo-700 text-white flex-col sticky top-0 h-screen">
            <div class="p-6"><h1 class="text-2xl font-bold flex items-center gap-2"><i class="fas fa-fingerprint"></i> AbsenKU</h1></div>
            <nav class="flex-1 px-4 space-y-2">
                <button onclick="switchTab('dashboard')" id="side-dashboard" class="tab-btn w-full flex items-center gap-3 p-3 rounded-lg hover:bg-indigo-600 transition"><i class="fas fa-home"></i> Beranda</button>
                <button onclick="switchTab('attendance')" id="side-attendance" class="tab-btn w-full flex items-center gap-3 p-3 rounded-lg hover:bg-indigo-600 transition"><i class="fas fa-clock"></i> Absensi</button>
                <button onclick="switchTab('history')" id="side-history" class="tab-btn w-full flex items-center gap-3 p-3 rounded-lg hover:bg-indigo-600 transition"><i class="fas fa-history"></i> Riwayat</button>
                <button onclick="switchTab('manage')" id="side-manage" class="hidden tab-btn w-full flex items-center gap-3 p-3 rounded-lg hover:bg-indigo-600 transition"><i class="fas fa-users"></i> Kelola Pegawai</button>
                <button onclick="switchTab('pic')" id="side-pic" class="hidden tab-btn w-full flex items-center gap-3 p-3 rounded-lg hover:bg-indigo-600 transition"><i class="fas fa-user-shield"></i> Penanggung Jawab</button>
                <button onclick="switchTab('kop')" id="side-kop" class="hidden tab-btn w-full flex items-center gap-3 p-3 rounded-lg hover:bg-indigo-600 transition"><i class="fas fa-file-invoice"></i> Pengaturan Kop Surat</button>
                <button onclick="switchTab('profile')" id="side-profile" class="tab-btn w-full flex items-center gap-3 p-3 rounded-lg hover:bg-indigo-600 transition"><i class="fas fa-user-circle"></i> Profil</button>
                
                <!-- TOMBOL SINGKRON ADMIN -->
                <button onclick="loadFromCloud()" id="side-sync" class="hidden w-full flex items-center gap-3 p-3 rounded-lg bg-emerald-600 hover:bg-emerald-500 transition shadow-inner font-bold"><i class="fas fa-sync-alt"></i> Singkronkan</button>
            </nav>
            <div class="p-4 mt-auto border-t border-indigo-500">
                <div class="flex items-center gap-3">
                    <img id="side-avatar" src="" class="w-10 h-10 rounded-full border-2 border-white object-cover">
                    <div class="overflow-hidden"><p id="display-name" class="text-sm font-semibold truncate">-</p><p id="user-role-label" class="text-[10px] font-bold uppercase opacity-70">-</p></div>
                </div>
            </div>
        </aside>

        <!-- HEADER MOBILE -->
        <header class="md:hidden glass-effect sticky top-0 z-50 px-5 py-4 border-b flex justify-between items-center bg-white/80">
            <div><p id="mobile-greeting" class="text-[10px] font-bold text-indigo-600 uppercase tracking-widest">Memuat...</p><h1 id="mobile-display-name" class="text-lg font-bold text-slate-800 truncate max-w-[150px]">-</h1></div>
            <div class="text-right"><div id="clock-mobile" class="text-sm font-mono font-bold text-indigo-700">00:00:00</div><div id="date-mobile" class="text-[9px] text-slate-400 font-medium uppercase tracking-tighter">Memuat Tanggal...</div></div>
        </header>

        <!-- MAIN CONTENT -->
        <main class="flex-1 flex flex-col h-full overflow-y-auto pb-24 md:pb-0">
            <header class="hidden md:flex items-center justify-between p-6 bg-white border-b">
                <div><h2 id="tab-title" class="text-xl font-bold">Beranda</h2><p id="greeting" class="text-sm text-slate-500">Selamat bekerja!</p></div>
                <div class="text-right"><div id="clock-desktop" class="text-2xl font-mono font-bold text-indigo-600">00:00:00</div><div id="date-now" class="text-sm text-slate-400">Senin, 1 Jan 2024</div></div>
            </header>

            <div class="p-5 md:p-8 space-y-6">
                <!-- DASHBOARD -->
                <section id="tab-dashboard" class="space-y-6">
                    <div class="grid grid-cols-2 md:grid-cols-3 gap-3">
                        <div class="bg-white p-4 rounded-2xl shadow-sm border border-slate-100 flex items-center gap-3"><div class="w-10 h-10 bg-green-100 text-green-600 rounded-xl flex items-center justify-center text-lg"><i class="fas fa-check-circle"></i></div><div><p class="text-[9px] text-slate-400 uppercase font-bold">Hadir</p><p class="text-lg font-bold" id="stats-hadir">0</p></div></div>
                        <div class="bg-white p-4 rounded-2xl shadow-sm border border-slate-100 flex items-center gap-3"><div class="w-10 h-10 bg-orange-100 text-orange-600 rounded-xl flex items-center justify-center text-lg"><i class="fas fa-clock"></i></div><div><p class="text-[9px] text-slate-400 uppercase font-bold">Telat</p><p class="text-lg font-bold" id="stats-late">0</p></div></div>
                    </div>
                </section>

                <!-- TAB: ATTENDANCE -->
                <section id="tab-attendance" class="hidden space-y-6">
                    <div class="bg-white p-8 rounded-3xl shadow-lg border border-slate-100 text-center space-y-6">
                        <div class="bg-amber-50 border border-amber-200 p-4 rounded-2xl flex items-center gap-3 text-left">
                            <div class="w-10 h-10 bg-amber-100 text-amber-600 rounded-full flex items-center justify-center flex-shrink-0"><i class="fas fa-location-arrow"></i></div>
                            <p class="text-xs font-medium text-amber-800 leading-relaxed"><span class="block font-bold mb-0.5">Petunjuk Absensi:</span>Pastikan Aktifkan GPS Handphone (HP) Anda sebelum menekan tombol ambil absen untuk verifikasi lokasi.</p>
                        </div>
                        <div class="space-y-2">
                            <h3 class="text-2xl font-bold text-slate-800">Panel Presensi</h3>
                            <p class="text-slate-500 text-xs italic"><i class="fas fa-map-marker-alt text-red-500"></i> <a href="https://www.google.com/maps?q=-1.2942335988142928,100.52147339164873" target="_blank" class="hover:text-indigo-600 transition underline decoration-dotted">UPT SDN 22 Tanah Keras</a></p>
                        </div>
                        <div id="label-status" class="px-6 py-2 inline-block rounded-full bg-slate-100 text-slate-500 text-sm font-semibold">Memeriksa Status...</div>
                        <div id="location-info" class="hidden text-xs text-indigo-600 bg-indigo-50 p-2 rounded-lg animate-pulse">Memverifikasi GPS...</div>
                        
                        <div class="flex flex-col gap-4 max-w-lg mx-auto">
                            <!-- Tombol Standar Pegawai -->
                            <div class="grid grid-cols-1 md:grid-cols-2 gap-4">
                                <button id="btn-in" onclick="startAbsenceProcess('Masuk')" class="group px-8 py-6 bg-indigo-600 text-white rounded-2xl font-bold hover:bg-indigo-700 transition flex flex-col items-center justify-center gap-2 active:scale-95 shadow-lg shadow-indigo-100"><i class="fas fa-sign-in-alt text-2xl"></i><span>Absen Masuk</span></button>
                                <button id="btn-out" onclick="startAbsenceProcess('Pulang')" disabled class="group px-8 py-6 bg-slate-200 text-slate-400 cursor-not-allowed rounded-2xl font-bold transition flex flex-col items-center justify-center gap-2"><i class="fas fa-sign-out-alt text-2xl"></i><span>Absen Pulang</span></button>
                            </div>

                            <!-- Tombol Khusus Admin: Presensi Manual -->
                            <div id="admin-manual-area" class="hidden pt-4 border-t border-slate-100">
                                <button onclick="openManualAbsenModal()" class="w-full py-4 bg-emerald-600 text-white rounded-2xl font-bold shadow-lg shadow-emerald-100 hover:bg-emerald-700 transition flex items-center justify-center gap-3">
                                    <i class="fas fa-user-edit text-xl"></i>
                                    <span>Input Presensi Manual (Admin)</span>
                                </button>
                            </div>
                        </div>
                    </div>
                </section>

                <!-- TAB: HISTORY -->
                <section id="tab-history" class="hidden space-y-4">
                    <div class="flex flex-col md:flex-row md:items-center justify-between gap-4">
                        <h3 class="font-bold text-lg text-slate-800">Riwayat Kehadiran</h3>
                        <div id="print-controls-admin" class="hidden flex flex-wrap items-center gap-2">
                            <input type="text" id="filter-name" oninput="renderHistory()" placeholder="Filter Nama" class="p-2 bg-white border rounded-xl text-xs w-28 focus:ring-2 focus:ring-indigo-500 outline-none">
                            <input type="text" id="filter-date-table" oninput="renderHistory()" placeholder="Filter Hari/Tgl" class="p-2 bg-white border rounded-xl text-xs w-28 focus:ring-2 focus:ring-indigo-500 outline-none">
                            
                            <input type="text" id="print-place" placeholder="Tempat Cetak" class="p-2 bg-white border rounded-xl text-xs w-32 focus:ring-2 focus:ring-indigo-500 outline-none">
                            <input type="text" id="print-date-manual" placeholder="Tanggal Cetak (Manual)" class="p-2 bg-white border rounded-xl text-xs w-40 focus:ring-2 focus:ring-indigo-500 outline-none">
                            
                            <button onclick="printReport()" class="p-3 bg-emerald-600 text-white rounded-xl font-bold flex items-center gap-2 transition hover:bg-emerald-700 active:scale-95"><i class="fas fa-print"></i> Cetak (F4)</button>
                        </div>
                    </div>
                    <div class="bg-white rounded-2xl shadow-sm border border-slate-100 overflow-hidden">
                        <div class="overflow-x-auto">
                            <table class="w-full text-left min-w-[900px]">
                                <thead class="bg-slate-50 text-[10px] text-slate-400 uppercase font-bold"><tr id="history-header"></tr></thead>
                                <tbody id="history-body" class="text-sm divide-y divide-slate-100"></tbody>
                            </table>
                        </div>
                    </div>
                </section>

                <!-- TAB: MANAGE PEGAWAI -->
                <section id="tab-manage" class="hidden space-y-6">
                    <div class="flex items-center justify-between"><h3 class="font-bold text-lg">Daftar Pegawai</h3><button onclick="openAddEmployeeModal()" class="p-3 bg-indigo-600 text-white rounded-xl font-bold"><i class="fas fa-plus"></i> Tambah Pegawai</button></div>
                    <div class="bg-white rounded-2xl shadow-sm border border-slate-100 overflow-hidden">
                        <div class="overflow-x-auto">
                            <table class="w-full text-left">
                                <thead class="bg-slate-50 text-[10px] text-slate-400 uppercase font-bold"><tr><th class="p-4">Nama</th><th class="p-4">NIP/NUPT</th><th class="p-4">Status</th><th class="p-4 text-center">Aksi</th></tr></thead>
                                <tbody id="employee-list-body" class="text-xs divide-y divide-slate-100"></tbody>
                            </table>
                        </div>
                    </div>
                </section>

                <!-- TAB: PIC -->
                <section id="tab-pic" class="hidden space-y-6">
                    <div class="bg-white p-8 rounded-3xl shadow-sm border border-slate-100">
                        <h3 class="text-xl font-bold text-slate-800 mb-6 flex items-center gap-2"><i class="fas fa-user-shield text-indigo-600"></i> Pengaturan Penanggung Jawab</h3>
                        <form onsubmit="handleSavePIC(event)" class="space-y-8">
                            <div class="grid grid-cols-1 md:grid-cols-2 gap-6">
                                <div class="space-y-4 p-5 bg-slate-50 rounded-2xl border border-slate-200">
                                    <h4 class="text-sm font-bold text-slate-500 uppercase">Pengawas</h4>
                                    <input type="text" id="pic-pengawas-nama" placeholder="Nama Lengkap Pengawas" class="w-full p-3 bg-white border border-slate-200 rounded-xl outline-none text-sm">
                                    <input type="text" id="pic-pengawas-nip" placeholder="NIP/NUPT Pengawas" class="w-full p-3 bg-white border border-slate-200 rounded-xl outline-none text-sm">
                                </div>
                                <div class="space-y-4 p-5 bg-slate-50 rounded-2xl border border-slate-200">
                                    <h4 class="text-sm font-bold text-slate-500 uppercase">Kepala Sekolah</h4>
                                    <input type="text" id="pic-kepsek-nama" placeholder="Nama Kepala Sekolah" class="w-full p-3 bg-white border border-slate-200 rounded-xl outline-none text-sm">
                                    <input type="text" id="pic-kepsek-nip" placeholder="NIP/NUPT Kepala Sekolah" class="w-full p-3 bg-white border border-slate-200 rounded-xl outline-none text-sm">
                                </div>
                            </div>
                            <button type="submit" class="w-full py-4 bg-indigo-600 text-white rounded-xl font-bold">Simpan Data Penanggung Jawab</button>
                        </form>
                    </div>
                </section>

                <!-- TAB: KOP SURAT -->
                <section id="tab-kop" class="hidden space-y-6">
                    <div class="bg-white p-8 rounded-3xl shadow-sm border border-slate-100">
                        <h3 class="text-xl font-bold text-slate-800 mb-6 flex items-center gap-2"><i class="fas fa-file-invoice text-indigo-600"></i> Pengaturan Kop Surat</h3>
                        <form onsubmit="handleSaveKop(event)" class="space-y-6">
                            <div class="grid grid-cols-1 md:grid-cols-2 gap-6">
                                <input type="text" id="kop-logo-kiri" placeholder="URL Logo Kiri" class="w-full p-3 bg-white border rounded-xl text-sm">
                                <input type="text" id="kop-logo-kanan" placeholder="URL Logo Kanan" class="w-full p-3 bg-white border rounded-xl text-sm">
                                <input type="text" id="kop-pemda" placeholder="Nama Pemerintah Daerah" class="w-full p-3 bg-white border rounded-xl text-sm">
                                <input type="text" id="kop-dinas" placeholder="Nama Dinas" class="w-full p-3 bg-white border rounded-xl text-sm">
                                <input type="text" id="kop-sekolah" placeholder="Nama Sekolah" class="w-full p-3 bg-white border rounded-xl text-sm">
                                <input type="text" id="kop-kecamatan" placeholder="Kecamatan" class="w-full p-3 bg-white border rounded-xl text-sm">
                                <input type="text" id="kop-alamat" placeholder="Alamat Lengkap" class="w-full p-3 bg-white border rounded-xl text-sm">
                                <input type="text" id="kop-email" placeholder="Email" class="w-full p-3 bg-white border rounded-xl text-sm">
                                <input type="text" id="kop-website" placeholder="Website" class="w-full p-3 bg-white border rounded-xl text-sm">
                            </div>
                            <button type="submit" class="w-full py-4 bg-indigo-600 text-white rounded-2xl font-bold">Simpan Pengaturan Kop Surat</button>
                        </form>
                    </div>
                </section>

                <!-- TAB: PROFILE -->
                <section id="tab-profile" class="hidden space-y-6">
                    <div class="bg-white p-6 rounded-3xl shadow-sm border border-slate-100 text-center">
                        <img id="profile-avatar" src="" class="w-24 h-24 mx-auto rounded-full mb-4 object-cover shadow-md">
                        <h3 id="profile-name" class="text-xl font-bold text-slate-800">-</h3>
                        <p id="profile-role-text" class="text-slate-500 text-sm mb-6">-</p>
                        <div class="grid grid-cols-2 gap-3 text-left">
                            <div class="bg-slate-50 p-3 rounded-2xl"><p class="text-[9px] font-bold text-slate-400 uppercase">NIP/NUPT</p><p id="profile-nip" class="text-xs font-bold text-slate-700">-</p></div>
                            <div class="bg-slate-50 p-3 rounded-2xl"><p class="text-[9px] font-bold text-slate-400 uppercase">Status</p><p id="profile-status" class="text-xs font-bold text-slate-700">-</p></div>
                        </div>
                    </div>
                    <button onclick="handleLogout()" class="w-full p-4 text-red-600 font-bold bg-white border border-red-100 rounded-2xl hover:bg-red-50 transition">Logout</button>
                </section>
            </div>
        </main>

        <!-- BOTTOM NAV MOBILE -->
        <nav id="bottom-nav" class="md:hidden fixed bottom-0 left-0 right-0 bg-white/90 backdrop-blur-md border-t flex justify-around p-3 z-50">
            <button id="nav-btn-dashboard" onclick="switchTab('dashboard')" class="mobile-nav-btn flex flex-col items-center gap-1 text-indigo-600"><i class="fas fa-th-large text-xl"></i><span class="text-[9px] font-bold">Home</span></button>
            <button id="nav-btn-attendance" onclick="switchTab('attendance')" class="mobile-nav-btn flex flex-col items-center gap-1 text-slate-400"><i class="fas fa-camera text-xl"></i><span class="text-[9px] font-bold">Absen</span></button>
            <button id="nav-btn-history" onclick="switchTab('history')" class="mobile-nav-btn flex flex-col items-center gap-1 text-slate-400"><i class="fas fa-history text-xl"></i><span class="text-[9px] font-bold">Riwayat</span></button>
            <button id="nav-btn-profile" onclick="switchTab('profile')" class="mobile-nav-btn flex flex-col items-center gap-1 text-slate-400"><i class="fas fa-user text-xl"></i><span class="text-[9px] font-bold">Profil</span></button>
        </nav>
    </div>

    <!-- MODAL PEGAWAI -->
    <div id="modal-add-employee" class="fixed inset-0 bg-black/50 z-[200] hidden flex items-center justify-center p-4">
        <div class="bg-white rounded-3xl p-6 w-full max-w-md space-y-4 shadow-2xl overflow-y-auto max-h-[90vh]">
            <h4 class="text-lg font-bold">Tambah Pegawai</h4>
            <form id="form-add-employee" onsubmit="addEmployee(event)" class="space-y-3">
                <input type="text" id="new-name" required placeholder="Nama Lengkap" class="w-full p-3 bg-slate-50 border rounded-xl text-sm">
                <input type="text" id="new-nip" required placeholder="NIP/NUPT" class="w-full p-3 bg-slate-50 border rounded-xl text-sm" oninput="syncUsername(this.value)">
                <input type="text" id="new-photo" placeholder="URL Foto Pegawai (Opsional)" class="w-full p-3 bg-slate-50 border rounded-xl text-sm">
                <select id="new-status" required class="w-full p-3 bg-slate-50 border rounded-xl text-sm">
                    <option value="PNS">PNS</option><option value="PPPK">PPPK</option><option value="PPPK PW">PPPK PW</option><option value="Honor">Honor</option><option value="Kontrak">Kontrak</option>
                </select>
                <input type="text" id="new-jabatan" required placeholder="Jabatan" class="w-full p-3 bg-slate-50 border rounded-xl text-sm">
                <div class="grid grid-cols-2 gap-2">
                    <input type="text" id="new-user" required placeholder="Username" class="w-full p-3 bg-slate-50 border rounded-xl text-sm">
                    <input type="password" id="new-pass" required placeholder="Password" class="w-full p-3 bg-slate-50 border rounded-xl text-sm">
                </div>
                <div class="flex gap-2">
                    <button type="button" onclick="closeAddEmployeeModal()" class="flex-1 py-3 bg-slate-100 rounded-xl font-bold">Batal</button>
                    <button type="submit" class="flex-1 py-3 bg-indigo-600 text-white rounded-xl font-bold">Simpan</button>
                </div>
            </form>
        </div>
    </div>

    <!-- MODAL EDIT RIWAYAT -->
    <div id="modal-edit-history" class="fixed inset-0 bg-black/50 z-[200] hidden flex items-center justify-center p-4">
        <div class="bg-white rounded-3xl p-6 w-full max-w-md space-y-4 shadow-2xl overflow-y-auto max-h-[90vh]">
            <h4 class="text-lg font-bold">Edit Riwayat Absensi</h4>
            <form id="form-edit-history" onsubmit="handleUpdateHistory(event)" class="space-y-3">
                <input type="hidden" id="edit-history-index">
                <div class="space-y-1">
                    <label class="text-[10px] font-bold text-slate-400 uppercase">Nama Lengkap</label>
                    <input type="text" id="edit-name" required class="w-full p-3 bg-slate-50 border rounded-xl text-sm">
                </div>
                <div class="space-y-1">
                    <label class="text-[10px] font-bold text-slate-400 uppercase">NIP/NUPT</label>
                    <input type="text" id="edit-nip" required class="w-full p-3 bg-slate-50 border rounded-xl text-sm">
                </div>
                <div class="space-y-1">
                    <label class="text-[10px] font-bold text-slate-400 uppercase">Hari & Tanggal</label>
                    <input type="text" id="edit-date" required class="w-full p-3 bg-slate-50 border rounded-xl text-sm">
                </div>
                <div class="grid grid-cols-2 gap-3">
                    <div class="space-y-1">
                        <label class="text-[10px] font-bold text-slate-400 uppercase">Masuk</label>
                        <input type="text" id="edit-in" class="w-full p-3 bg-slate-50 border rounded-xl text-sm">
                    </div>
                    <div class="space-y-1">
                        <label class="text-[10px] font-bold text-slate-400 uppercase">Pulang</label>
                        <input type="text" id="edit-out" class="w-full p-3 bg-slate-50 border rounded-xl text-sm">
                    </div>
                </div>
                <div class="space-y-1">
                    <label class="text-[10px] font-bold text-slate-400 uppercase">Keterangan Status</label>
                    <select id="edit-status" class="w-full p-3 bg-slate-50 border rounded-xl text-sm">
                        <option value="Hadir">Hadir</option>
                        <option value="Terlambat">Terlambat</option>
                        <option value="Izin">Izin</option>
                        <option value="Sakit">Sakit</option>
                        <option value="Alfa">Alfa</option>
                    </select>
                </div>
                <div class="flex gap-2 pt-2">
                    <button type="button" onclick="closeEditHistoryModal()" class="flex-1 py-3 bg-slate-100 rounded-xl font-bold transition hover:bg-slate-200">Batal</button>
                    <button type="submit" class="flex-1 py-3 bg-indigo-600 text-white rounded-xl font-bold transition hover:bg-indigo-700 shadow-lg shadow-indigo-100">Simpan Perubahan</button>
                </div>
            </form>
        </div>
    </div>

    <!-- MODAL KONFIRMASI HAPUS (KUSTOM) -->
    <div id="modal-confirm-delete" class="fixed inset-0 bg-black/50 z-[300] hidden flex items-center justify-center p-4">
        <div class="bg-white rounded-3xl p-6 w-full max-w-xs text-center space-y-4 shadow-2xl">
            <div class="text-5xl text-red-500"><i class="fas fa-trash-alt"></i></div>
            <h4 class="text-lg font-bold">Hapus Data?</h4>
            <p class="text-slate-500 text-xs">Data riwayat ini akan dihapus permanen dari sistem dan Cloud.</p>
            <div class="flex gap-2">
                <button onclick="closeConfirmDelete()" class="flex-1 py-3 bg-slate-100 rounded-xl font-bold transition">Batal</button>
                <button id="btn-actual-delete" class="flex-1 py-3 bg-red-600 text-white rounded-xl font-bold transition hover:bg-red-700">Hapus</button>
            </div>
        </div>
    </div>

    <!-- MODAL PRESENSI MANUAL ADMIN -->
    <div id="modal-manual-absen" class="fixed inset-0 bg-black/50 z-[200] hidden flex items-center justify-center p-4">
        <div class="bg-white rounded-3xl p-6 w-full max-w-lg space-y-4 shadow-2xl overflow-y-auto max-h-[90vh]">
            <div class="flex justify-between items-center border-b pb-4">
                <h4 class="text-xl font-bold text-emerald-600">Presensi Manual Pegawai</h4>
                <button onclick="closeManualAbsenModal()" class="text-slate-400 hover:text-slate-600 transition"><i class="fas fa-times text-xl"></i></button>
            </div>
            <form id="form-manual-absen" onsubmit="handleManualAbsenSubmit(event)" class="space-y-4">
                <div class="space-y-1">
                    <label class="text-xs font-bold text-slate-400 uppercase">Pilih Nama Pegawai</label>
                    <select id="manual-select-user" required onchange="onManualEmployeeChange()" class="w-full p-3 bg-slate-50 border border-slate-200 rounded-xl text-sm outline-none focus:ring-2 focus:ring-emerald-500 transition">
                        <option value="">-- Pilih Pegawai --</option>
                    </select>
                </div>

                <div class="grid grid-cols-1 md:grid-cols-2 gap-4">
                    <div class="space-y-1">
                        <label class="text-xs font-bold text-slate-400 uppercase">NIP/NUPTK (Otomatis)</label>
                        <input type="text" id="manual-nip" readonly class="w-full p-3 bg-slate-100 border border-slate-200 rounded-xl text-sm text-slate-500 cursor-not-allowed">
                    </div>
                    <div class="space-y-1">
                        <label class="text-xs font-bold text-slate-400 uppercase">Status (Otomatis)</label>
                        <input type="text" id="manual-status-pegawai" readonly class="w-full p-3 bg-slate-100 border border-slate-200 rounded-xl text-sm text-slate-500 cursor-not-allowed">
                    </div>
                </div>

                <div class="space-y-1">
                    <label class="text-xs font-bold text-slate-400 uppercase">Jabatan (Otomatis)</label>
                    <input type="text" id="manual-jabatan" readonly class="w-full p-3 bg-slate-100 border border-slate-200 rounded-xl text-sm text-slate-500 cursor-not-allowed">
                </div>

                <div class="space-y-1">
                    <label class="text-xs font-bold text-slate-400 uppercase">Hari & Tanggal (Manual)</label>
                    <input type="text" id="manual-date" required placeholder="Contoh: Sabtu, 07 Maret 2026" class="w-full p-3 bg-white border border-slate-200 rounded-xl text-sm outline-none focus:ring-2 focus:ring-emerald-500">
                </div>

                <div class="grid grid-cols-2 gap-4">
                    <div class="space-y-1">
                        <label class="text-xs font-bold text-slate-400 uppercase">Jam Masuk</label>
                        <input type="text" id="manual-in" placeholder="07:30" class="w-full p-3 bg-white border border-slate-200 rounded-xl text-sm outline-none focus:ring-2 focus:ring-emerald-500">
                    </div>
                    <div class="space-y-1">
                        <label class="text-xs font-bold text-slate-400 uppercase">Jam Keluar</label>
                        <input type="text" id="manual-out" placeholder="14:00" class="w-full p-3 bg-white border border-slate-200 rounded-xl text-sm outline-none focus:ring-2 focus:ring-emerald-500">
                    </div>
                </div>

                <div class="space-y-1">
                    <label class="text-xs font-bold text-slate-400 uppercase">Keterangan Presensi</label>
                    <select id="manual-status" required class="w-full p-3 bg-white border border-slate-200 rounded-xl text-sm outline-none focus:ring-2 focus:ring-emerald-500">
                        <option value="Hadir">Hadir</option>
                        <option value="Terlambat">Terlambat</option>
                        <option value="Izin">Izin</option>
                        <option value="Sakit">Sakit</option>
                        <option value="Alfa">Alfa</option>
                    </select>
                </div>

                <div class="flex gap-2 pt-4">
                    <button type="button" onclick="closeManualAbsenModal()" class="flex-1 py-4 bg-slate-100 text-slate-600 rounded-2xl font-bold transition hover:bg-slate-200">Batal</button>
                    <button type="submit" class="flex-1 py-4 bg-emerald-600 text-white rounded-2xl font-bold transition hover:bg-emerald-700 shadow-lg shadow-emerald-100">Simpan Data</button>
                </div>
            </form>
        </div>
    </div>

    <!-- MODAL ALERT -->
    <div id="modal-alert" class="fixed inset-0 bg-black/50 z-[400] hidden flex items-center justify-center p-4">
        <div class="bg-white rounded-3xl p-6 w-full max-w-xs text-center space-y-4 shadow-2xl">
            <div id="modal-icon" class="text-5xl text-green-500"><i class="fas fa-check-circle"></i></div>
            <h4 id="modal-title" class="text-lg font-bold">Berhasil</h4>
            <p id="modal-message" class="text-slate-500 text-xs">Selesai.</p>
            <button onclick="closeModal()" class="w-full py-3 bg-indigo-600 text-white rounded-xl font-bold transition hover:bg-indigo-700">Oke</button>
        </div>
    </div>

    <script>
        const SCRIPT_URL = "https://script.google.com/macros/s/AKfycbxBIeh0Jkq-U5zuoSV-_3YMYXJCMA6a2o19pSccoxw4L-Ios-O_accbFzPYdq7vADL6-g/exec";
        
        const OFFICE_NAME = "UPT SDN 22 Tanah Keras";
        const OFFICE_LAT = -1.2942335988142928;
        const OFFICE_LNG = 100.52147339164873;
        const MAX_DISTANCE_METER = 1000;

        let users = [];
        let attendanceData = [];
        let picData = {};
        let kopData = {};
        let currentUser = null;

        const DEFAULT_ADMIN = { 
            id: 1, name: 'Admin Utama', user: 'admin', pass: '12345', role: 'admin', dept: 'System Management', nip: 'ADMIN-001', statusPegawai: 'Administrator', jabatan: 'IT Manager', photoUrl: '' 
        };

        function cleanDate(dateString) {
            if (!dateString) return '';
            const s = String(dateString);
            if (s.includes('T') && s.includes('Z')) {
                try {
                    const d = new Date(s);
                    return d.toLocaleDateString('id-ID', {weekday: 'long', day: '2-digit', month: 'long', year: 'numeric'});
                } catch(e) {
                    return s.replace(/T\d{2}:\d{2}:\d{2}\.\d{3}Z/, '');
                }
            }
            return s;
        }

        async function syncWithCloud() {
            if (!SCRIPT_URL || SCRIPT_URL === "") return;
            const loader = document.getElementById('loading-overlay');
            loader.classList.remove('hidden');
            
            try {
                const elPlace = document.getElementById('print-place');
                const elDate = document.getElementById('print-date-manual');
                if (elPlace) kopData.tempatCetak = elPlace.value;
                if (elDate) kopData.tanggalCetak = elDate.value;

                await fetch(SCRIPT_URL, {
                    method: 'POST',
                    mode: 'no-cors',
                    cache: 'no-cache',
                    body: JSON.stringify({
                        type: "sync_all", users: users, history: attendanceData, pic: picData, kop: kopData
                    })
                });
            } catch (err) { 
                console.error("Sync Error:", err); 
            } finally { 
                loader.classList.add('hidden');
            }
        }

        async function loadFromCloud() {
            if (!SCRIPT_URL || SCRIPT_URL === "") { loadFromLocalFallback(); return; }
            const loader = document.getElementById('loading-overlay');
            loader.classList.remove('hidden');
            
            try {
                const response = await fetch(SCRIPT_URL);
                const data = await response.json();
                
                if (data.users && data.users.length > 0) users = data.users;
                else loadFromLocalFallback();

                const hasAdmin = users.some(u => u.role === 'admin' && String(u.user) === 'admin');
                if (!hasAdmin) users.push(DEFAULT_ADMIN);

                if (data.history) {
                    attendanceData = data.history.map(item => ({
                        ...item,
                        date: cleanDate(item.date)
                    }));
                }
                
                if (data.pic) picData = data.pic;
                if (data.kop) {
                    kopData = data.kop;
                    if (document.getElementById('print-place')) document.getElementById('print-place').value = kopData.tempatCetak || '';
                    if (document.getElementById('print-date-manual')) document.getElementById('print-date-manual').value = kopData.tanggalCetak || '';
                }
                
                const fDateTable = document.getElementById('filter-date-table');
                if(fDateTable) fDateTable.value = "";

                renderHistory();
                renderEmployeeList();
                loadKopDataUI();
                loadPICDataUI();
                
                if(currentUser) updateAttendanceStatus();

            } catch (err) { 
                console.error("Cloud Load Error:", err); 
                loadFromLocalFallback();
            } finally { 
                loader.classList.add('hidden');
            }
        }

        function loadFromLocalFallback() {
            const u = localStorage.getItem('absensi_users_data');
            users = u ? JSON.parse(u) : [DEFAULT_ADMIN];
            const h = localStorage.getItem('absensi_history_data');
            if (h) {
                attendanceData = JSON.parse(h).map(item => ({
                    ...item,
                    date: cleanDate(item.date)
                }));
            }
        }

        function switchLoginForm(type) {
            const isP = type === 'pegawai';
            document.getElementById('tab-login-pegawai').className = isP ? 'flex-1 py-4 text-sm font-bold border-b-2 border-indigo-600 text-indigo-600' : 'flex-1 py-4 text-sm font-bold border-b-2 border-transparent text-slate-400';
            document.getElementById('tab-login-admin').className = !isP ? 'hidden md:flex flex-1 py-4 text-sm font-bold border-b-2 border-purple-600 text-purple-600 items-center justify-center' : 'hidden md:flex flex-1 py-4 text-sm font-bold border-b-2 border-transparent text-slate-400 items-center justify-center';
            document.getElementById('form-pegawai').classList.toggle('hidden', !isP);
            document.getElementById('form-admin').classList.toggle('hidden', isP);
        }

        function handleLogin(e, role) {
            e.preventDefault();
            const uInput = role === 'admin' ? document.getElementById('a-user').value : document.getElementById('p-user').value;
            const pInput = role === 'admin' ? document.getElementById('a-pass').value : document.getElementById('p-pass').value;
            const found = users.find(x => String(x.user).toLowerCase() === String(uInput).toLowerCase() && String(x.pass) === String(pInput) && x.role === role);
            if (found) { 
                currentUser = found; initDashboard(); 
                document.getElementById('login-page').classList.add('hidden'); 
                document.getElementById('main-app').classList.remove('hidden'); 
                document.getElementById('login-error').classList.add('hidden');
            } else document.getElementById('login-error').classList.remove('hidden'); 
        }

        function initDashboard() {
            const isAdmin = currentUser.role === 'admin';
            document.getElementById('display-name').innerText = currentUser.name;
            document.getElementById('mobile-display-name').innerText = currentUser.name;
            document.getElementById('user-role-label').innerText = currentUser.role;
            document.getElementById('profile-avatar').src = currentUser.photoUrl || `https://ui-avatars.com/api/?name=${encodeURIComponent(currentUser.name)}`;
            document.getElementById('side-avatar').src = currentUser.photoUrl || `https://ui-avatars.com/api/?name=${encodeURIComponent(currentUser.name)}`;
            document.getElementById('profile-name').innerText = currentUser.name;
            document.getElementById('profile-role-text').innerText = currentUser.jabatan;
            document.getElementById('profile-nip').innerText = currentUser.nip;
            document.getElementById('profile-status').innerText = currentUser.statusPegawai;
            
            if (isAdmin) {
                ['side-dashboard', 'side-history', 'side-manage', 'side-pic', 'side-kop', 'print-controls-admin', 'admin-manual-area', 'side-sync'].forEach(id => document.getElementById(id)?.classList.remove('hidden'));
                switchTab('dashboard');
            } else {
                ['side-dashboard', 'side-history', 'side-manage', 'side-pic', 'side-kop', 'print-controls-admin', 'admin-manual-area', 'side-sync'].forEach(id => document.getElementById(id)?.classList.add('hidden'));
                switchTab('attendance');
            }
            
            renderHistory();
            if(isAdmin) renderEmployeeList();
            updateAttendanceStatus();
        }

        function updateAttendanceStatus() {
            if (!currentUser || currentUser.role === 'admin') return;
            const now = new Date();
            const dateStr = now.toLocaleDateString('id-ID', {weekday: 'long', day: '2-digit', month: 'long', year: 'numeric'});
            const todayRecord = attendanceData.find(x => x.username === currentUser.user && x.date === dateStr);
            const btnIn = document.getElementById('btn-in');
            const btnOut = document.getElementById('btn-out');
            const labelStatus = document.getElementById('label-status');
            if (!todayRecord) {
                btnIn.disabled = false;
                btnIn.className = "group px-8 py-6 bg-indigo-600 text-white rounded-2xl font-bold hover:bg-indigo-700 transition flex flex-col items-center justify-center gap-2 active:scale-95 shadow-lg shadow-indigo-100";
                btnOut.disabled = true;
                btnOut.className = "group px-8 py-6 bg-slate-200 text-slate-400 cursor-not-allowed rounded-2xl font-bold transition flex flex-col items-center justify-center gap-2";
                labelStatus.innerText = "Belum Absen Hari Ini";
                labelStatus.className = "px-6 py-2 inline-block rounded-full bg-slate-100 text-slate-500 text-sm font-semibold";
            } else if (todayRecord.out === '-') {
                btnIn.disabled = true;
                btnIn.className = "group px-8 py-6 bg-slate-200 text-slate-400 cursor-not-allowed rounded-2xl font-bold transition flex flex-col items-center justify-center gap-2";
                btnOut.disabled = false;
                btnOut.className = "group px-8 py-6 bg-orange-600 text-white rounded-2xl font-bold hover:bg-orange-700 transition flex flex-col items-center justify-center gap-2 active:scale-95 shadow-lg shadow-orange-100";
                labelStatus.innerText = "Sudah Absen Masuk";
                labelStatus.className = "px-6 py-2 inline-block rounded-full bg-indigo-100 text-indigo-600 text-sm font-semibold";
            } else {
                btnIn.disabled = true;
                btnIn.className = "group px-8 py-6 bg-slate-200 text-slate-400 cursor-not-allowed rounded-2xl font-bold transition flex flex-col items-center justify-center gap-2";
                btnOut.disabled = true;
                btnOut.className = "group px-8 py-6 bg-slate-200 text-slate-400 cursor-not-allowed rounded-2xl font-bold transition flex flex-col items-center justify-center gap-2";
                labelStatus.innerText = "Absensi Hari Ini Selesai";
                labelStatus.className = "px-6 py-2 inline-block rounded-full bg-green-100 text-green-600 text-sm font-semibold";
            }
        }

        async function startAbsenceProcess(type) {
            document.getElementById('location-info').classList.remove('hidden');
            navigator.geolocation.getCurrentPosition(pos => {
                const dist = getDistance(pos.coords.latitude, pos.coords.longitude, OFFICE_LAT, OFFICE_LNG);
                document.getElementById('location-info').classList.add('hidden');
                if (dist > MAX_DISTANCE_METER) showModal("Gagal Absen", `Lokasi tidak sesuai! Jarak: ${Math.round(dist)}m.`);
                else doAbsen(type);
            }, () => showModal("GPS Error", "Aktifkan GPS Anda."), { enableHighAccuracy: true });
        }

        async function doAbsen(type) {
            const now = new Date(); 
            const time = now.toLocaleTimeString('id-ID', {hour: '2-digit', minute: '2-digit'}); 
            const dateStr = now.toLocaleDateString('id-ID', {weekday: 'long', day: '2-digit', month: 'long', year: 'numeric'});
            if (type === 'Masuk') {
                const isLate = (now.getHours() > 7 || (now.getHours() == 7 && now.getMinutes() > 30));
                attendanceData.unshift({ 
                    name: currentUser.name, nip: currentUser.nip, statusPegawai: currentUser.statusPegawai, jabatan: currentUser.jabatan, date: dateStr, in: time, out: '-', status: isLate ? 'Terlambat' : 'Hadir', username: currentUser.user 
                });
            } else {
                const entry = attendanceData.find(x => x.username === currentUser.user && x.date === dateStr && x.out === '-');
                if(entry) entry.out = time;
                else {
                    attendanceData.unshift({ 
                        name: currentUser.name, nip: currentUser.nip, statusPegawai: currentUser.statusPegawai, jabatan: currentUser.jabatan, date: dateStr, in: '-', out: time, status: 'Hadir', username: currentUser.user 
                    });
                }
            }
            await syncWithCloud(); renderHistory(); updateAttendanceStatus(); showModal("Berhasil", `Absen ${type} tercatat.`);
        }

        function renderHistory() {
            const head = document.getElementById('history-header'); 
            const body = document.getElementById('history-body'); if(!body) return;
            if (!currentUser) return; 
            const isAdmin = currentUser.role === 'admin';
            let headerHtml = `<th class="p-4">Nama</th><th class="p-4">NIP/NUPT</th><th class="p-4">Status</th><th class="p-4">Jabatan</th><th class="p-4">Tanggal</th><th class="p-4 text-center">Masuk</th><th class="p-4 text-center">Keluar</th><th class="p-4">Ket</th>`;
            if(isAdmin) headerHtml += `<th class="p-4 text-center">Aksi</th>`;
            head.innerHTML = headerHtml;

            let displayData = isAdmin ? attendanceData : attendanceData.filter(d => d.username === currentUser.user);
            const fName = document.getElementById('filter-name')?.value.toLowerCase() || '';
            const fDate = document.getElementById('filter-date-table')?.value.toLowerCase() || '';
            if (fName) displayData = displayData.filter(d => d.name.toLowerCase().includes(fName));
            if (fDate) displayData = displayData.filter(d => cleanDate(d.date).toLowerCase().includes(fDate));

            body.innerHTML = displayData.map((i, index) => {
                let row = `<tr>
                    <td class="p-4 font-bold text-slate-700">${i.name}</td>
                    <td class="p-4 text-slate-500">${i.nip || '-'}</td>
                    <td class="p-4"><span class="px-2 py-1 bg-slate-100 rounded text-[10px] font-bold">${i.statusPegawai}</span></td>
                    <td class="p-4 text-slate-500">${i.jabatan}</td>
                    <td class="p-4 text-slate-600">${cleanDate(i.date)}</td>
                    <td class="p-4 text-center font-mono font-bold text-indigo-600">${i.in}</td>
                    <td class="p-4 text-center font-mono font-bold text-orange-600">${i.out}</td>
                    <td class="p-4 font-bold ${i.status === 'Hadir' ? 'text-green-600' : 'text-red-600'}">${i.status}</td>`;
                if(isAdmin) {
                    const realIndex = attendanceData.indexOf(i);
                    row += `<td class="p-4 text-center">
                        <div class="flex items-center justify-center gap-2">
                            <button onclick="openEditHistoryModal(${realIndex})" class="p-2 bg-indigo-50 text-indigo-600 rounded-lg hover:bg-indigo-100 transition shadow-sm" title="Edit"><i class="fas fa-edit"></i></button>
                            <button onclick="confirmDeleteHistory(${realIndex})" class="p-2 bg-red-50 text-red-600 rounded-lg hover:bg-red-100 transition shadow-sm" title="Hapus"><i class="fas fa-trash-alt"></i></button>
                        </div>
                    </td>`;
                }
                row += `</tr>`;
                return row;
            }).join('');
            const statsEl = document.getElementById('stats-hadir'); if (statsEl) statsEl.innerText = displayData.length;
        }

        function confirmDeleteHistory(index) {
            const modal = document.getElementById('modal-confirm-delete');
            const btnDelete = document.getElementById('btn-actual-delete');
            modal.classList.remove('hidden');
            btnDelete.onclick = () => deleteHistory(index);
        }

        function closeConfirmDelete() {
            document.getElementById('modal-confirm-delete').classList.add('hidden');
        }

        async function deleteHistory(index) {
            attendanceData.splice(index, 1);
            await syncWithCloud();
            renderHistory();
            closeConfirmDelete();
            showModal("Berhasil", "Data riwayat absensi telah dihapus.");
        }

        function openManualAbsenModal() {
            const selectUser = document.getElementById('manual-select-user');
            selectUser.innerHTML = '<option value="">-- Pilih Pegawai --</option>';
            users.filter(u => u.role === 'pegawai').forEach(emp => {
                const opt = document.createElement('option');
                opt.value = emp.id;
                opt.textContent = emp.name;
                selectUser.appendChild(opt);
            });
            const now = new Date();
            document.getElementById('manual-date').value = now.toLocaleDateString('id-ID', {weekday: 'long', day: '2-digit', month: 'long', year: 'numeric'});
            document.getElementById('manual-in').value = "07:30";
            document.getElementById('manual-out').value = "14:00";
            document.getElementById('manual-nip').value = "";
            document.getElementById('manual-status-pegawai').value = "";
            document.getElementById('manual-jabatan').value = "";
            document.getElementById('modal-manual-absen').classList.remove('hidden');
        }

        function onManualEmployeeChange() {
            const userId = document.getElementById('manual-select-user').value;
            const emp = users.find(u => String(u.id) === String(userId));
            if(emp) {
                document.getElementById('manual-nip').value = emp.nip || '-';
                document.getElementById('manual-status-pegawai').value = emp.statusPegawai || '-';
                document.getElementById('manual-jabatan').value = emp.jabatan || '-';
            }
        }

        function closeManualAbsenModal() { document.getElementById('modal-manual-absen').classList.add('hidden'); }

        async function handleManualAbsenSubmit(e) {
            e.preventDefault();
            const userId = document.getElementById('manual-select-user').value;
            const emp = users.find(u => String(u.id) === String(userId));
            if(!emp) { showModal("Error", "Silakan pilih pegawai."); return; }
            const manualEntry = {
                name: emp.name, nip: emp.nip, statusPegawai: emp.statusPegawai, jabatan: emp.jabatan, date: document.getElementById('manual-date').value, in: document.getElementById('manual-in').value || '-', out: document.getElementById('manual-out').value || '-', status: document.getElementById('manual-status').value, username: emp.user
            };
            attendanceData.unshift(manualEntry);
            await syncWithCloud(); renderHistory(); closeManualAbsenModal(); showModal("Berhasil", "Data presensi manual telah disimpan.");
        }

        function openEditHistoryModal(index) {
            const data = attendanceData[index];
            document.getElementById('edit-history-index').value = index;
            document.getElementById('edit-name').value = data.name;
            document.getElementById('edit-nip').value = data.nip || '';
            document.getElementById('edit-date').value = cleanDate(data.date);
            document.getElementById('edit-in').value = data.in;
            document.getElementById('edit-out').value = data.out;
            document.getElementById('edit-status').value = data.status;
            document.getElementById('modal-edit-history').classList.remove('hidden');
        }

        function closeEditHistoryModal() { document.getElementById('modal-edit-history').classList.add('hidden'); }

        async function handleUpdateHistory(e) {
            e.preventDefault();
            const index = document.getElementById('edit-history-index').value;
            attendanceData[index] = {
                ...attendanceData[index],
                name: document.getElementById('edit-name').value,
                nip: document.getElementById('edit-nip').value,
                date: cleanDate(document.getElementById('edit-date').value),
                in: document.getElementById('edit-in').value,
                out: document.getElementById('edit-out').value,
                status: document.getElementById('edit-status').value
            };
            await syncWithCloud(); renderHistory(); closeEditHistoryModal(); showModal("Berhasil", "Data riwayat diperbarui.");
        }

        function getDistance(lat1, lon1, lat2, lon2) {
            const R = 6371e3; const dLat = (lat2 - lat1) * Math.PI / 180; const dLon = (lon2 - lon1) * Math.PI / 180;
            const a = Math.sin(dLat / 2) * Math.sin(dLat / 2) + Math.cos(lat1 * Math.PI / 180) * Math.cos(lat2 * Math.PI / 180) * Math.sin(dLon / 2) * Math.sin(dLon / 2);
            return R * 2 * Math.atan2(Math.sqrt(a), Math.sqrt(1 - a));
        }

        function switchTab(name) {
            ['dashboard', 'attendance', 'history', 'manage', 'pic', 'kop', 'profile'].forEach(t => document.getElementById('tab-'+t)?.classList.add('hidden'));
            document.getElementById('tab-'+name)?.classList.remove('hidden');
            const titleEl = document.getElementById('tab-title'); if (titleEl) titleEl.innerText = name.charAt(0).toUpperCase() + name.slice(1);
        }

        async function addEmployee(e) {
            e.preventDefault();
            users.push({ 
                id: Date.now(), name: document.getElementById('new-name').value, user: document.getElementById('new-user').value, pass: document.getElementById('new-pass').value, role: 'pegawai', dept: OFFICE_NAME, nip: document.getElementById('new-nip').value, statusPegawai: document.getElementById('new-status').value, jabatan: document.getElementById('new-jabatan').value, photoUrl: document.getElementById('new-photo').value 
            });
            await syncWithCloud(); renderEmployeeList(); closeAddEmployeeModal(); e.target.reset(); showModal("Berhasil", "Pegawai ditambahkan.");
        }

        function renderEmployeeList() {
            const b = document.getElementById('employee-list-body'); if(!b) return;
            b.innerHTML = users.filter(x => x.role === 'pegawai').map(emp => `<tr><td class="p-4 font-bold">${emp.name}</td><td class="p-4">${emp.nip}</td><td class="p-4 font-bold">${emp.statusPegawai}</td><td class="p-4 text-center"><button onclick="deleteUser(${emp.id})" class="text-red-500 hover:text-red-700 transition"><i class="fas fa-trash"></i></button></td></tr>`).join('');
        }

        async function deleteUser(id) { users = users.filter(u => u.id !== id); await syncWithCloud(); renderEmployeeList(); }

        function loadKopDataUI() {
            if(!kopData) return;
            ['kop-logo-kiri', 'kop-logo-kanan', 'kop-pemda', 'kop-dinas', 'kop-sekolah', 'kop-kecamatan', 'kop-alamat', 'kop-email', 'kop-website'].forEach(f => {
                const el = document.getElementById(f); if(el) el.value = kopData[f.replace('kop-', '')] || '';
            });
        }

        function loadPICDataUI() {
            if(!picData) return;
            const fields = {'pic-pengawas-nama': 'pengawas', 'pic-pengawas-nip': 'nipPengawas', 'pic-kepsek-nama': 'kepsek', 'pic-kepsek-nip': 'nipKepsek'};
            for(let id in fields) { const el = document.getElementById(id); if(el) el.value = picData[fields[id]] || ''; }
        }

        async function handleSaveKop(e) {
            e.preventDefault();
            kopData = { ...kopData, logoKiri: document.getElementById('kop-logo-kiri').value, logoKanan: document.getElementById('kop-logo-kanan').value, pemda: document.getElementById('kop-pemda').value, dinas: document.getElementById('kop-dinas').value, sekolah: document.getElementById('kop-sekolah').value, kecamatan: document.getElementById('kop-kecamatan').value, alamat: document.getElementById('kop-alamat').value, email: document.getElementById('kop-email').value, website: document.getElementById('kop-website').value };
            await syncWithCloud(); showModal("Berhasil", "Pengaturan Kop Surat disimpan ke Cloud.");
        }

        async function handleSavePIC(e) {
            e.preventDefault();
            picData = { pengawas: document.getElementById('pic-pengawas-nama').value, nipPengawas: document.getElementById('pic-pengawas-nip').value, kepsek: document.getElementById('pic-kepsek-nama').value, nipKepsek: document.getElementById('pic-kepsek-nip').value };
            await syncWithCloud(); showModal("Berhasil", "Data Penanggung Jawab disimpan ke Cloud.");
        }

        async function printReport() {
            const elPlace = document.getElementById('print-place');
            const elDate = document.getElementById('print-date-manual');
            const tempatVal = elPlace ? elPlace.value : '';
            const tanggalVal = elDate ? elDate.value : '';
            kopData.tempatCetak = tempatVal; kopData.tanggalCetak = tanggalVal;
            await syncWithCloud();
            const frame = document.getElementById('print-frame');
            const doc = frame.contentWindow.document;
            const rows = attendanceData.map((d, idx) => `<tr><td style="border:1px solid black;padding:5px;text-align:center">${idx+1}</td><td style="border:1px solid black;padding:5px">${d.name}</td><td style="border:1px solid black;padding:5px">${d.nip}</td><td style="border:1px solid black;padding:5px">${d.statusPegawai}</td><td style="border:1px solid black;padding:5px">${d.jabatan}</td><td style="border:1px solid black;padding:5px">${cleanDate(d.date)}</td><td style="border:1px solid black;padding:5px;text-align:center">${d.in}</td><td style="border:1px solid black;padding:5px;text-align:center">${d.out}</td><td style="border:1px solid black;padding:5px;text-align:center">${d.status}</td></tr>`).join('');
            const html = `<html><head><style>body { font-family: 'Times New Roman', serif; padding: 20px; background: white; } .kop-container { width: 100%; border-bottom: 4px double black; margin-bottom: 20px; padding-bottom: 5px; } .kop-table { width: 100%; border-collapse: collapse; } .kop-logo { width: 80px; height: auto; } .kop-text { text-align: center; } .kop-text h2 { margin: 0; font-size: 16px; text-transform: uppercase; font-weight: normal; } .kop-text h1 { margin: 0; font-size: 20px; text-transform: uppercase; font-weight: bold; } .kop-text p { margin: 2px 0; font-size: 10px; font-style: italic; } .report-title { text-align: center; margin-bottom: 20px; } .report-title h3 { margin: 0; font-size: 16px; font-weight: bold; text-decoration: underline; } table.data-table { width: 100%; border-collapse: collapse; font-size: 11px; } table.data-table th { border: 1px solid black; background: #f2f2f2; padding: 8px; text-transform: uppercase; } .footer-table { width: 100%; margin-top: 30px; border-collapse: collapse; } .footer-table td { width: 50%; text-align: center; font-size: 13px; vertical-align: top; } .signature-space { height: 70px; } .footnote { margin-top: 20px; font-style: italic; font-size: 10px; color: #555; border-top: 1px solid #ccc; padding-top: 5px; }</style></head><body><div class="kop-container"><table class="kop-table"><tr><td width="15%" align="left"><img src="${kopData.logoKiri || ''}" class="kop-logo" onerror="this.style.display='none'"></td><td class="kop-text"><h2>${kopData.pemda || 'Pemerintah Kabupaten/Kota'}</h2><h2>${kopData.dinas || 'Dinas Pendidikan dan Kebudayaan'}</h2><h1>${kopData.sekolah || OFFICE_NAME}</h1><h2>KECAMATAN ${kopData.kecamatan || '..........'}</h2><p>Alamat: ${kopData.alamat || '..........'} | Email: ${kopData.email || '..........'}</p><p>Website: ${kopData.website || '..........'}</p></td><td width="15%" align="right"><img src="${kopData.logoKanan || ''}" class="kop-logo" onerror="this.style.display='none'"></td></tr></table></div><div class="report-title"><h3>LAPORAN KEHADIRAN PEGAWAI</h3></div><table class="data-table"><thead><tr><th>No</th><th>Nama Lengkap</th><th>NIP/NUPT</th><th>Status</th><th>Jabatan</th><th>Hari & Tanggal</th><th>Masuk</th><th>Keluar</th><th>Ket</th></tr></thead><tbody>${rows || '<tr><td colspan="9" align="center" style="padding:20px">Belum ada data kehadiran</td></tr>'}</tbody></table><table class="footer-table"><tr><td>Mengetahui,<br>Pengawas Satuan Pendidikan<br><br><div class="signature-space"></div><b><u>${picData.pengawas || '( .......................................... )'}</u></b><br>NIP. ${picData.nipPengawas || '..........................................'}</td><td>${tempatVal || '..........'}, ${tanggalVal || '..........'}<br>Kepala Sekolah<br><br><div class="signature-space"></div><b><u>${picData.kepsek || '( .......................................... )'}</u></b><br>NIP. ${picData.nipKepsek || '..........................................'}</td></tr></table><div class="footnote">Dicetak Melalui Aplikasi Presensi UPT SDN 22 Tanah Keras</div></body></html>`;
            doc.open(); doc.write(html); doc.close();
            setTimeout(() => { frame.contentWindow.focus(); frame.contentWindow.print(); }, 1000);
        }

        function showModal(t, m) { const titleEl = document.getElementById('modal-title'); const msgEl = document.getElementById('modal-message'); const alertEl = document.getElementById('modal-alert'); if (titleEl) titleEl.innerText = t; if (msgEl) msgEl.innerText = m; if (alertEl) alertEl.classList.remove('hidden'); }
        function closeModal() { const alertEl = document.getElementById('modal-alert'); if (alertEl) alertEl.classList.add('hidden'); }
        function openAddEmployeeModal() { const modalEl = document.getElementById('modal-add-employee'); if (modalEl) modalEl.classList.remove('hidden'); }
        function closeAddEmployeeModal() { document.getElementById('modal-add-employee').classList.add('hidden'); }
        function syncUsername(v) { const userEl = document.getElementById('new-user'); if (userEl) userEl.value = v; }
        function handleLogout() { currentUser = null; location.reload(); }

        // LOGIKA JAM, TANGGAL, DAN SALAM DINAMIS
        setInterval(() => {
            const now = new Date();
            const t = now.toLocaleTimeString('id-ID');
            const hour = now.getHours();

            // Elemen Jam
            const elD = document.getElementById('clock-desktop');
            const elM = document.getElementById('clock-mobile');
            if(elD) elD.innerText = t;
            if(elM) elM.innerText = t;

            // Logika Salam Dinamis
            let greet = "Selamat Pagi";
            if (hour >= 11 && hour < 15) greet = "Selamat Siang";
            else if (hour >= 15 && hour < 18) greet = "Selamat Sore";
            else if (hour >= 18 || hour < 4) greet = "Selamat Malam";

            const mobileGreetEl = document.getElementById('mobile-greeting');
            if(mobileGreetEl) mobileGreetEl.innerText = greet;

            const desktopGreetEl = document.getElementById('greeting');
            if(desktopGreetEl) desktopGreetEl.innerText = greet + ", selamat bekerja!";

            // Update Tanggal
            const dateEl = document.getElementById('date-now');
            const dateMobEl = document.getElementById('date-mobile'); // Perbaikan ID di sini

            if(dateEl) {
                dateEl.innerText = now.toLocaleDateString('id-ID', {weekday: 'long', day: '2-digit', month: 'long', year: 'numeric'});
            }
            if(dateMobEl) {
                dateMobEl.innerText = now.toLocaleDateString('id-ID', {day: '2-digit', month: 'short', year: 'numeric'});
            }
        }, 1000);

        window.onload = loadFromCloud;
    </script>
</body>
</html>
