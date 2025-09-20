<!DOCTYPE html>
<html lang="id">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Dasbor Analisis Data Siswa</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <script src="https://cdn.jsdelivr.net/npm/chart.js"></script>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/jspdf/2.5.1/jspdf.umd.min.js"></script>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/jspdf-autotable/3.5.15/jspdf.plugin.autotable.min.js"></script>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/html2canvas/1.4.1/html2canvas.min.js"></script>
    <link href="https://fonts.googleapis.com/css2?family=Inter:wght@400;500;600;700&display=swap" rel="stylesheet">
    <style>
        body {
            font-family: 'Inter', sans-serif;
            background-color: #F9F5EF; 
            color: #3D352E; 
        }
        .chart-container {
            position: relative;
            width: 100%;
            max-width: 600px;
            margin-left: auto;
            margin-right: auto;
            height: 300px;
            max-height: 350px;
        }
        @media (min-width: 768px) {
            .chart-container {
                height: 320px;
            }
        }
        ::-webkit-scrollbar {
            width: 8px;
        }
        ::-webkit-scrollbar-track {
            background: #F0EBE3;
        }
        ::-webkit-scrollbar-thumb {
            background: #D4C4B0;
            border-radius: 10px;
        }
        ::-webkit-scrollbar-thumb:hover {
            background: #B7A490;
        }
        .fade-in {
            animation: fadeIn 0.5s ease-out;
        }
        @keyframes fadeIn {
            from { opacity: 0; transform: translateY(10px); }
            to { opacity: 1; transform: translateY(0); }
        }
    </style>
</head>
<body class="antialiased flex items-center justify-center min-h-screen p-4">
    
    <!-- Halaman Login -->
    <div id="loginPage" class="bg-[#EAE0D5] p-8 rounded-xl shadow-2xl w-full max-w-sm fade-in">
        <img src="https://iili.io/3b1SedN.png" alt="Logo Dasbor" class="h-20 mx-auto mb-4">
        <p class="text-sm text-center text-gray-600 mb-6">Silakan masuk untuk mengakses dasbor.</p>
        
        <form id="loginForm" class="space-y-4">
            <div>
                <label for="username" class="block text-sm font-medium text-gray-700">Nama Pengguna</label>
                <input type="text" id="username" class="mt-1 block w-full rounded-md border-gray-300 shadow-sm p-2 bg-[#F9F5EF] focus:border-[#B7A490] focus:ring focus:ring-[#B7A490] focus:ring-opacity-50">
            </div>
            <div>
                <label for="password" class="block text-sm font-medium text-gray-700">Sandi</label>
                <input type="password" id="password" class="mt-1 block w-full rounded-md border-gray-300 shadow-sm p-2 bg-[#F9F5EF] focus:border-[#B7A490] focus:ring focus:ring-[#B7A490] focus:ring-opacity-50">
            </div>
            <div id="errorMessage" class="text-red-500 text-sm text-center hidden">
                Nama pengguna atau sandi salah.
            </div>
            <button type="submit" class="w-full bg-[#A47F6A] text-white py-2 rounded-lg shadow-md hover:bg-[#B7A490] transition-colors duration-200 font-semibold">
                Login
            </button>
        </form>
    </div>

    <!-- Halaman Dasbor -->
    <div id="dashboardPage" class="min-h-screen flex flex-col hidden w-full">
        <header class="bg-[#EAE0D5] border-b border-[#D4C4B0] p-4 shadow-sm">
            <div class="container mx-auto flex justify-between items-center">
                <h1 class="text-2xl font-bold text-[#5E503F]">Dasbor Analisis Data Siswa</h1>
                <button id="logoutBtn" class="bg-red-500 text-white px-4 py-2 rounded-lg shadow-md hover:bg-red-600 transition-colors duration-200">
                    Keluar
                </button>
            </div>
        </header>

        <main class="flex-grow container mx-auto p-4 md:p-6 lg:p-8">
            <div class="grid grid-cols-1 lg:grid-cols-3 gap-6">
                <div class="lg:col-span-2 bg-[#F9F5EF] p-6 rounded-xl shadow-md border border-[#EAE0D5]">
                    <div class="flex flex-col md:flex-row md:justify-between md:items-center mb-4 space-y-2 md:space-y-0">
                        <h2 class="text-xl font-semibold text-[#5E503F]">Daftar Siswa</h2>
                        <div class="flex flex-wrap items-center justify-end space-x-2 space-y-2 md:space-y-0">
                            <button id="addStudentBtn" class="bg-[#A47F6A] text-white px-4 py-2 rounded-lg shadow-md hover:bg-[#B7A490] transition-colors duration-200">
                                + Tambah Siswa
                            </button>
                            <button id="promoteClassBtn" class="bg-[#5E503F] text-white px-4 py-2 rounded-lg shadow-md hover:bg-[#3D352E] transition-colors duration-200">
                                Naikkan Kelas
                            </button>
                            <button id="downloadPdfBtn" class="bg-[#5E503F] text-white px-4 py-2 rounded-lg shadow-md hover:bg-[#3D352E] transition-colors duration-200">
                                Unduh PDF
                            </button>
                        </div>
                    </div>
                    
                    <div class="grid grid-cols-1 md:grid-cols-3 gap-4 mb-4">
                        <input type="text" id="searchInput" placeholder="Cari Nama atau NISN/NIS..." class="w-full px-4 py-2 border border-[#D4C4B0] rounded-lg focus:outline-none focus:ring-2 focus:ring-[#B7A490] bg-[#F0EBE3]">
                        <select id="classFilter" class="w-full px-4 py-2 border border-[#D4C4B0] rounded-lg focus:outline-none focus:ring-2 focus:ring-[#B7A490] bg-[#F0EBE3]">
                            <option value="">Semua Kelas</option>
                        </select>
                        <select id="genderFilter" class="w-full px-4 py-2 border border-[#D4C4B0] rounded-lg focus:outline-none focus:ring-2 focus:ring-[#B7A490] bg-[#F0EBE3]">
                            <option value="">Semua Gender</option>
                            <option value="Laki-laki">Laki-laki</option>
                            <option value="Perempuan">Perempuan</option>
                        </select>
                    </div>

                    <div class="overflow-x-auto max-h-[600px] overflow-y-auto" id="tableContainer">
                        <table class="w-full text-left">
                            <thead class="bg-[#EAE0D5] sticky top-0">
                                <tr>
                                    <th class="p-3 font-semibold">Nama</th>
                                    <th class="p-3 font-semibold">Kelas</th>
                                    <th class="p-3 font-semibold">NISN/NIS</th>
                                    <th class="p-3 font-semibold">NIK</th>
                                    <th class="p-3 font-semibold">Jenis Kelamin</th>
                                    <th class="p-3 font-semibold">Tempat Lahir</th>
                                    <th class="p-3 font-semibold">Tanggal Lahir</th>
                                    <th class="p-3 font-semibold">Agama</th>
                                    <th class="p-3 font-semibold">Alamat</th>
                                    <th class="p-3 font-semibold">Kecamatan</th>
                                    <th class="p-3 font-semibold">Nama Ayah</th>
                                    <th class="p-3 font-semibold">Nama Ibu</th>
                                    <th class="p-3 font-semibold text-center">Aksi</th>
                                </tr>
                            </thead>
                            <tbody id="studentTableBody">
                            </tbody>
                        </table>
                    </div>
                </div>

                <div class="lg:col-span-1 space-y-6">
                    <div id="summarySection" class="grid grid-cols-2 gap-4">
                         <div class="bg-[#EAE0D5] p-4 rounded-xl shadow-sm border border-[#D4C4B0] text-center">
                            <h3 class="font-semibold text-[#5E503F]">Total Siswa</h3>
                            <p id="totalStudents" class="text-3xl font-bold text-[#A47F6A]">-</p>
                        </div>
                         <div class="bg-[#EAE0D5] p-4 rounded-xl shadow-sm border border-[#D4C4B0] text-center">
                            <h3 class="font-semibold text-[#5E503F]">Rata-rata Usia</h3>
                            <p id="averageAge" class="text-3xl font-bold text-[#A47F6A]">-</p>
                        </div>
                    </div>

                    <div id="detailOrChartSection">
                        <div id="chartsPanel" class="space-y-6">
                            <div class="bg-[#F0EBE3] p-4 rounded-xl shadow-md border border-[#EAE0D5]">
                                <h3 class="text-lg font-semibold text-center mb-2 text-[#5E503F]">Distribusi Gender</h3>
                                <div class="chart-container h-64 md:h-72"><canvas id="genderChart"></canvas></div>
                            </div>
                             <div class="bg-[#F0EBE3] p-4 rounded-xl shadow-md border border-[#EAE0D5]">
                                <h3 class="text-lg font-semibold text-center mb-2 text-[#5E503F]">Jumlah Siswa per Kelas</h3>
                                <div class="chart-container h-64 md:h-72"><canvas id="classChart"></canvas></div>
                            </div>
                        </div>
                    </div>
                </div>
            </div>
            
            <!-- Arsip Siswa Lulus -->
            <div id="archivedStudentsSection" class="mt-8 lg:col-span-3 bg-[#F9F5EF] p-6 rounded-xl shadow-md border border-[#EAE0D5]">
                <div class="flex flex-col md:flex-row md:justify-between md:items-center mb-4 space-y-2 md:space-y-0">
                    <h2 class="text-xl font-semibold text-[#5E503F]">Arsip Siswa Lulus</h2>
                    <button id="downloadArchivedPdfBtn" class="bg-[#5E503F] text-white px-4 py-2 rounded-lg shadow-md hover:bg-[#3D352E] transition-colors duration-200">
                        Unduh Arsip PDF
                    </button>
                </div>
                
                <div class="overflow-x-auto max-h-[400px] overflow-y-auto">
                    <table class="w-full text-left">
                        <thead class="bg-[#EAE0D5] sticky top-0">
                            <tr>
                                <th class="p-3 font-semibold">Nama</th>
                                <th class="p-3 font-semibold">NISN/NIS</th>
                                <th class="p-3 font-semibold">Tanggal Lulus</th>
                                <th class="p-3 font-semibold text-center">Aksi</th>
                            </tr>
                        </thead>
                        <tbody id="archivedStudentTableBody">
                        </tbody>
                    </table>
                </div>
            </div>

        </main>
        
        <!-- Footer -->
        <footer class="bg-[#EAE0D5] border-t border-[#D4C4B0] p-4 text-center text-sm text-[#5E503F] mt-6">
            <div class="container mx-auto">
                <p>Hak Cipta &copy; 2024 Dasbor Analisis Data Siswa. Semua Hak Dilindungi.</p>
            </div>
        </footer>

    </div>

    <!-- Modal untuk Tambah/Edit Siswa -->
    <div id="addStudentModal" class="fixed inset-0 bg-black bg-opacity-50 hidden flex items-center justify-center p-4 z-50">
        <div class="bg-[#F0EBE3] rounded-xl shadow-2xl w-full max-w-2xl p-6 relative fade-in">
            <button id="closeModalBtn" class="absolute top-4 right-4 text-[#5E503F] hover:text-[#3D352E] text-2xl font-bold">&times;</button>
            <h2 id="modalTitle" class="text-2xl font-bold text-center mb-6 text-[#5E503F]">Formulir Data Siswa Baru</h2>
            <form id="addStudentForm" class="space-y-4">
                <div class="grid grid-cols-1 md:grid-cols-2 gap-4">
                    <div>
                        <label for="namaSiswa" class="block text-sm font-medium text-gray-700">Nama Siswa</label>
                        <input type="text" id="namaSiswa" required class="mt-1 block w-full rounded-md border-gray-300 shadow-sm p-2 bg-[#F9F5EF] focus:border-[#B7A490] focus:ring focus:ring-[#B7A490] focus:ring-opacity-50">
                    </div>
                    <div>
                        <label for="kelas" class="block text-sm font-medium text-gray-700">Kelas</label>
                        <select id="kelas" required class="mt-1 block w-full rounded-md border-gray-300 shadow-sm p-2 bg-[#F9F5EF] focus:border-[#B7A490] focus:ring focus:ring-[#B7A490] focus:ring-opacity-50">
                            <option value="">Pilih Kelas</option>
                            <option value="Kelas 1">Kelas 1</option>
                            <option value="Kelas 2">Kelas 2</option>
                            <option value="Kelas 3">Kelas 3</option>
                            <option value="Kelas 4">Kelas 4</option>
                            <option value="Kelas 5">Kelas 5</option>
                            <option value="Kelas 6">Kelas 6</option>
                        </select>
                    </div>
                </div>
                 <div class="grid grid-cols-1 md:grid-cols-2 gap-4">
                    <div>
                        <label for="nisnNis" class="block text-sm font-medium text-gray-700">NISN/NIS</label>
                        <input type="text" id="nisnNis" required class="mt-1 block w-full rounded-md border-gray-300 shadow-sm p-2 bg-[#F9F5EF] focus:border-[#B7A490] focus:ring focus:ring-[#B7A490] focus:ring-opacity-50">
                    </div>
                    <div>
                        <label for="nikSiswa" class="block text-sm font-medium text-gray-700">NIK Siswa</label>
                        <input type="text" id="nikSiswa" required class="mt-1 block w-full rounded-md border-gray-300 shadow-sm p-2 bg-[#F9F5EF] focus:border-[#B7A490] focus:ring focus:ring-[#B7A490] focus:ring-opacity-50">
                    </div>
                </div>
                 <div class="grid grid-cols-1 md:grid-cols-2 gap-4">
                    <div>
                        <label for="tempatLahir" class="block text-sm font-medium text-gray-700">Tempat Lahir</label>
                        <input type="text" id="tempatLahir" required class="mt-1 block w-full rounded-md border-gray-300 shadow-sm p-2 bg-[#F9F5EF] focus:border-[#B7A490] focus:ring focus:ring-[#B7A490] focus:ring-opacity-50">
                    </div>
                    <div>
                        <label for="tanggalLahir" class="block text-sm font-medium text-gray-700">Tanggal Lahir</label>
                        <input type="date" id="tanggalLahir" required class="mt-1 block w-full rounded-md border-gray-300 shadow-sm p-2 bg-[#F9F5EF] focus:border-[#B7A490] focus:ring focus:ring-[#B7A490] focus:ring-opacity-50">
                    </div>
                </div>
                 <div class="grid grid-cols-1 md:grid-cols-2 gap-4">
                    <div>
                        <label for="jenisKelamin" class="block text-sm font-medium text-gray-700">Jenis Kelamin</label>
                        <select id="jenisKelamin" required class="mt-1 block w-full rounded-md border-gray-300 shadow-sm p-2 bg-[#F9F5EF] focus:border-[#B7A490] focus:ring focus:ring-[#B7A490] focus:ring-opacity-50">
                            <option value="">Pilih</option>
                            <option value="Laki-laki">Laki-laki</option>
                            <option value="Perempuan">Perempuan</option>
                        </select>
                    </div>
                    <div>
                        <label for="agama" class="block text-sm font-medium text-gray-700">Agama</label>
                        <input type="text" id="agama" required class="mt-1 block w-full rounded-md border-gray-300 shadow-sm p-2 bg-[#F9F5EF] focus:border-[#B7A490] focus:ring focus:ring-[#B7A490] focus:ring-opacity-50">
                    </div>
                </div>
                <div class="grid grid-cols-1 md:grid-cols-2 gap-4">
                    <div>
                        <label for="alamat" class="block text-sm font-medium text-gray-700">Alamat</label>
                        <input type="text" id="alamat" required class="mt-1 block w-full rounded-md border-gray-300 shadow-sm p-2 bg-[#F9F5EF] focus:border-[#B7A490] focus:ring focus:ring-[#B7A490] focus:ring-opacity-50">
                    </div>
                    <div>
                        <label for="kecamatan" class="block text-sm font-medium text-gray-700">Kecamatan</label>
                        <input type="text" id="kecamatan" required class="mt-1 block w-full rounded-md border-gray-300 shadow-sm p-2 bg-[#F9F5EF] focus:border-[#B7A490] focus:ring focus:ring-[#B7A490] focus:ring-opacity-50">
                    </div>
                </div>
                <div class="grid grid-cols-1 md:grid-cols-2 gap-4">
                    <div>
                        <label for="namaAyah" class="block text-sm font-medium text-gray-700">Nama Ayah</label>
                        <input type="text" id="namaAyah" required class="mt-1 block w-full rounded-md border-gray-300 shadow-sm p-2 bg-[#F9F5EF] focus:border-[#B7A490] focus:ring focus:ring-[#B7A490] focus:ring-opacity-50">
                    </div>
                    <div>
                        <label for="namaIbu" class="block text-sm font-medium text-gray-700">Nama Ibu</label>
                        <input type="text" id="namaIbu" required class="mt-1 block w-full rounded-md border-gray-300 shadow-sm p-2 bg-[#F9F5EF] focus:border-[#B7A490] focus:ring focus:ring-[#B7A490] focus:ring-opacity-50">
                    </div>
                </div>
                <div class="flex justify-end pt-4">
                    <button type="submit" id="submitBtn" class="bg-[#A47F6A] text-white px-6 py-2 rounded-lg shadow-md hover:bg-[#B7A490] transition-colors duration-200">
                        Simpan Siswa
                    </button>
                </div>
            </form>
        </div>
    </div>

    <!-- Modal untuk Konfirmasi Hapus/Naik Kelas -->
    <div id="confirmationModal" class="fixed inset-0 bg-black bg-opacity-50 hidden flex items-center justify-center p-4 z-50">
        <div class="bg-[#F0EBE3] rounded-xl shadow-2xl w-full max-w-sm p-6 relative fade-in text-center">
            <p id="confirmationMessage" class="mb-6 text-lg font-medium text-[#5E503F]"></p>
            <div class="flex justify-center space-x-4">
                <button id="confirmActionBtn" class="bg-red-500 text-white px-6 py-2 rounded-lg shadow-md hover:bg-red-600 transition-colors duration-200">
                    Ya
                </button>
                <button id="cancelActionBtn" class="bg-gray-400 text-white px-6 py-2 rounded-lg shadow-md hover:bg-gray-500 transition-colors duration-200">
                    Batal
                </button>
            </div>
        </div>
    </div>

    <!-- Status Message Modal -->
    <div id="statusModal" class="fixed inset-0 bg-black bg-opacity-50 hidden flex items-center justify-center p-4 z-50">
        <div class="bg-[#F0EBE3] rounded-xl shadow-2xl w-full max-w-sm p-6 relative fade-in text-center">
            <p id="statusMessage" class="mb-6 text-lg font-medium text-[#5E503F]"></p>
            <button id="closeStatusModalBtn" class="bg-gray-400 text-white px-6 py-2 rounded-lg shadow-md hover:bg-gray-500 transition-colors duration-200">
                Tutup
            </button>
        </div>
    </div>

<script>
let studentData = [];
let archivedStudents = [];
const logoUrl = 'https://iili.io/3b1SedN.png';
let logoBase64 = null;

const loginPage = document.getElementById('loginPage');
const dashboardPage = document.getElementById('dashboardPage');
const loginForm = document.getElementById('loginForm');
const logoutBtn = document.getElementById('logoutBtn');
const errorMessage = document.getElementById('errorMessage');

const searchInput = document.getElementById('searchInput');
const classFilter = document.getElementById('classFilter');
const genderFilter = document.getElementById('genderFilter');
const studentTableBody = document.getElementById('studentTableBody');
const archivedStudentTableBody = document.getElementById('archivedStudentTableBody');
const totalStudentsEl = document.getElementById('totalStudents');
const averageAgeEl = document.getElementById('averageAge');
const chartsPanel = document.getElementById('chartsPanel');
const addStudentBtn = document.getElementById('addStudentBtn');
const promoteClassBtn = document.getElementById('promoteClassBtn');
const downloadPdfBtn = document.getElementById('downloadPdfBtn');
const downloadArchivedPdfBtn = document.getElementById('downloadArchivedPdfBtn');
const addStudentModal = document.getElementById('addStudentModal');
const closeModalBtn = document.getElementById('closeModalBtn');
const addStudentForm = document.getElementById('addStudentForm');
const modalTitle = document.getElementById('modalTitle');
const submitBtn = document.getElementById('submitBtn');

const confirmationModal = document.getElementById('confirmationModal');
const confirmationMessage = document.getElementById('confirmationMessage');
const confirmActionBtn = document.getElementById('confirmActionBtn');
const cancelActionBtn = document.getElementById('cancelActionBtn');

const statusModal = document.getElementById('statusModal');
const statusMessage = document.getElementById('statusMessage');
const closeStatusModalBtn = document.getElementById('closeStatusModalBtn');

let genderChart, classChart;
let currentAction = '';

function showStatusMessage(message) {
    statusMessage.textContent = message;
    statusModal.classList.remove('hidden');
}

function closeStatusMessage() {
    statusModal.classList.add('hidden');
}

// Fungsi untuk menyimpan data ke localStorage
function saveDataToLocalStorage() {
    try {
        localStorage.setItem('studentData', JSON.stringify(studentData));
        localStorage.setItem('archivedStudents', JSON.stringify(archivedStudents));
        showStatusMessage('Data berhasil disimpan!');
    } catch (error) {
        console.error('Gagal menyimpan data ke localStorage:', error);
        showStatusMessage('Gagal menyimpan data.');
    }
    setTimeout(closeStatusMessage, 1500);
}

function calculateAge(birthDate) {
    const today = new Date();
    const birth = new Date(birthDate);
    let age = today.getFullYear() - birth.getFullYear();
    const m = today.getMonth() - birth.getMonth();
    if (m < 0 || (m === 0 && today.getDate() < birth.getDate())) {
        age--;
    }
    return age;
}

function renderStudentTable(data) {
    studentTableBody.innerHTML = '';
    if (data.length === 0) {
        studentTableBody.innerHTML = `<tr><td colspan="13" class="text-center p-4">Data tidak ditemukan.</td></tr>`;
        return;
    }
    data.forEach((student, index) => {
        const row = document.createElement('tr');
        row.className = 'border-b border-[#EAE0D5] hover:bg-[#F0EBE3]';
        row.id = `student-row-${index}`;
        row.innerHTML = `
            <td class="p-3">${student.namaSiswa}</td>
            <td class="p-3">${student.kelas}</td>
            <td class="p-3">${student.nisnNis}</td>
            <td class="p-3">${student.nikSiswa || '-'}</td>
            <td class="p-3">${student.jenisKelamin}</td>
            <td class="p-3">${student.tempatLahir || '-'}</td>
            <td class="p-3">${student.tanggalLahir || '-'}</td>
            <td class="p-3">${student.agama || '-'}</td>
            <td class="p-3">${student.alamat || '-'}</td>
            <td class="p-3">${student.kecamatan || '-'}</td>
            <td class="p-3">${student.namaAyah || '-'}</td>
            <td class="p-3">${student.namaIbu || '-'}</td>
            <td class="p-3 text-center space-x-2">
                <button onclick="handleEdit(${index})" class="bg-[#B7A490] text-white px-2 py-1 rounded-md text-sm hover:bg-[#A47F6A] transition-colors duration-200">Edit</button>
                <button onclick="showConfirmation('delete', ${index})" class="bg-red-500 text-white px-2 py-1 rounded-md text-sm hover:bg-red-600 transition-colors duration-200">Hapus</button>
            </td>
        `;
        studentTableBody.appendChild(row);
    });
}

function renderArchivedStudentsTable(data) {
    archivedStudentTableBody.innerHTML = '';
    if (data.length === 0) {
        archivedStudentTableBody.innerHTML = `<tr><td colspan="4" class="text-center p-4">Tidak ada siswa yang diarsipkan.</td></tr>`;
        return;
    }
    data.forEach((student, index) => {
        const row = document.createElement('tr');
        row.className = 'border-b border-[#EAE0D5] hover:bg-[#F0EBE3]';
        row.innerHTML = `
            <td class="p-3">${student.namaSiswa}</td>
            <td class="p-3">${student.nisnNis}</td>
            <td class="p-3">${student.tanggalLulus}</td>
            <td class="p-3 text-center">
                <button onclick="showConfirmation('deleteArchived', ${index})" class="bg-red-500 text-white px-2 py-1 rounded-md text-sm hover:bg-red-600 transition-colors duration-200">Hapus</button>
            </td>
        `;
        archivedStudentTableBody.appendChild(row);
    });
}

function renderSummary(data) {
    totalStudentsEl.textContent = data.length;
    if (data.length > 0) {
        const totalAge = data.reduce((sum, s) => sum + calculateAge(s.tanggalLahir), 0);
        averageAgeEl.textContent = (totalAge / data.length).toFixed(1);
    } else {
        averageAgeEl.textContent = '-';
    }
}

function handleEdit(studentIndex) {
    const student = studentData[studentIndex];
    if (!student) return;

    modalTitle.textContent = 'Edit Data Siswa';
    submitBtn.textContent = 'Simpan Perubahan';
    addStudentForm.setAttribute('data-student-index', studentIndex);
    
    document.getElementById('namaSiswa').value = student.namaSiswa;
    document.getElementById('kelas').value = student.kelas;
    document.getElementById('nisnNis').value = student.nisnNis;
    document.getElementById('nikSiswa').value = student.nikSiswa;
    document.getElementById('tempatLahir').value = student.tempatLahir;
    document.getElementById('tanggalLahir').value = student.tanggalLahir;
    document.getElementById('jenisKelamin').value = student.jenisKelamin;
    document.getElementById('agama').value = student.agama;
    document.getElementById('alamat').value = student.alamat;
    document.getElementById('kecamatan').value = student.kecamatan;
    document.getElementById('namaAyah').value = student.namaAyah;
    document.getElementById('namaIbu').value = student.namaIbu;
    
    addStudentModal.classList.remove('hidden');
}

function showConfirmation(action, index = null) {
    currentAction = action;
    confirmActionBtn.setAttribute('data-index', index);
    if (action === 'delete') {
        const student = studentData[index];
        confirmationMessage.textContent = `Apakah Anda yakin ingin menghapus data siswa bernama ${student.namaSiswa}?`;
        confirmActionBtn.textContent = 'Ya, Hapus';
    } else if (action === 'deleteArchived') {
        const student = archivedStudents[index];
        confirmationMessage.textContent = `Apakah Anda yakin ingin menghapus arsip siswa bernama ${student.namaSiswa}?`;
        confirmActionBtn.textContent = 'Ya, Hapus Arsip';
    } else if (action === 'promote') {
        confirmationMessage.textContent = `Apakah Anda yakin ingin menaikkan semua siswa ke kelas berikutnya? Siswa kelas 6 akan diarsipkan sebagai lulus.`;
        confirmActionBtn.textContent = 'Ya, Proses';
    }
    confirmationModal.classList.remove('hidden');
}

async function handleConfirmedAction() {
    confirmationModal.classList.add('hidden');
    const index = parseInt(confirmActionBtn.getAttribute('data-index'));

    if (currentAction === 'delete') {
        if (index > -1) {
            studentData.splice(index, 1);
            applyFilters();
            saveDataToLocalStorage();
        }
    } else if (currentAction === 'deleteArchived') {
        if (index > -1) {
            archivedStudents.splice(index, 1);
            renderArchivedStudentsTable(archivedStudents);
            saveDataToLocalStorage();
        }
    } else if (currentAction === 'promote') {
        const studentsToArchive = [];
        const promotedStudents = [];
        const today = new Date().toLocaleDateString('id-ID', { year: 'numeric', month: 'long', day: 'numeric' });

        studentData.forEach(student => {
            const gradeMatch = student.kelas.match(/(\d+)/);
            if (gradeMatch) {
                const currentGrade = parseInt(gradeMatch[1]);
                if (currentGrade < 6) {
                    const nextGrade = currentGrade + 1;
                    student.kelas = `Kelas ${nextGrade}`;
                    promotedStudents.push(student);
                } else if (currentGrade === 6) {
                    studentsToArchive.push({
                        namaSiswa: student.namaSiswa,
                        nisnNis: student.nisnNis,
                        tanggalLulus: today
                    });
                }
            }
        });
        
        archivedStudents = [...archivedStudents, ...studentsToArchive];
        studentData = promotedStudents;
        
        updateClassFilterOptions();
        applyFilters();
        renderArchivedStudentsTable(archivedStudents);
        saveDataToLocalStorage();
    }
}

function renderCharts(data) {
    const genderCtx = document.getElementById('genderChart').getContext('2d');
    const classCtx = document.getElementById('classChart').getContext('2d');

    const genderCounts = data.reduce((acc, student) => {
        acc[student.jenisKelamin] = (acc[student.jenisKelamin] || 0) + 1;
        return acc;
    }, {});
    
    const genderColors = {
        'Laki-laki': '#EF4444', 
        'Perempuan': '#3B82F6'
    };

    const classCounts = data.reduce((acc, student) => {
        acc[student.kelas] = (acc[student.kelas] || 0) + 1;
        return acc;
    }, {});
    const sortedClasses = Object.keys(classCounts).sort((a, b) => {
        const numA = parseInt(a.match(/\d+/) || 0);
        const numB = parseInt(b.match(/\d+/) || 0);
        if (numA === numB) {
            return a.localeCompare(b);
        }
        return numA - numB;
    });
    const sortedClassData = sortedClasses.map(cls => classCounts[cls]);

    // Array warna baru untuk grafik bar
    const barColors = [
        '#A47F6A', 
        '#3D352E', 
        '#F9E79F', 
        '#B7A490', 
        '#EAE0D5', 
        '#5E503F',
        '#C5D6C1'
    ];

    if (genderChart) genderChart.destroy();
    genderChart = new Chart(genderCtx, {
        type: 'doughnut',
        data: {
            labels: Object.keys(genderCounts),
            datasets: [{
                data: Object.values(genderCounts),
                backgroundColor: Object.keys(genderCounts).map(gender => genderColors[gender]),
                borderColor: '#F0EBE3',
                borderWidth: 4
            }]
        },
        options: {
            responsive: true,
            maintainAspectRatio: false,
            plugins: {
                legend: { position: 'bottom' }
            }
        }
    });

    if (classChart) classChart.destroy();
    classChart = new Chart(classCtx, {
        type: 'bar',
        data: {
            labels: sortedClasses,
            datasets: [{
                label: 'Jumlah Siswa',
                data: sortedClassData,
                backgroundColor: barColors.slice(0, sortedClasses.length), // Menggunakan array warna baru
                borderRadius: 4
            }]
        },
        options: {
            responsive: true,
            maintainAspectRatio: false,
            scales: {
                y: { beginAtZero: true, ticks: { stepSize: 1 } }
            },
            plugins: {
                legend: { display: false }
            }
        }
    });
}

function applyFilters() {
    const searchTerm = searchInput.value.toLowerCase();
    const selectedClass = classFilter.value;
    const selectedGender = genderFilter.value;

    const filteredData = studentData.filter(student => {
        const nameMatch = student.namaSiswa.toLowerCase().includes(searchTerm);
        const nisnMatch = student.nisnNis.toLowerCase().includes(searchTerm);
        const classMatch = selectedClass ? student.kelas === selectedClass : true;
        const genderMatch = selectedGender ? student.jenisKelamin === selectedGender : true;
        return (nameMatch || nisnMatch) && classMatch && genderMatch;
    });

    renderStudentTable(filteredData);
    renderSummary(filteredData);
    renderCharts(filteredData);
}

function updateClassFilterOptions() {
    const uniqueClasses = [...new Set(studentData.map(s => s.kelas))].sort((a, b) => {
        const numA = parseInt(a.match(/\d+/) || 0);
        const numB = parseInt(b.match(/\d+/) || 0);
        if (numA === numB) {
            return a.localeCompare(b);
        }
        return numA - numB;
    });

    classFilter.innerHTML = '<option value="">Semua Kelas</option>';
    uniqueClasses.forEach(cls => {
        const option = document.createElement('option');
        option.value = cls;
        option.textContent = cls;
        classFilter.appendChild(option);
    });
}

function initialize() {
    // Muat data dari localStorage saat halaman dimuat
    const storedData = localStorage.getItem('studentData');
    if (storedData) {
        try {
            studentData = JSON.parse(storedData);
        } catch (e) {
            console.error("Failed to parse student data from localStorage", e);
            studentData = [];
        }
    } else {
        studentData = [];
    }

    const storedArchivedData = localStorage.getItem('archivedStudents');
    if (storedArchivedData) {
        try {
            archivedStudents = JSON.parse(storedArchivedData);
        } catch (e) {
            console.error("Failed to parse archived students data from localStorage", e);
            archivedStudents = [];
        }
    } else {
        archivedStudents = [];
    }

    // Load logo as base64
    fetch(logoUrl)
        .then(response => response.blob())
        .then(blob => {
            const reader = new FileReader();
            reader.onloadend = function() {
                logoBase64 = reader.result;
            };
            reader.readAsDataURL(blob);
        })
        .catch(err => console.error("Failed to load logo:", err));

    updateClassFilterOptions(); 
    applyFilters(); 
    renderArchivedStudentsTable(archivedStudents);
    
    searchInput.addEventListener('input', applyFilters);
    classFilter.addEventListener('change', applyFilters);
    genderFilter.addEventListener('change', applyFilters);
    
    addStudentBtn.addEventListener('click', () => {
        modalTitle.textContent = 'Formulir Data Siswa Baru';
        submitBtn.textContent = 'Simpan Siswa';
        addStudentForm.removeAttribute('data-student-index');
        addStudentForm.reset();
        addStudentModal.classList.remove('hidden');
    });

    promoteClassBtn.addEventListener('click', () => {
        showConfirmation('promote');
    });

    closeModalBtn.addEventListener('click', () => {
        addStudentModal.classList.add('hidden');
        addStudentForm.reset();
    });

    closeStatusModalBtn.addEventListener('click', closeStatusMessage);

    addStudentModal.addEventListener('click', (e) => {
        if (e.target.id === 'addStudentModal') {
            addStudentModal.classList.add('hidden');
            addStudentForm.reset();
        }
    });
    
    confirmActionBtn.addEventListener('click', handleConfirmedAction);
    cancelActionBtn.addEventListener('click', () => {
        confirmationModal.classList.add('hidden');
    });
    
    downloadPdfBtn.addEventListener('click', async () => {
        const { jsPDF } = window.jspdf;
        const doc = new jsPDF({
            orientation: 'l',
            unit: 'pt',
            format: 'legal' 
        });

        // Add logo if available, smaller size
        if (logoBase64) {
            const imgWidth = 60;
            const imgHeight = 60;
            const margin = 20;
            const xPos = margin;
            const yPos = margin;
            doc.addImage(logoBase64, 'PNG', xPos, yPos, imgWidth, imgHeight);
        }

        // Add title below logo
        const title = "Daftar Siswa UPT SDN 22 TANAH KERAS";
        const pageWidth = doc.internal.pageSize.getWidth();
        const titleWidth = doc.getStringUnitWidth(title) * doc.internal.getFontSize() / doc.internal.scaleFactor;
        const titleX = (pageWidth - titleWidth) / 2;
        doc.text(title, titleX, 40);

        const headers = [
            ['Nama', 'Kelas', 'NISN/NIS', 'NIK', 'Jenis Kelamin', 'Tempat Lahir', 'Tanggal Lahir', 'Agama', 'Alamat', 'Kecamatan', 'Nama Ayah', 'Nama Ibu']
        ];
        
        const data = studentData.map(student => [
            student.namaSiswa,
            student.kelas,
            student.nisnNis,
            student.nikSiswa || '-',
            student.jenisKelamin,
            student.tempatLahir || '-',
            student.tanggalLahir || '-',
            student.agama || '-',
            student.alamat || '-',
            student.kecamatan || '-',
            student.namaAyah || '-',
            student.namaIbu || '-'
        ]);

        doc.autoTable({
            head: headers,
            body: data,
            startY: 80, 
            styles: {
                fontSize: 8,
                cellPadding: 4,
            },
            headStyles: {
                fillColor: [234, 224, 213], 
                textColor: [61, 53, 46],
                fontStyle: 'bold',
            },
            theme: 'striped',
            alternateRowStyles: {
                fillColor: [249, 245, 239], 
            }
        });

        doc.save('Data_Siswa.pdf');
    });

    downloadArchivedPdfBtn.addEventListener('click', async () => {
        const { jsPDF } = window.jspdf;
        const doc = new jsPDF({
            orientation: 'p',
            unit: 'pt',
            format: 'A4'
        });

        // Add logo if available, smaller size
        if (logoBase64) {
            const imgWidth = 60;
            const imgHeight = 60;
            const margin = 20;
            const xPos = margin;
            const yPos = margin;
            doc.addImage(logoBase64, 'PNG', xPos, yPos, imgWidth, imgHeight);
        }

        // Add title below logo
        const title = "ARSIP KELULUSAN UPT SDN 22 TANAH KERAS";
        const pageWidth = doc.internal.pageSize.getWidth();
        const titleWidth = doc.getStringUnitWidth(title) * doc.internal.getFontSize() / doc.internal.scaleFactor;
        const titleX = (pageWidth - titleWidth) / 2;
        doc.text(title, titleX, 40);

        const headers = [
            ['Nama', 'NISN/NIS', 'Tanggal Lulus']
        ];
        
        const data = archivedStudents.map(student => [
            student.namaSiswa,
            student.nisnNis,
            student.tanggalLulus
        ]);

        doc.autoTable({
            head: headers,
            body: data,
            startY: 80,
            styles: {
                fontSize: 10,
                cellPadding: 6,
            },
            headStyles: {
                fillColor: [234, 224, 213], 
                textColor: [61, 53, 46],
                fontStyle: 'bold',
            },
            theme: 'striped',
            alternateRowStyles: {
                fillColor: [249, 245, 239], 
            }
        });

        doc.save('Arsip_Siswa_Lulus.pdf');
    });

    addStudentForm.addEventListener('submit', async (e) => {
        e.preventDefault();
        const studentIndex = addStudentForm.getAttribute('data-student-index');
        const formData = {
            namaSiswa: document.getElementById('namaSiswa').value,
            kelas: document.getElementById('kelas').value,
            nisnNis: document.getElementById('nisnNis').value,
            nikSiswa: document.getElementById('nikSiswa').value,
            tempatLahir: document.getElementById('tempatLahir').value,
            tanggalLahir: document.getElementById('tanggalLahir').value,
            jenisKelamin: document.getElementById('jenisKelamin').value,
            agama: document.getElementById('agama').value,
            alamat: document.getElementById('alamat').value,
            kecamatan: document.getElementById('kecamatan').value,
            namaAyah: document.getElementById('namaAyah').value,
            namaIbu: document.getElementById('namaIbu').value,
            pekerjaanAyah: '',
            pekerjaanIbu: '',
            noHp: ''
        };

        if (studentIndex) {
            studentData[studentIndex] = { ...studentData[studentIndex], ...formData };
        } else {
            const newStudent = { id: Date.now(), ...formData };
            studentData.push(newStudent);
        }
        
        addStudentModal.classList.add('hidden');
        addStudentForm.reset();
        
        updateClassFilterOptions();
        applyFilters();
        saveDataToLocalStorage();
    });
}

document.addEventListener('DOMContentLoaded', () => {
    // Clear login form fields on page load
    document.getElementById('username').value = '';
    document.getElementById('password').value = '';
    
    const isAuthenticated = localStorage.getItem('isAuthenticated') === 'true';
    if (isAuthenticated) {
        loginPage.classList.add('hidden');
        dashboardPage.classList.remove('hidden');
        initialize();
    } else {
        loginPage.classList.remove('hidden');
        dashboardPage.classList.add('hidden');
    }

    loginForm.addEventListener('submit', function(e) {
        e.preventDefault();
        const usernameInput = document.getElementById('username').value;
        const passwordInput = document.getElementById('password').value;
        
        if (usernameInput === 'Admin' && passwordInput === '12345') {
            localStorage.setItem('isAuthenticated', 'true');
            loginPage.classList.add('hidden');
            dashboardPage.classList.remove('hidden');
            initialize();
        } else {
            errorMessage.classList.remove('hidden');
        }
    });

    logoutBtn.addEventListener('click', () => {
        localStorage.removeItem('isAuthenticated');
        dashboardPage.classList.add('hidden');
        loginPage.classList.remove('hidden');
        document.getElementById('username').value = '';
        document.getElementById('password').value = '';
    });
});
</script>
</body>
</html>
