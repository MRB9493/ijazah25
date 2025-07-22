<!DOCTYPE html>
<html lang="id">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Formulir Pengambilan Ijazah - MTs Nurul Jadid</title>
    
    <!-- Memuat Tailwind CSS untuk styling -->
    <script src="https://cdn.tailwindcss.com"></script>
    
    <!-- Memuat Font Inter dari Google Fonts -->
    <link rel="preconnect" href="https://fonts.googleapis.com">
    <link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
    <link href="https://fonts.googleapis.com/css2?family=Inter:wght@400;500;600;700&display=swap" rel="stylesheet">
    
    <style>
        /* Menggunakan font Inter sebagai font utama */
        body {
            font-family: 'Inter', sans-serif;
        }
        /* Menyembunyikan elemen video dan canvas secara default */
        #cameraStream, #photoCanvas, #photoPreview {
            display: none;
        }
    </style>
</head>
<body class="bg-gray-100 flex items-center justify-center min-h-screen p-4">

    <div class="w-full max-w-2xl bg-white rounded-xl shadow-2xl overflow-hidden">
        <!-- Header Formulir -->
        <div class="bg-indigo-600 p-6">
            <div class="flex items-center space-x-4">
                <div class="bg-white p-2 rounded-md">
                    <!-- Logo MTs Nurul Jadid yang sudah diperbarui -->
                    <img src="http://googleusercontent.com/file_content/1" alt="Logo MTs Nurul Jadid" class="h-12 w-12 object-contain">
                </div>
                <div>
                    <h1 class="text-2xl font-bold text-white">Formulir Pengambilan Ijazah</h1>
                    <p class="text-indigo-200">MTs Nurul Jadid</p>
                </div>
            </div>
        </div>

        <!-- Konten Formulir -->
        <form id="ijazahForm" class="p-8 space-y-6">
            
            <!-- Isian Nama Lengkap -->
            <div>
                <label for="namaLengkap" class="block text-sm font-medium text-gray-700 mb-1">Nama Lengkap (sesuai ijazah)</label>
                <input type="text" id="namaLengkap" name="namaLengkap" placeholder="Contoh: Muhammad Abdullah" required
                       class="w-full px-4 py-2 border border-gray-300 rounded-md shadow-sm focus:outline-none focus:ring-2 focus:ring-indigo-500 focus:border-indigo-500 transition">
            </div>

            <!-- Isian Jurusan -->
            <div>
                <label for="jurusan" class="block text-sm font-medium text-gray-700 mb-1">Jurusan</label>
                <select id="jurusan" name="jurusan" required
                        class="w-full px-4 py-2 border border-gray-300 rounded-md shadow-sm focus:outline-none focus:ring-2 focus:ring-indigo-500 focus:border-indigo-500 transition bg-white">
                    <option value="" disabled selected>-- Pilih Jurusan --</option>
                    <option value="Reguler">Reguler</option>
                    <option value="Tahfidz">Tahfidz</option>
                    <option value="Program Khusus">Program Khusus</option>
                    <option value="Lainnya">Lainnya</option>
                </select>
            </div>

            <!-- Isian Studi Lanjut -->
            <div>
                <label for="studiLanjut" class="block text-sm font-medium text-gray-700 mb-1">Melanjutkan Studi ke</label>
                <input type="text" id="studiLanjut" name="studiLanjut" placeholder="Contoh: MA Nurul Jadid / SMK Teknologi Informasi" required
                       class="w-full px-4 py-2 border border-gray-300 rounded-md shadow-sm focus:outline-none focus:ring-2 focus:ring-indigo-500 focus:border-indigo-500 transition">
            </div>

            <!-- Pengambilan Foto Langsung -->
            <div>
                <label class="block text-sm font-medium text-gray-700 mb-2">Bukti Foto (Ambil Langsung)</label>
                <div class="w-full p-4 border border-gray-300 rounded-md bg-gray-50 text-center">
                    <!-- Elemen untuk stream kamera dan pratinjau foto -->
                    <div id="cameraContainer" class="relative w-full max-w-md mx-auto bg-gray-200 rounded-md overflow-hidden">
                        <video id="cameraStream" autoplay playsinline class="w-full h-auto"></video>
                        <img id="photoPreview" src="" alt="Pratinjau Foto" class="w-full h-auto"/>
                    </div>
                    <canvas id="photoCanvas" class="hidden"></canvas>
                    <input type="hidden" id="buktiFotoData" name="buktiFotoData" required>
                    
                    <!-- Pesan error kamera -->
                    <p id="cameraError" class="text-red-500 text-sm mt-2"></p>

                    <!-- Tombol Aksi Kamera -->
                    <div class="mt-4 flex items-center justify-center space-x-3">
                        <button type="button" id="startCameraButton" class="px-4 py-2 bg-indigo-600 text-white rounded-md hover:bg-indigo-700 transition-all shadow-sm">
                            Mulai Kamera
                        </button>
                        <button type="button" id="captureButton" class="hidden px-4 py-2 bg-green-600 text-white rounded-md hover:bg-green-700 transition-all shadow-sm">
                            Ambil Foto
                        </button>
                        <button type="button" id="retakeButton" class="hidden px-4 py-2 bg-yellow-500 text-white rounded-md hover:bg-yellow-600 transition-all shadow-sm">
                            Ulangi
                        </button>
                    </div>
                </div>
            </div>
            
            <!-- Pesan Status -->
            <div id="statusMessage" class="text-center font-medium text-base"></div>

            <!-- Tombol Kirim -->
            <div>
                <button type="submit" id="submitButton"
                        class="w-full flex justify-center py-3 px-4 border border-transparent rounded-md shadow-lg text-base font-medium text-white bg-indigo-600 hover:bg-indigo-700 focus:outline-none focus:ring-2 focus:ring-offset-2 focus:ring-indigo-500 transition-transform transform hover:scale-105">
                    Kirim Data
                </button>
            </div>
        </form>
    </div>

    <!-- INILAH BAGIAN SCRIPT YANG DIMAKSUD -->
    <script>
        // --- Elemen DOM ---
        const form = document.getElementById('ijazahForm');
        const statusMessage = document.getElementById('statusMessage');
        const submitButton = document.getElementById('submitButton');
        const cameraStream = document.getElementById('cameraStream');
        const photoCanvas = document.getElementById('photoCanvas');
        const photoPreview = document.getElementById('photoPreview');
        const buktiFotoData = document.getElementById('buktiFotoData');
        const cameraError = document.getElementById('cameraError');
        const startCameraButton = document.getElementById('startCameraButton');
        const captureButton = document.getElementById('captureButton');
        const retakeButton = document.getElementById('retakeButton');

        let streamInstance = null;

        // --- Fungsi UI Kamera (tidak berubah) ---
        function showCameraUI() {
            startCameraButton.classList.add('hidden');
            captureButton.classList.remove('hidden');
            retakeButton.classList.add('hidden');
            cameraStream.style.display = 'block';
            photoPreview.style.display = 'none';
            cameraError.textContent = '';
        }

        function showPreviewUI() {
            captureButton.classList.add('hidden');
            retakeButton.classList.remove('hidden');
            cameraStream.style.display = 'none';
            photoPreview.style.display = 'block';
        }

        function resetCameraUI() {
            startCameraButton.classList.remove('hidden');
            captureButton.classList.add('hidden');
            retakeButton.classList.add('hidden');
            cameraStream.style.display = 'none';
            photoPreview.style.display = 'none';
            buktiFotoData.value = '';
            if (streamInstance) {
                streamInstance.getTracks().forEach(track => track.stop());
                streamInstance = null;
            }
        }

        // --- Event Listener Kamera (tidak berubah) ---
        startCameraButton.addEventListener('click', async () => {
            if (!navigator.mediaDevices || !navigator.mediaDevices.getUserMedia) {
                cameraError.textContent = 'Browser Anda tidak mendukung akses kamera.';
                return;
            }
            try {
                streamInstance = await navigator.mediaDevices.getUserMedia({ video: { facingMode: 'user' } });
                cameraStream.srcObject = streamInstance;
                showCameraUI();
            } catch (err) {
                console.error("Error accessing camera: ", err);
                if (err.name === "NotAllowedError" || err.name === "PermissionDeniedError") {
                    cameraError.textContent = 'Izin akses kamera ditolak.';
                } else {
                    cameraError.textContent = 'Tidak dapat mengakses kamera.';
                }
            }
        });

        captureButton.addEventListener('click', () => {
            if (!streamInstance) return;
            const context = photoCanvas.getContext('2d');
            photoCanvas.width = cameraStream.videoWidth;
            photoCanvas.height = cameraStream.videoHeight;
            context.drawImage(cameraStream, 0, 0, photoCanvas.width, photoCanvas.height);
            const dataUrl = photoCanvas.toDataURL('image/jpeg', 0.9);
            photoPreview.src = dataUrl;
            buktiFotoData.value = dataUrl;
            if (streamInstance) {
                streamInstance.getTracks().forEach(track => track.stop());
                streamInstance = null;
            }
            showPreviewUI();
        });

        retakeButton.addEventListener('click', () => {
            buktiFotoData.value = '';
            startCameraButton.click();
        });

        // --- Event Listener untuk Submit Form (BAGIAN YANG DIPERBARUI) ---
        form.addEventListener('submit', (event) => {
            event.preventDefault();

            // GANTI DENGAN URL WEB APP ANDA DARI GOOGLE APPS SCRIPT
            const scriptURL = 'https://script.google.com/macros/s/AKfycby-2_yc7MVZwCnpvL-VCaPIDfsfqAw0tOWJBzDdYtx1r2maOz6BBmDn53HOaHYZ9u1iqw/exec';

            if (!buktiFotoData.value) {
                statusMessage.textContent = 'Mohon ambil bukti foto terlebih dahulu.';
                statusMessage.className = 'text-center font-medium text-red-600';
                return;
            }

            statusMessage.textContent = 'Mengirim data, mohon tunggu...';
            statusMessage.className = 'text-center font-medium text-blue-600';
            submitButton.disabled = true;
            submitButton.textContent = 'Memproses...';
            submitButton.classList.add('opacity-50', 'cursor-not-allowed');

            const formData = new FormData(form);
            const data = Object.fromEntries(formData.entries());

            fetch(scriptURL, {
                method: 'POST',
                mode: 'cors', // Diperlukan untuk request cross-origin
                body: JSON.stringify(data),
                headers: { 'Content-Type': 'text/plain;charset=utf-8' } // Apps Script lebih baik menerima text/plain
            })
            .then(response => response.json())
            .then(result => {
                console.log('Success:', result);
                if(result.status === 'sukses'){
                    statusMessage.textContent = 'Data Anda berhasil terkirim!';
                    statusMessage.className = 'text-center font-medium text-green-700 bg-green-100 p-3 rounded-md';
                    form.reset();
                    resetCameraUI();
                } else {
                    throw new Error(result.message || 'Terjadi kesalahan saat mengirim data.');
                }
            })
            .catch(error => {
                console.error('Error:', error);
                statusMessage.textContent = 'Gagal mengirim data. ' + error.message;
                statusMessage.className = 'text-center font-medium text-red-600 bg-red-100 p-3 rounded-md';
            })
            .finally(() => {
                 // Aktifkan kembali tombol setelah beberapa saat
                setTimeout(() => {
                    statusMessage.textContent = '';
                    statusMessage.className = 'text-center font-medium text-base';
                    submitButton.disabled = false;
                    submitButton.textContent = 'Kirim Data';
                    submitButton.classList.remove('opacity-50', 'cursor-not-allowed');
                }, 5000);
            });
        });
    </script>
</body>
</html>
