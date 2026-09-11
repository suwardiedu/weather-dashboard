# weather-dashboard
A modern weather dashboard th```html
<!DOCTYPE html>
<html lang="id" class="h-full bg-slate-900">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Sistem Manajemen Rental Mobil Modern</title>
    <!-- Tailwind CSS CDN -->
    <script src="https://cdn.tailwindcss.com"></script>
    <!-- FontAwesome Icon CDN -->
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.5.1/css/all.min.css">
    <!-- Google Font Plus Jakarta Sans -->
    <link href="https://fonts.googleapis.com/css2?family=Plus+Jakarta+Sans:wght@300;400;500;600;700;800&display=swap" rel="stylesheet">
    
    <script>
        tailwind.config = {
            theme: {
                extend: {
                    fontFamily: {
                        sans: ['Plus Jakarta Sans', 'sans-serif'],
                    },
                    colors: {
                        brand: {
                            50: '#eff6ff',
                            100: '#dbeafe',
                            500: '#3b82f6',
                            600: '#2563eb',
                            700: '#1d4ed8',
                            900: '#1e3a8a',
                        }
                    }
                }
            }
        }
    </script>
    <style>
        body { font-family: 'Plus Jakarta Sans', sans-serif; }
        :root {
            --brand-color: #2563eb;
            --brand-dark: #1d4ed8;
        }
        .bg-brand { background-color: var(--brand-color); }
        .text-brand { color: var(--brand-color); }
        .border-brand { border-color: var(--brand-color); }
        .hover\:bg-brand-dark:hover { background-color: var(--brand-dark); }
        .glass-panel {
            background: rgba(255, 255, 255, 0.85);
            backdrop-filter: blur(12px);
            -webkit-backdrop-filter: blur(12px);
        }
        /* Style Cetak Nota untuk Mode Print Browser */
        @media print {
            body * { visibility: hidden; }
            #receiptPrintModal, #receiptPrintModal * { visibility: visible; }
            #receiptPrintModal { position: absolute; left: 0; top: 0; width: 100%; height: 100%; background: white; z-index: 9999; padding: 20px; }
        }
    </style>
</head>
<body class="bg-slate-950 text-slate-100 min-h-screen flex flex-col antialiased selection:bg-blue-500 selection:text-white">


    <!-- Header Navigasi Utama Perusahaan -->
    <header class="sticky top-0 z-40 border-b border-slate-800 bg-slate-900/80 backdrop-blur-md">
        <div class="max-w-7xl mx-auto px-4 sm:px-6 lg:px-8 h-20 flex items-center justify-between">
            <div class="flex items-center space-x-3.5">
                <div class="relative">
                    <img id="headerLogo" src="https://placehold.co/120x120/2563eb/ffffff?text=RENTAL" alt="Logo Perusahaan" class="w-12 h-12 rounded-2xl object-cover border-2 border-blue-500/30 shadow-lg shadow-blue-500/20">
                    <span class="absolute -bottom-1 -right-1 w-4 h-4 bg-emerald-500 border-2 border-slate-900 rounded-full"></span>
                </div>
                <div>
                    <h1 id="headerName" class="font-extrabold text-lg text-white tracking-tight leading-tight">Rental Mobil App</h1>
                    <p id="headerSlogan" class="text-xs text-slate-400 font-medium">Solusi Management Fleet Terpercaya</p>
                </div>
            </div>
            
            <div id="userInfoHeader" class="hidden flex items-center space-x-4">
                <span id="userBadge" class="text-[11px] px-3 py-1 rounded-full font-bold uppercase tracking-wider bg-blue-500/20 text-blue-400 border border-blue-500/30">Admin</span>
                <span id="userNameHeader" class="text-sm font-semibold text-slate-200 hidden sm:inline">User</span>
                <button onclick="logout()" class="px-3.5 py-2 bg-slate-800 hover:bg-rose-600/20 hover:text-rose-400 border border-slate-700 text-slate-300 text-xs font-semibold rounded-xl transition-all duration-200 flex items-center gap-2">
                    <i class="fa-solid fa-power-off"></i>
                    <span class="hidden sm:inline">Keluar</span>
                </button>
            </div>
        </div>
    </header>


    <!-- Floating Toast Notification System -->
    <div id="toast" class="fixed top-24 right-5 z-50 transform translate-x-full transition-all duration-300 ease-out bg-slate-900/95 text-white px-5 py-3.5 rounded-2xl shadow-2xl border border-slate-700 flex items-center space-x-3 backdrop-blur-lg">
        <div id="toastIconContainer" class="w-8 h-8 rounded-xl bg-emerald-500/20 text-emerald-400 flex items-center justify-center">
            <i id="toastIcon" class="fa-solid fa-circle-check text-base"></i>
        </div>
        <span id="toastMessage" class="text-xs font-semibold text-slate-200">Notifikasi Sistem</span>
    </div>


    <!-- Modal Tangkap Foto Kamera Langsung -->
    <div id="cameraModal" class="fixed inset-0 bg-slate-950/80 backdrop-blur-md z-50 hidden flex items-center justify-center p-4">
        <div class="bg-slate-900 border border-slate-800 w-full max-w-lg rounded-3xl overflow-hidden shadow-2xl flex flex-col">
            <div class="p-4 border-b border-slate-800 flex justify-between items-center">
                <h3 class="font-bold text-sm text-white flex items-center gap-2">
                    <i class="fa-solid fa-camera text-blue-500"></i> Ambil Foto Kamera Langsung
                </h3>
                <button onclick="closeCameraModal()" class="w-8 h-8 rounded-full bg-slate-800 text-slate-400 hover:text-white flex items-center justify-center"><i class="fa-solid fa-xmark"></i></button>
            </div>
            <div class="p-4 flex flex-col items-center justify-center bg-black/40">
                <video id="cameraVideo" autoplay playsinline class="w-full max-h-[360px] object-cover rounded-2xl border border-slate-800 bg-black"></video>
            </div>
            <div class="p-4 bg-slate-900 border-t border-slate-800 flex justify-between items-center">
                <button onclick="closeCameraModal()" class="px-4 py-2 text-xs font-medium text-slate-400 hover:text-white">Batal</button>
                <button onclick="capturePhoto()" class="px-6 py-2.5 bg-blue-600 hover:bg-blue-500 text-white text-xs font-bold rounded-xl shadow-lg shadow-blue-500/30 flex items-center gap-2">
                    <i class="fa-solid fa-circle"></i> Tangkap Foto
                </button>
            </div>
        </div>
    </div>


    <!-- Container Konten Utama Aplikasi -->
    <main class="flex-1 max-w-7xl w-full mx-auto p-4 sm:p-6 lg:p-8">


        <!-- VIEW: Portal Login Multi-Role -->
        <section id="viewLogin" class="max-w-md mx-auto my-8 sm:my-14 bg-slate-900/90 rounded-3xl shadow-2xl border border-slate-800 overflow-hidden backdrop-blur-xl">
            <div class="p-8 bg-gradient-to-br from-blue-600 via-indigo-600 to-slate-900 text-white text-center relative overflow-hidden">
                <div class="absolute -right-8 -top-8 w-32 h-32 bg-white/10 rounded-full blur-2xl"></div>
                <div class="w-20 h-20 bg-white/10 rounded-3xl mx-auto flex items-center justify-center mb-4 backdrop-blur-md border border-white/20 shadow-xl">
                    <i class="fa-solid fa-car-side text-3xl text-blue-200"></i>
                </div>
                <h2 class="text-2xl font-extrabold tracking-tight">Portal Masuk System</h2>
                <p class="text-blue-200 text-xs mt-1.5 font-medium">Pilih peran akun Anda untuk mengelola armada</p>
            </div>


            <div class="p-6 sm:p-8">
                <!-- Selector Tab Peran User -->
                <div class="grid grid-cols-3 gap-1.5 p-1.5 bg-slate-950 rounded-2xl border border-slate-800 mb-6">
                    <button type="button" onclick="setLoginRole('admin')" id="roleBtnAdmin" class="py-2.5 text-xs font-bold rounded-xl transition-all duration-200 bg-blue-600 text-white shadow-lg shadow-blue-600/30">Admin</button>
                    <button type="button" onclick="setLoginRole('owner')" id="roleBtnOwner" class="py-2.5 text-xs font-bold rounded-xl transition-all duration-200 text-slate-400 hover:text-white">Owner</button>
                    <button type="button" onclick="setLoginRole('driver')" id="roleBtnDriver" class="py-2.5 text-xs font-bold rounded-xl transition-all duration-200 text-slate-400 hover:text-white">Supir</button>
                </div>


                <form onsubmit="handleLogin(event)" class="space-y-4">
                    <input type="hidden" id="loginRole" value="admin">
                    <div>
                        <label class="block text-xs font-bold text-slate-400 uppercase tracking-wider mb-2">Username</label>
                        <div class="relative">
                            <span class="absolute inset-y-0 left-0 flex items-center pl-3.5 text-slate-500"><i class="fa-solid fa-user-circle"></i></span>
                            <input type="text" id="loginUsername" required class="w-full pl-10 pr-4 py-3 bg-slate-950 border border-slate-800 rounded-2xl text-sm text-white focus:outline-none focus:border-blue-500 focus:ring-1 focus:ring-blue-500 transition" placeholder="Username akun">
                        </div>
                    </div>
                    <div>
                        <label class="block text-xs font-bold text-slate-400 uppercase tracking-wider mb-2">Password</label>
                        <div class="relative">
                            <span class="absolute inset-y-0 left-0 flex items-center pl-3.5 text-slate-500"><i class="fa-solid fa-key"></i></span>
                            <input type="password" id="loginPassword" required class="w-full pl-10 pr-4 py-3 bg-slate-950 border border-slate-800 rounded-2xl text-sm text-white focus:outline-none focus:border-blue-500 focus:ring-1 focus:ring-blue-500 transition" placeholder="••••••••">
                        </div>
                    </div>
                    <button type="submit" class="w-full py-3.5 bg-blue-600 hover:bg-blue-500 text-white font-bold rounded-2xl transition shadow-xl shadow-blue-600/30 flex items-center justify-center gap-2 text-sm mt-2">
                        <span>Masuk Dashboard</span>
                        <i class="fa-solid fa-arrow-right"></i>
                    </button>
                </form>


                <div class="mt-6 p-4 bg-slate-950/60 rounded-2xl border border-slate-800/80 text-xs text-slate-400 space-y-1.5">
                    <p class="font-bold text-slate-300">Akun Demo Default:</p>
                    <p class="flex justify-between"><span>• Admin:</span> <span class="font-mono text-blue-400">admin / 123</span></p>
                    <p class="flex justify-between"><span>• Owner:</span> <span class="font-mono text-emerald-400">owner1 / 123</span></p>
                    <p class="flex justify-between"><span>• Supir:</span> <span class="font-mono text-amber-400">supir1 / 123</span></p>
                </div>
            </div>
        </section>


        <!-- VIEW: Panel Administrator (Full Control) -->
        <section id="viewAdmin" class="hidden space-y-6">
            <div class="flex flex-col lg:flex-row lg:items-center lg:justify-between gap-4 bg-slate-900 p-6 rounded-3xl border border-slate-800 shadow-xl">
                <div>
                    <h2 class="text-xl font-black text-white tracking-tight">Panel Utama Administrator</h2>
                    <p class="text-xs text-slate-400 mt-1">Kelola profil, pendaftaran owner 3-sisi foto, akun supir, & konsolidasi laporan setoran.</p>
                </div>
                <!-- Admin Sub-Nav Tabs -->
                <div class="flex flex-wrap gap-2">
                    <button onclick="switchAdminTab('profil')" id="tabAdminProfil" class="px-4 py-2.5 text-xs font-bold rounded-2xl bg-blue-600 text-white transition shadow-lg shadow-blue-600/30">Profil Perusahaan</button>
                    <button onclick="switchAdminTab('owner')" id="tabAdminOwner" class="px-4 py-2.5 text-xs font-bold rounded-2xl bg-slate-800 text-slate-300 hover:bg-slate-700 transition">Reg. Owner & Mobil</button>
                    <button onclick="switchAdminTab('supir')" id="tabAdminSupir" class="px-4 py-2.5 text-xs font-bold rounded-2xl bg-slate-800 text-slate-300 hover:bg-slate-700 transition">Reg. Supir</button>
                    <button onclick="switchAdminTab('setoran')" id="tabAdminSetoran" class="px-4 py-2.5 text-xs font-bold rounded-2xl bg-slate-800 text-slate-300 hover:bg-slate-700 transition">Laporan Setoran Hub</button>
                </div>
            </div>


            <!-- Tab Admin 1: Profil Perusahaan & Bank -->
            <div id="adminTabContentProfil" class="bg-slate-900 p-6 sm:p-8 rounded-3xl border border-slate-800 shadow-xl">
                <h3 class="text-base font-bold text-white mb-6 pb-3 border-b border-slate-800 flex items-center gap-2.5">
                    <i class="fa-solid fa-building-user text-blue-500"></i> Edit Profil Perusahaan & Rekening Pembayaran
                </h3>
                <form onsubmit="saveCompanyProfile(event)" class="space-y-5">
                    <div class="grid grid-cols-1 md:grid-cols-2 gap-5">
                        <div>
                            <label class="block text-xs font-bold text-slate-400 uppercase tracking-wider mb-2">Nama Rental / Perusahaan</label>
                            <input type="text" id="cfgName" required class="w-full p-3 bg-slate-950 border border-slate-800 rounded-2xl text-sm text-white focus:outline-none focus:border-blue-500">
                        </div>
                        <div>
                            <label class="block text-xs font-bold text-slate-400 uppercase tracking-wider mb-2">Slogan Perusahaan</label>
                            <input type="text" id="cfgSlogan" required class="w-full p-3 bg-slate-950 border border-slate-800 rounded-2xl text-sm text-white focus:outline-none focus:border-blue-500">
                        </div>
                    </div>
                    <div>
                        <label class="block text-xs font-bold text-slate-400 uppercase tracking-wider mb-2">Alamat Lengkap Perusahaan</label>
                        <textarea id="cfgAddress" rows="2" required class="w-full p-3 bg-slate-950 border border-slate-800 rounded-2xl text-sm text-white focus:outline-none focus:border-blue-500"></textarea>
                    </div>


                    <div class="grid grid-cols-1 md:grid-cols-2 gap-5">
                        <div>
                            <label class="block text-xs font-bold text-slate-400 uppercase tracking-wider mb-2">Warna Aksen Dashboard</label>
                            <div class="flex items-center space-x-3">
                                <input type="color" id="cfgThemeColor" class="w-14 h-11 border border-slate-800 rounded-xl cursor-pointer bg-slate-950 p-1">
                                <span class="text-xs text-slate-400">Pilih warna branding tema</span>
                            </div>
                        </div>


                        <div>
                            <label class="block text-xs font-bold text-slate-400 uppercase tracking-wider mb-2">Logo Perusahaan (Upload / Kamera)</label>
                            <div class="flex flex-col sm:flex-row gap-3 items-start sm:items-center">
                                <input type="file" id="cfgLogoFile" accept="image/*" onchange="previewImage(this, 'cfgLogoPreview')" class="hidden">
                                <button type="button" onclick="document.getElementById('cfgLogoFile').click()" class="px-4 py-2.5 bg-slate-800 hover:bg-slate-700 text-xs font-bold text-slate-300 rounded-xl border border-slate-700 flex items-center gap-2">
                                    <i class="fa-solid fa-upload"></i> Upload Logo
                                </button>
                                <button type="button" onclick="openCameraModal(url => setPhotoPreview('cfgLogoPreview', url))" class="px-4 py-2.5 bg-blue-600/20 hover:bg-blue-600/30 text-blue-400 border border-blue-500/30 text-xs font-bold rounded-xl flex items-center gap-2">
                                    <i class="fa-solid fa-camera"></i> Ambil Foto Kamera
                                </button>
                            </div>
                            <img id="cfgLogoPreview" class="mt-3 h-16 w-16 object-cover rounded-2xl border-2 border-slate-700 shadow-md">
                        </div>
                    </div>


                    <!-- Rekening Bank Section -->
                    <div class="pt-5 border-t border-slate-800">
                        <h4 class="text-sm font-bold text-white mb-4 flex items-center gap-2">
                            <i class="fa-solid fa-credit-card text-emerald-400"></i> Rekening Integrasi Untuk Supir Transfer
                        </h4>
                        <div class="grid grid-cols-1 md:grid-cols-3 gap-4">
                            <div>
                                <label class="block text-xs font-bold text-slate-400 uppercase tracking-wider mb-2">Nama Bank</label>
                                <input type="text" id="cfgBankName" placeholder="Contoh: BCA / Mandiri" required class="w-full p-3 bg-slate-950 border border-slate-800 rounded-2xl text-sm text-white focus:outline-none">
                            </div>
                            <div>
                                <label class="block text-xs font-bold text-slate-400 uppercase tracking-wider mb-2">No. Rekening</label>
                                <input type="text" id="cfgBankAcc" placeholder="1234567890" required class="w-full p-3 bg-slate-950 border border-slate-800 rounded-2xl text-sm text-white focus:outline-none">
                            </div>
                            <div>
                                <label class="block text-xs font-bold text-slate-400 uppercase tracking-wider mb-2">Atas Nama (A/N)</label>
                                <input type="text" id="cfgBankHolder" placeholder="PT Rental Mobil" required class="w-full p-3 bg-slate-950 border border-slate-800 rounded-2xl text-sm text-white focus:outline-none">
                            </div>
                        </div>
                    </div>


                    <button type="submit" class="px-6 py-3 bg-blue-600 hover:bg-blue-500 text-white font-bold text-xs rounded-2xl transition shadow-lg shadow-blue-600/30 flex items-center gap-2">
                        <i class="fa-solid fa-floppy-disk"></i> Simpan Profil Perusahaan
                    </button>
                </form>
            </div>


            <!-- Tab Admin 2: Reg Owner & Foto Mobil 3 Sisi -->
            <div id="adminTabContentOwner" class="hidden bg-slate-900 p-6 sm:p-8 rounded-3xl border border-slate-800 shadow-xl">
                <h3 class="text-base font-bold text-white mb-6 pb-3 border-b border-slate-800 flex items-center gap-2.5">
                    <i class="fa-solid fa-user-plus text-blue-500"></i> Pendaftaran Owner & Tambah Mobil (Foto 3 Sisi)
                </h3>
                <form onsubmit="registerOwner(event)" class="space-y-6">
                    <div class="grid grid-cols-1 md:grid-cols-3 gap-4">
                        <div>
                            <label class="block text-xs font-bold text-slate-400 uppercase tracking-wider mb-2">Nama Owner</label>
                            <input type="text" id="regOwnerName" required class="w-full p-3 bg-slate-950 border border-slate-800 rounded-2xl text-sm text-white focus:outline-none">
                        </div>
                        <div>
                            <label class="block text-xs font-bold text-slate-400 uppercase tracking-wider mb-2">Username Login Owner</label>
                            <input type="text" id="regOwnerUser" required class="w-full p-3 bg-slate-950 border border-slate-800 rounded-2xl text-sm text-white focus:outline-none">
                        </div>
                        <div>
                            <label class="block text-xs font-bold text-slate-400 uppercase tracking-wider mb-2">Password Login</label>
                            <input type="password" id="regOwnerPass" required class="w-full p-3 bg-slate-950 border border-slate-800 rounded-2xl text-sm text-white focus:outline-none">
                        </div>
                    </div>


                    <div class="grid grid-cols-1 md:grid-cols-3 gap-4">
                        <div>
                            <label class="block text-xs font-bold text-slate-400 uppercase tracking-wider mb-2">Nama Mobil & Plat Nomor</label>
                            <input type="text" id="regCarName" placeholder="Avanza Veloz - B 1234 ABC" required class="w-full p-3 bg-slate-950 border border-slate-800 rounded-2xl text-sm text-white focus:outline-none">
                        </div>
                        <div>
                            <label class="block text-xs font-bold text-slate-400 uppercase tracking-wider mb-2">Setoran Kotor Per Hari (Rp)</label>
                            <input type="number" id="regCarDeposit" placeholder="150000" required class="w-full p-3 bg-slate-950 border border-slate-800 rounded-2xl text-sm text-white focus:outline-none">
                        </div>
                        <div>
                            <label class="block text-xs font-bold text-slate-400 uppercase tracking-wider mb-2">Biaya Admin Rental (Rp)</label>
                            <input type="number" id="regCarAdminFee" placeholder="10000" required class="w-full p-3 bg-slate-950 border border-slate-800 rounded-2xl text-sm text-white focus:outline-none">
                        </div>
                    </div>


                    <!-- Upload / Foto Kamera 3 Angle Kendaraan -->
                    <div>
                        <label class="block text-xs font-bold text-slate-300 uppercase tracking-wider mb-3">Foto Kendaraan 3 Sisi (Depan, Samping, Belakang)</label>
                        <div class="grid grid-cols-1 md:grid-cols-3 gap-4">
                            <!-- Tampak Depan -->
                            <div class="p-4 bg-slate-950 rounded-2xl border border-slate-800 space-y-3">
                                <span class="text-xs font-bold text-blue-400 block"><i class="fa-solid fa-car"></i> 1. Tampak Depan</span>
                                <div class="flex gap-2">
                                    <input type="file" id="regCarPhotoFront" accept="image/*" onchange="previewImage(this, 'previewFront')" class="hidden">
                                    <button type="button" onclick="document.getElementById('regCarPhotoFront').click()" class="w-1/2 py-2 bg-slate-800 text-[11px] font-bold text-slate-300 rounded-xl">File</button>
                                    <button type="button" onclick="openCameraModal(url => setPhotoPreview('previewFront', url))" class="w-1/2 py-2 bg-blue-600/20 text-blue-400 text-[11px] font-bold rounded-xl"><i class="fa-solid fa-camera"></i> Kamera</button>
                                </div>
                                <img id="previewFront" class="w-full h-28 object-cover rounded-xl border border-slate-800 hidden">
                            </div>


                            <!-- Tampak Samping -->
                            <div class="p-4 bg-slate-950 rounded-2xl border border-slate-800 space-y-3">
                                <span class="text-xs font-bold text-blue-400 block"><i class="fa-solid fa-car-side"></i> 2. Tampak Samping</span>
                                <div class="flex gap-2">
                                    <input type="file" id="regCarPhotoSide" accept="image/*" onchange="previewImage(this, 'previewSide')" class="hidden">
                                    <button type="button" onclick="document.getElementById('regCarPhotoSide').click()" class="w-1/2 py-2 bg-slate-800 text-[11px] font-bold text-slate-300 rounded-xl">File</button>
                                    <button type="button" onclick="openCameraModal(url => setPhotoPreview('previewSide', url))" class="w-1/2 py-2 bg-blue-600/20 text-blue-400 text-[11px] font-bold rounded-xl"><i class="fa-solid fa-camera"></i> Kamera</button>
                                </div>
                                <img id="previewSide" class="w-full h-28 object-cover rounded-xl border border-slate-800 hidden">
                            </div>


                            <!-- Tampak Belakang -->
                            <div class="p-4 bg-slate-950 rounded-2xl border border-slate-800 space-y-3">
                                <span class="text-xs font-bold text-blue-400 block"><i class="fa-solid fa-car"></i> 3. Tampak Belakang</span>
                                <div class="flex gap-2">
                                    <input type="file" id="regCarPhotoRear" accept="image/*" onchange="previewImage(this, 'previewRear')" class="hidden">
                                    <button type="button" onclick="document.getElementById('regCarPhotoRear').click()" class="w-1/2 py-2 bg-slate-800 text-[11px] font-bold text-slate-300 rounded-xl">File</button>
                                    <button type="button" onclick="openCameraModal(url => setPhotoPreview('previewRear', url))" class="w-1/2 py-2 bg-blue-600/20 text-blue-400 text-[11px] font-bold rounded-xl"><i class="fa-solid fa-camera"></i> Kamera</button>
                                </div>
                                <img id="previewRear" class="w-full h-28 object-cover rounded-xl border border-slate-800 hidden">
                            </div>
                        </div>
                    </div>


                    <button type="submit" class="px-6 py-3 bg-emerald-600 hover:bg-emerald-500 text-white font-bold text-xs rounded-2xl transition shadow-lg shadow-emerald-600/30 flex items-center gap-2">
                        <i class="fa-solid fa-plus"></i> Daftarkan Owner & Armada
                    </button>
                </form>


                <!-- Tabel Owner Terdaftar -->
                <div class="mt-8 pt-6 border-t border-slate-800">
                    <h4 class="text-sm font-bold text-white mb-4">Daftar Owner & Kendaraan Terdaftar</h4>
                    <div class="overflow-x-auto">
                        <table class="w-full text-left text-xs">
                            <thead class="bg-slate-950 text-slate-400 uppercase font-bold tracking-wider">
                                <tr>
                                    <th class="p-3.5 rounded-l-2xl">Owner</th>
                                    <th class="p-3.5">Mobil & Plat</th>
                                    <th class="p-3.5">Foto 3 Sisi</th>
                                    <th class="p-3.5">Setoran Kotor</th>
                                    <th class="p-3.5">Biaya Admin</th>
                                    <th class="p-3.5 rounded-r-2xl">Hasil Bersih Owner</th>
                                </tr>
                            </thead>
                            <tbody id="tableAdminOwners" class="divide-y divide-slate-800/60">
                                <!-- Dynamic rows -->
                            </tbody>
                        </table>
                    </div>
                </div>
            </div>


            <!-- Tab Admin 3: Pendaftaran Supir Baru -->
            <div id="adminTabContentSupir" class="hidden bg-slate-900 p-6 sm:p-8 rounded-3xl border border-slate-800 shadow-xl">
                <h3 class="text-base font-bold text-white mb-6 pb-3 border-b border-slate-800 flex items-center gap-2.5">
                    <i class="fa-solid fa-id-card text-blue-500"></i> Pendaftaran Supir Baru
                </h3>
                <form onsubmit="registerDriver(event)" class="space-y-5">
                    <div class="grid grid-cols-1 md:grid-cols-3 gap-4">
                        <div>
                            <label class="block text-xs font-bold text-slate-400 uppercase tracking-wider mb-2">Nama Supir</label>
                            <input type="text" id="regDriverName" required class="w-full p-3 bg-slate-950 border border-slate-800 rounded-2xl text-sm text-white focus:outline-none">
                        </div>
                        <div>
                            <label class="block text-xs font-bold text-slate-400 uppercase tracking-wider mb-2">Username Login Supir</label>
                            <input type="text" id="regDriverUser" required class="w-full p-3 bg-slate-950 border border-slate-800 rounded-2xl text-sm text-white focus:outline-none">
                        </div>
                        <div>
                            <label class="block text-xs font-bold text-slate-400 uppercase tracking-wider mb-2">Password Login</label>
                            <input type="password" id="regDriverPass" required class="w-full p-3 bg-slate-950 border border-slate-800 rounded-2xl text-sm text-white focus:outline-none">
                        </div>
                    </div>


                    <div class="grid grid-cols-1 md:grid-cols-2 gap-5">
                        <div>
                            <label class="block text-xs font-bold text-slate-400 uppercase tracking-wider mb-2">Pilih Mobil Yang Ditugaskan</label>
                            <select id="regDriverCar" required class="w-full p-3 bg-slate-950 border border-slate-800 rounded-2xl text-sm text-white focus:outline-none">
                                <!-- Options populated dynamically -->
                            </select>
                        </div>
                        <div>
                            <label class="block text-xs font-bold text-slate-400 uppercase tracking-wider mb-2">Foto Diri Supir</label>
                            <div class="flex gap-3 items-center">
                                <input type="file" id="regDriverPhotoFile" accept="image/*" onchange="previewImage(this, 'regDriverPreview')" class="hidden">
                                <button type="button" onclick="document.getElementById('regDriverPhotoFile').click()" class="px-4 py-2.5 bg-slate-800 text-xs font-bold text-slate-300 rounded-xl">Upload Foto</button>
                                <button type="button" onclick="openCameraModal(url => setPhotoPreview('regDriverPreview', url))" class="px-4 py-2.5 bg-blue-600/20 text-blue-400 text-xs font-bold rounded-xl"><i class="fa-solid fa-camera"></i> Ambil Foto Kamera</button>
                            </div>
                            <img id="regDriverPreview" class="mt-3 h-20 w-20 object-cover rounded-full border-2 border-slate-700 hidden">
                        </div>
                    </div>


                    <p class="text-xs text-amber-400 bg-amber-950/30 p-3.5 rounded-2xl border border-amber-800/40 leading-relaxed">
                        <i class="fa-solid fa-shield-halved mr-1"></i> Catatan Sistem: Dalam portal supir, biaya admin rental otomatis disembunyikan. Supir hanya melihat nominal total setoran per hari sesuai kesepakatan.
                    </p>


                    <button type="submit" class="px-6 py-3 bg-blue-600 hover:bg-blue-500 text-white font-bold text-xs rounded-2xl transition shadow-lg shadow-blue-600/30 flex items-center gap-2">
                        <i class="fa-solid fa-user-plus"></i> Daftarkan Supir
                    </button>
                </form>


                <!-- Tabel Supir Aktif -->
                <div class="mt-8 pt-6 border-t border-slate-800">
                    <h4 class="text-sm font-bold text-white mb-4">Daftar Supir Aktif</h4>
                    <div class="overflow-x-auto">
                        <table class="w-full text-left text-xs">
                            <thead class="bg-slate-950 text-slate-400 uppercase font-bold tracking-wider">
                                <tr>
                                    <th class="p-3.5 rounded-l-2xl">Foto</th>
                                    <th class="p-3.5">Nama Supir</th>
                                    <th class="p-3.5">Username</th>
                                    <th class="p-3.5">Mobil Ditugaskan</th>
                                    <th class="p-3.5 rounded-r-2xl">Setoran Per Hari (Murni Supir)</th>
                                </tr>
                            </thead>
                            <tbody id="tableAdminDrivers" class="divide-y divide-slate-800/60">
                                <!-- Dynamic rows -->
                            </tbody>
                        </table>
                    </div>
                </div>
            </div>


            <!-- Tab Admin 4: Hub Setoran & Rekap Seluruh Mobil -->
            <div id="adminTabContentSetoran" class="hidden bg-slate-900 p-6 sm:p-8 rounded-3xl border border-slate-800 shadow-xl space-y-6">
                <div class="flex flex-col sm:flex-row sm:items-center justify-between gap-4 pb-4 border-b border-slate-800">
                    <div>
                        <h3 class="text-base font-bold text-white flex items-center gap-2">
                            <i class="fa-solid fa-file-invoice-dollar text-blue-500"></i> Laporan Setoran & Konsolidasi Seluruh Mobil
                        </h3>
                        <p class="text-xs text-slate-400 mt-0.5">Semua laporan setoran muncul secara langsung dan dapat di-generate sekaligus atau per mobil.</p>
                    </div>
                    <button onclick="generateAllOwnerReports()" class="px-5 py-2.5 bg-emerald-600 hover:bg-emerald-500 text-white text-xs font-bold rounded-2xl transition shadow-lg shadow-emerald-600/20 flex items-center gap-2">
                        <i class="fa-solid fa-paper-plane"></i> Kirim Laporan Semua Mobil Ke Owner
                    </button>
                </div>


                <!-- Master Ringkasan Semua Armada -->
                <div>
                    <h4 class="text-xs font-bold text-blue-400 uppercase tracking-wider mb-3"><i class="fa-solid fa-layer-group"></i> Ringkasan Konsolidasi Seluruh Armada</h4>
                    <div class="overflow-x-auto">
                        <table class="w-full text-left text-xs">
                            <thead class="bg-slate-950 text-slate-400 uppercase font-bold tracking-wider">
                                <tr>
                                    <th class="p-3.5 rounded-l-2xl">Armada / Mobil</th>
                                    <th class="p-3.5">Total Kotor</th>
                                    <th class="p-3.5">Total Admin Fee</th>
                                    <th class="p-3.5">Total Biaya Servis</th>
                                    <th class="p-3.5">Hasil Net Owner</th>
                                    <th class="p-3.5 rounded-r-2xl text-right">Aksi Generate</th>
                                </tr>
                            </thead>
                            <tbody id="tableAdminAllCarsOverview" class="divide-y divide-slate-800/60">
                                <!-- Dynamic list of all cars summary -->
                            </tbody>
                        </table>
                    </div>
                </div>


                <!-- Detail Mobil & Input Potongan Servis -->
                <div class="pt-6 border-t border-slate-800 space-y-4">
                    <div class="flex flex-col sm:flex-row sm:items-center justify-between gap-4">
                        <label class="text-xs font-bold text-slate-300 uppercase tracking-wider">Pilih Folder Mobil Untuk Rincian Servis & Setoran Detail:</label>
                        <select id="adminFilterCar" onchange="renderAdminSetoran()" class="p-3 bg-slate-950 border border-slate-800 rounded-2xl text-xs text-white outline-none w-full sm:w-72 font-semibold">
                            <!-- Dynamic options -->
                        </select>
                    </div>


                    <!-- Form Input Potongan Servis Perbaikan -->
                    <div class="p-4 bg-slate-950/80 rounded-2xl border border-slate-800 space-y-3">
                        <h4 class="text-xs font-bold text-amber-400 uppercase tracking-wider"><i class="fa-solid fa-wrench"></i> Tambah Biaya Servis / Perbaikan Mobil Ini</h4>
                        <div class="grid grid-cols-1 md:grid-cols-3 gap-3">
                            <input type="number" id="inputServiceCost" placeholder="Nominal Biaya Servis (Rp)" class="p-3 bg-slate-900 border border-slate-800 rounded-xl text-xs text-white outline-none">
                            <input type="text" id="inputServiceNote" placeholder="Keterangan (misal: Ganti Oli / Sparepart)" class="p-3 bg-slate-900 border border-slate-800 rounded-xl text-xs text-white outline-none">
                            <button onclick="addServiceCost()" class="bg-amber-600 hover:bg-amber-500 text-white font-bold text-xs px-4 py-3 rounded-xl transition flex items-center justify-center gap-2">
                                <i class="fa-solid fa-plus-circle"></i> Simpan Biaya Servis
                            </button>
                        </div>
                    </div>


                    <!-- Ringkasan Kartu Angka Mobil Terpilih -->
                    <div class="grid grid-cols-2 sm:grid-cols-4 gap-3">
                        <div class="bg-slate-950 p-4 rounded-2xl border border-slate-800">
                            <span class="text-[10px] text-slate-400 font-bold block uppercase">Setoran Kotor</span>
                            <span id="sumGrossDeposit" class="text-base font-extrabold text-blue-400">Rp 0</span>
                        </div>
                        <div class="bg-slate-950 p-4 rounded-2xl border border-slate-800">
                            <span class="text-[10px] text-slate-400 font-bold block uppercase">Potongan Admin</span>
                            <span id="sumAdminFee" class="text-base font-extrabold text-rose-400">Rp 0</span>
                        </div>
                        <div class="bg-slate-950 p-4 rounded-2xl border border-slate-800">
                            <span class="text-[10px] text-slate-400 font-bold block uppercase">Potongan Servis</span>
                            <span id="sumServiceCost" class="text-base font-extrabold text-amber-400">Rp 0</span>
                        </div>
                        <div class="bg-slate-950 p-4 rounded-2xl border border-slate-800">
                            <span class="text-[10px] text-slate-400 font-bold block uppercase">Hasil Net Owner</span>
                            <span id="sumNetOwner" class="text-base font-extrabold text-emerald-400">Rp 0</span>
                        </div>
                    </div>


                    <!-- Tabel Item Setoran Supir -->
                    <div class="overflow-x-auto">
                        <table class="w-full text-left text-xs">
                            <thead class="bg-slate-950 text-slate-400 uppercase font-bold tracking-wider">
                                <tr>
                                    <th class="p-3.5 rounded-l-2xl">Tanggal Setoran</th>
                                    <th class="p-3.5">Nama Supir</th>
                                    <th class="p-3.5">Bukti Transfer</th>
                                    <th class="p-3.5">Setoran Kotor</th>
                                    <th class="p-3.5">Potongan Admin</th>
                                    <th class="p-3.5 rounded-r-2xl">Status</th>
                                </tr>
                            </thead>
                            <tbody id="tableAdminSetoranList" class="divide-y divide-slate-800/60">
                                <!-- Dynamic items -->
                            </tbody>
                        </table>
                    </div>


                    <div class="flex justify-end pt-3">
                        <button onclick="sendSingleReportToOwner()" class="px-6 py-3 bg-blue-600 hover:bg-blue-500 text-white font-bold text-xs rounded-2xl transition shadow-lg shadow-blue-600/30 flex items-center gap-2">
                            <i class="fa-solid fa-paper-plane"></i> Kirim Laporan & Nota Mobil Ini
                        </button>
                    </div>
                </div>
            </div>
        </section>


        <!-- VIEW: Portal Pemilik Mobil (Owner Dashboard) -->
        <section id="viewOwner" class="hidden space-y-6">
            <div class="bg-slate-900 p-6 sm:p-8 rounded-3xl border border-slate-800 shadow-xl flex flex-col sm:flex-row items-start sm:items-center justify-between gap-4">
                <div>
                    <h2 class="text-xl font-black text-white tracking-tight">Portal Pemilik Kendaraan (Owner)</h2>
                    <p class="text-xs text-slate-400 mt-1">Pantau kendaraan 3-sisi foto Anda, rincian biaya admin, servis, & laporan setoran bersih.</p>
                </div>
                <button onclick="exportOwnerReportToExcel()" class="px-5 py-3 bg-emerald-600 hover:bg-emerald-500 text-white font-bold text-xs rounded-2xl transition shadow-lg shadow-emerald-600/30 flex items-center gap-2">
                    <i class="fa-solid fa-file-excel"></i> Export Excel (CSV)
                </button>
            </div>


            <!-- Grid Kendaraan Owner -->
            <div class="grid grid-cols-1 lg:grid-cols-2 gap-6" id="ownerCarsGrid">
                <!-- Dynamic Car Cards dengan foto 3 angle -->
            </div>


            <!-- Tabel Laporan Setoran Bersih Owner -->
            <div class="bg-slate-900 p-6 sm:p-8 rounded-3xl border border-slate-800 shadow-xl">
                <h3 class="text-base font-bold text-white mb-6 pb-3 border-b border-slate-800 flex items-center gap-2">
                    <i class="fa-solid fa-receipt text-emerald-400"></i> Laporan Setoran Mingguan / Bulanan & Nota Dari Admin
                </h3>


                <div class="overflow-x-auto">
                    <table class="w-full text-left text-xs">
                        <thead class="bg-slate-950 text-slate-400 uppercase font-bold tracking-wider">
                            <tr>
                                <th class="p-3.5 rounded-l-2xl">Mobil</th>
                                <th class="p-3.5">Tanggal Laporan</th>
                                <th class="p-3.5">Total Setoran Kotor</th>
                                <th class="p-3.5">Potongan Admin</th>
                                <th class="p-3.5">Potongan Servis</th>
                                <th class="p-3.5">Total Net Diterima</th>
                                <th class="p-3.5 rounded-r-2xl text-right">Nota Resmi</th>
                            </tr>
                        </thead>
                        <tbody id="tableOwnerReportBody" class="divide-y divide-slate-800/60">
                            <!-- Dynamic Content -->
                        </tbody>
                    </table>
                </div>
            </div>
        </section>


        <!-- VIEW: Portal Supir Kendaraan -->
        <section id="viewDriver" class="hidden space-y-6">
            <!-- Header Kartu Supir -->
            <div class="bg-slate-900 p-6 sm:p-8 rounded-3xl border border-slate-800 shadow-xl flex flex-col sm:flex-row items-center gap-6">
                <img id="driverPhotoDisplay" src="https://placehold.co/150x150/2563eb/ffffff?text=SUPIR" class="w-24 h-24 rounded-3xl object-cover border-2 border-blue-500/30 shadow-lg shadow-blue-500/20">
                <div class="flex-1 space-y-1.5 text-center sm:text-left">
                    <h2 id="driverNameDisplay" class="text-xl font-black text-white">Nama Supir</h2>
                    <p id="driverCarInfo" class="text-xs text-slate-400 font-medium">Mobil Ditugaskan: Avanza - B 1234 ABC</p>
                    <p id="driverRateInfo" class="text-xs text-blue-400 font-bold">Setoran Per Hari: Rp 150.000 / Hari</p>
                </div>
                <div class="bg-slate-950 p-4 rounded-2xl border border-slate-800 text-center sm:text-right w-full sm:w-auto">
                    <span class="text-[10px] text-amber-400 font-bold block uppercase tracking-wider">Jatah Libur Bulan Ini</span>
                    <span id="driverOffStatus" class="text-xs font-extrabold text-amber-300">1 Hari (Tersedia)</span>
                </div>
            </div>


            <div class="grid grid-cols-1 lg:grid-cols-3 gap-6">
                <!-- Kalender & Form Setoran Supir -->
                <div class="lg:col-span-2 bg-slate-900 p-6 sm:p-8 rounded-3xl border border-slate-800 shadow-xl space-y-5">
                    <h3 class="text-base font-bold text-white flex items-center justify-between pb-3 border-b border-slate-800">
                        <span><i class="fa-solid fa-calendar-days text-blue-500"></i> Kalender Hitung Setoran Supir</span>
                        <span class="text-xs text-slate-400 font-normal">Pilih rentang tanggal setoran</span>
                    </h3>


                    <div class="grid grid-cols-1 sm:grid-cols-2 gap-4">
                        <div>
                            <label class="block text-xs font-bold text-slate-400 uppercase tracking-wider mb-2">Dari Tanggal</label>
                            <input type="date" id="driverStartDate" onchange="calculateDriverDeposit()" class="w-full p-3 bg-slate-950 border border-slate-800 rounded-2xl text-xs text-white focus:outline-none focus:border-blue-500">
                        </div>
                        <div>
                            <label class="block text-xs font-bold text-slate-400 uppercase tracking-wider mb-2">Sampai Tanggal</label>
                            <input type="date" id="driverEndDate" onchange="calculateDriverDeposit()" class="w-full p-3 bg-slate-950 border border-slate-800 rounded-2xl text-xs text-white focus:outline-none focus:border-blue-500">
                        </div>
                    </div>


                    <!-- Kotak Kalkulasi Setoran -->
                    <div class="p-4 bg-slate-950 rounded-2xl border border-slate-800 space-y-2.5">
                        <div class="flex justify-between text-xs text-slate-400">
                            <span>Jumlah Hari Dipilih:</span>
                            <span id="calcDays" class="font-bold text-white">0 Hari</span>
                        </div>
                        <div class="flex justify-between text-xs text-slate-400">
                            <span>Potongan Libur (1 Hari/Bln):</span>
                            <span id="calcOffDays" class="font-bold text-amber-400">0 Hari</span>
                        </div>
                        <div class="flex justify-between text-sm font-extrabold text-white pt-2 border-t border-slate-800">
                            <span>Total Nominal Setoran:</span>
                            <span id="calcTotalAmount" class="text-blue-400">Rp 0</span>
                        </div>
                    </div>


                    <!-- Tombol Klaim Libur -->
                    <div class="flex items-center justify-between p-4 bg-amber-950/20 rounded-2xl border border-amber-800/40">
                        <div class="space-y-0.5">
                            <span class="text-xs font-bold text-amber-300 block">Gunakan Jatah Libur Bulan Ini?</span>
                            <span class="text-[10px] text-amber-400/80">1 hari bebas setoran per bulan</span>
                        </div>
                        <button id="btnClaimOff" onclick="claimMonthlyOff()" class="px-4 py-2 bg-amber-600 hover:bg-amber-500 text-white text-xs font-bold rounded-xl transition">
                            Ambil Libur
                        </button>
                    </div>


                    <!-- Form Unggah Bukti Transfer -->
                    <form onsubmit="submitDriverDeposit(event)" class="space-y-4 pt-3 border-t border-slate-800">
                        <h4 class="text-xs font-bold text-slate-300 uppercase tracking-wider">Unggah Bukti Transfer Setoran</h4>
                        
                        <div>
                            <label class="block text-xs text-slate-400 mb-2">Pilih Foto Bukti Transfer (File atau Kamera Langsung):</label>
                            <div class="flex gap-3 items-center">
                                <input type="file" id="driverTransferProofFile" accept="image/*" onchange="previewImage(this, 'proofPreview')" class="hidden">
                                <button type="button" onclick="document.getElementById('driverTransferProofFile').click()" class="px-4 py-2.5 bg-slate-800 hover:bg-slate-700 text-xs font-bold text-slate-300 rounded-xl">
                                    <i class="fa-solid fa-file-image"></i> Pilih File
                                </button>
                                <button type="button" onclick="openCameraModal(url => setPhotoPreview('proofPreview', url))" class="px-4 py-2.5 bg-blue-600/20 text-blue-400 border border-blue-500/30 text-xs font-bold rounded-xl">
                                    <i class="fa-solid fa-camera"></i> Foto Kamera Langsung
                                </button>
                            </div>
                            <img id="proofPreview" class="mt-3 h-36 w-full object-contain bg-slate-950 rounded-2xl border border-slate-800 hidden">
                        </div>


                        <button type="submit" class="w-full py-3.5 bg-emerald-600 hover:bg-emerald-500 text-white font-bold text-xs rounded-2xl transition shadow-lg shadow-emerald-600/30 flex items-center justify-center gap-2">
                            <i class="fa-solid fa-paper-plane"></i>
                            <span>Kirim Bukti Transfer Ke Admin</span>
                        </button>
                    </form>
                </div>


                <!-- Riwayat Setoran Supir -->
                <div class="bg-slate-900 p-6 sm:p-8 rounded-3xl border border-slate-800 shadow-xl space-y-4">
                    <h3 class="text-base font-bold text-white pb-3 border-b border-slate-800 flex items-center gap-2">
                        <i class="fa-solid fa-clock-rotate-left text-blue-500"></i> Riwayat Setoran Anda
                    </h3>
                    <div id="driverDepositHistory" class="space-y-3 max-h-[460px] overflow-y-auto pr-1">
                        <!-- Dynamic History Cards -->
                    </div>
                </div>
            </div>
        </section>


    </main>


    <!-- Modal Nota / Bukti Pembayaran Lunas -->
    <div id="receiptModal" class="fixed inset-0 bg-slate-950/80 backdrop-blur-md z-50 hidden flex items-center justify-center p-4">
        <div class="bg-white w-full max-w-lg rounded-3xl shadow-2xl overflow-hidden flex flex-col max-h-[90vh] text-slate-800">
            <div class="p-4 bg-slate-100 border-b border-slate-200 flex justify-between items-center">
                <h3 class="font-bold text-xs uppercase tracking-wider text-slate-700 flex items-center gap-2">
                    <i class="fa-solid fa-receipt text-blue-600"></i> Nota Pembayaran Setoran Resmi
                </h3>
                <button onclick="closeReceiptModal()" class="w-7 h-7 rounded-full bg-slate-200 hover:bg-slate-300 text-slate-600 flex items-center justify-center"><i class="fa-solid fa-xmark"></i></button>
            </div>


            <!-- Area Nota Yang Ditingkatkan Untuk Cetak PDF/Printer -->
            <div id="receiptPrintModal" class="p-6 space-y-6 overflow-y-auto">
                <div class="flex justify-between items-start border-b pb-4 border-slate-200">
                    <div class="flex items-center space-x-3">
                        <img id="receiptModalLogo" src="" class="w-12 h-12 rounded-xl object-cover border border-slate-200">
                        <div>
                            <h2 id="receiptModalName" class="font-bold text-base text-slate-900">Nama Rental</h2>
                            <p id="receiptModalAddress" class="text-[11px] text-slate-500 max-w-[200px]">Alamat Perusahaan</p>
                        </div>
                    </div>
                    <div class="text-right">
                        <span class="text-[10px] uppercase font-bold text-emerald-700 bg-emerald-100 px-2.5 py-1 rounded-full border border-emerald-300">LUNAS</span>
                        <p id="receiptModalDate" class="text-[10px] text-slate-400 mt-1.5">Tanggal</p>
                    </div>
                </div>


                <div class="space-y-2.5 text-xs">
                    <div class="flex justify-between py-1 border-b border-slate-100">
                        <span class="text-slate-500">Nama Supir / Pengirim:</span>
                        <span id="receiptModalDriver" class="font-bold text-slate-800">-</span>
                    </div>
                    <div class="flex justify-between py-1 border-b border-slate-100">
                        <span class="text-slate-500">Kendaraan & Plat:</span>
                        <span id="receiptModalCar" class="font-bold text-slate-800">-</span>
                    </div>
                    <div class="flex justify-between py-1 border-b border-slate-100">
                        <span class="text-slate-500">Jumlah Hari Setoran:</span>
                        <span id="receiptModalDays" class="font-bold text-slate-800">- Hari</span>
                    </div>
                    <div class="flex justify-between py-1 border-b border-slate-100">
                        <span class="text-slate-500">Nominal Rate per Hari:</span>
                        <span id="receiptModalRate" class="font-bold text-slate-800">Rp 0</span>
                    </div>
                    <div class="flex justify-between py-2.5 text-sm font-extrabold bg-blue-50 px-3.5 rounded-2xl border border-blue-200 text-slate-900">
                        <span>Total Nominal Lunas:</span>
                        <span id="receiptModalTotal" class="text-blue-600">Rp 0</span>
                    </div>
                </div>


                <div class="text-center text-[10px] text-slate-400 pt-4 border-t border-slate-100 space-y-1">
                    <p id="receiptModalSlogan" class="italic">Slogan Perusahaan</p>
                    <p>Terima kasih atas kerja samanya. Harap simpan bukti nota ini.</p>
                </div>
            </div>


            <div class="p-4 bg-slate-50 border-t border-slate-200 flex justify-end gap-2">
                <button onclick="closeReceiptModal()" class="px-4 py-2 text-xs font-bold text-slate-600 hover:bg-slate-200 rounded-xl transition">Tutup</button>
                <button onclick="window.print()" class="px-5 py-2 bg-blue-600 hover:bg-blue-700 text-white font-bold text-xs rounded-xl transition flex items-center gap-2 shadow">
                    <i class="fa-solid fa-print"></i> Cetak / Save PDF
                </button>
            </div>
        </div>
    </div>


    <!-- LOGIKA JAVASCRIPT LENGKAP SEMENTARA & INTEGRASI DATA -->
    <script>
        // State Manajemen Data Aplikasi
        let state = {
            currentUser: null,
            company: {
                name: 'Rental Mobil Prima Utama',
                slogan: 'Mitra Transportasi Terpercaya Anda',
                address: 'Jl. Merdeka No. 123, Jakarta Selatan',
                themeColor: '#2563eb',
                logo: 'https://placehold.co/120x120/2563eb/ffffff?text=RENTAL',
                bankName: 'BCA',
                bankAcc: '8830129841',
                bankHolder: 'PT Rental Mobil Prima'
            },
            users: [
                { id: 'u1', role: 'admin', username: 'admin', password: '123', name: 'Administrator System' },
                { id: 'u2', role: 'owner', username: 'owner1', password: '123', name: 'Bapak H. Ahmad' },
                { id: 'u3', role: 'driver', username: 'supir1', password: '123', name: 'Budi Santoso', carId: 'c1', photo: 'https://placehold.co/150x150/2563eb/ffffff?text=BUDI', offUsed: false }
            ],
            cars: [
                { 
                    id: 'c1', 
                    ownerId: 'u2', 
                    name: 'Avanza Veloz - B 1234 ABC', 
                    dailyDeposit: 150000, 
                    adminFee: 10000, 
                    photos: {
                        front: 'https://placehold.co/300x200/1e293b/ffffff?text=Depan',
                        side: 'https://placehold.co/300x200/1e293b/ffffff?text=Samping',
                        rear: 'https://placehold.co/300x200/1e293b/ffffff?text=Belakang'
                    }
                }
            ],
            deposits: [], // Tempat menampung setoran supir
            services: [], // Tempat menampung biaya perbaikan/servis
            ownerReports: [] // Tempat menampung rekap laporan ke owner
        };


        // Pengelola Kamera Langsung & Preview Gambar
        let currentCameraStream = null;
        let currentCameraCallback = null;


        async function openCameraModal(callback) {
            currentCameraCallback = callback;
            const modal = document.getElementById('cameraModal');
            const video = document.getElementById('cameraVideo');
            modal.classList.remove('hidden');


            try {
                currentCameraStream = await navigator.mediaDevices.getUserMedia({
                    video: { facingMode: 'environment' },
                    audio: false
                });
                video.srcObject = currentCameraStream;
            } catch (err) {
                showToast('Gagal mengakses kamera: ' + err.message, 'error');
                closeCameraModal();
            }
        }


        function capturePhoto() {
            const video = document.getElementById('cameraVideo');
            const canvas = document.createElement('canvas');
            canvas.width = video.videoWidth || 640;
            canvas.height = video.videoHeight || 480;
            const ctx = canvas.getContext('2d');
            ctx.drawImage(video, 0, 0, canvas.width, canvas.height);
            const dataUrl = canvas.toDataURL('image/jpeg');


            if (currentCameraCallback) {
                currentCameraCallback(dataUrl);
            }
            closeCameraModal();
        }


        function closeCameraModal() {
            const modal = document.getElementById('cameraModal');
            const video = document.getElementById('cameraVideo');
            if (currentCameraStream) {
                currentCameraStream.getTracks().forEach(track => track.stop());
                currentCameraStream = null;
            }
            video.srcObject = null;
            modal.classList.add('hidden');
        }


        function setPhotoPreview(elementId, dataUrl) {
            const img = document.getElementById(elementId);
            img.src = dataUrl;
            img.classList.remove('hidden');
        }


        function previewImage(input, previewId) {
            const file = input.files[0];
            if (file) {
                const reader = new FileReader();
                reader.onload = function(e) {
                    setPhotoPreview(previewId, e.target.result);
                };
                reader.readAsDataURL(file);
            }
        }


        // Fungsi Notifikasi Floating Toast
        function showToast(message, type = 'success') {
            const toast = document.getElementById('toast');
            const toastMsg = document.getElementById('toastMessage');
            const toastIconContainer = document.getElementById('toastIconContainer');
            const toastIcon = document.getElementById('toastIcon');


            toastMsg.innerText = message;
            if(type === 'success') {
                toastIconContainer.className = 'w-8 h-8 rounded-xl bg-emerald-500/20 text-emerald-400 flex items-center justify-center';
                toastIcon.className = 'fa-solid fa-circle-check text-base';
            } else {
                toastIconContainer.className = 'w-8 h-8 rounded-xl bg-rose-500/20 text-rose-400 flex items-center justify-center';
                toastIcon.className = 'fa-solid fa-circle-exclamation text-base';
            }


            toast.classList.remove('translate-x-full');
            setTimeout(() => {
                toast.classList.add('translate-x-full');
            }, 3200);
        }


        // Logika Otentikasi & Navigasi
        function setLoginRole(role) {
            document.getElementById('loginRole').value = role;
            ['Admin', 'Owner', 'Driver'].forEach(r => {
                const btn = document.getElementById('roleBtn' + r);
                if (r.toLowerCase() === role) {
                    btn.className = 'py-2.5 text-xs font-bold rounded-xl transition-all duration-200 bg-blue-600 text-white shadow-lg shadow-blue-600/30';
                } else {
                    btn.className = 'py-2.5 text-xs font-bold rounded-xl transition-all duration-200 text-slate-400 hover:text-white';
                }
            });
        }


        function handleLogin(e) {
            e.preventDefault();
            const role = document.getElementById('loginRole').value;
            const u = document.getElementById('loginUsername').value.trim();
            const p = document.getElementById('loginPassword').value.trim();


            const user = state.users.find(x => x.role === role && x.username === u && x.password === p);


            if (user) {
                state.currentUser = user;
                showToast('Login Berhasil! Selamat datang, ' + user.name);
                updateUIHeader();
                navigateView();
            } else {
                showToast('Username atau password tidak valid!', 'error');
            }
        }


        function logout() {
            state.currentUser = null;
            showToast('Berhasil keluar dari akun.');
            updateUIHeader();
            navigateView();
        }


        function updateUIHeader() {
            const info = document.getElementById('userInfoHeader');
            if (state.currentUser) {
                info.classList.remove('hidden');
                document.getElementById('userNameHeader').innerText = state.currentUser.name;
                const badge = document.getElementById('userBadge');
                badge.innerText = state.currentUser.role;
                if(state.currentUser.role === 'admin') badge.className = 'text-[11px] px-3 py-1 rounded-full font-bold uppercase tracking-wider bg-blue-500/20 text-blue-400 border border-blue-500/30';
                else if(state.currentUser.role === 'owner') badge.className = 'text-[11px] px-3 py-1 rounded-full font-bold uppercase tracking-wider bg-emerald-500/20 text-emerald-400 border border-emerald-500/30';
                else badge.className = 'text-[11px] px-3 py-1 rounded-full font-bold uppercase tracking-wider bg-amber-500/20 text-amber-400 border border-amber-500/30';
            } else {
                info.classList.add('hidden');
            }


            document.getElementById('headerName').innerText = state.company.name;
            document.getElementById('headerSlogan').innerText = state.company.slogan;
            document.getElementById('headerLogo').src = state.company.logo;
            document.documentElement.style.setProperty('--brand-color', state.company.themeColor);
        }


        function navigateView() {
            document.getElementById('viewLogin').classList.add('hidden');
            document.getElementById('viewAdmin').classList.add('hidden');
            document.getElementById('viewOwner').classList.add('hidden');
            document.getElementById('viewDriver').classList.add('hidden');


            if (!state.currentUser) {
                document.getElementById('viewLogin').classList.remove('hidden');
                return;
            }


            if (state.currentUser.role === 'admin') {
                document.getElementById('viewAdmin').classList.remove('hidden');
                loadAdminData();
            } else if (state.currentUser.role === 'owner') {
                document.getElementById('viewOwner').classList.remove('hidden');
                loadOwnerData();
            } else if (state.currentUser.role === 'driver') {
                document.getElementById('viewDriver').classList.remove('hidden');
                loadDriverData();
            }
        }


        // Logika Admin Panel
        function switchAdminTab(tab) {
            ['Profil', 'Owner', 'Supir', 'Setoran'].forEach(t => {
                const btn = document.getElementById('tabAdmin' + t);
                const content = document.getElementById('adminTabContent' + t);
                if (t.toLowerCase() === tab) {
                    btn.className = 'px-4 py-2.5 text-xs font-bold rounded-2xl bg-blue-600 text-white transition shadow-lg shadow-blue-600/30';
                    content.classList.remove('hidden');
                } else {
                    btn.className = 'px-4 py-2.5 text-xs font-bold rounded-2xl bg-slate-800 text-slate-300 hover:bg-slate-700 transition';
                    content.classList.add('hidden');
                }
            });
        }


        function loadAdminData() {
            document.getElementById('cfgName').value = state.company.name;
            document.getElementById('cfgSlogan').value = state.company.slogan;
            document.getElementById('cfgAddress').value = state.company.address;
            document.getElementById('cfgThemeColor').value = state.company.themeColor;
            document.getElementById('cfgBankName').value = state.company.bankName;
            document.getElementById('cfgBankAcc').value = state.company.bankAcc;
            document.getElementById('cfgBankHolder').value = state.company.bankHolder;
            document.getElementById('cfgLogoPreview').src = state.company.logo;


            renderAdminOwnerTable();
            renderAdminDriverTable();
            populateAdminCarSelects();
            renderAdminSetoranHub();
        }


        function saveCompanyProfile(e) {
            e.preventDefault();
            state.company.name = document.getElementById('cfgName').value;
            state.company.slogan = document.getElementById('cfgSlogan').value;
            state.company.address = document.getElementById('cfgAddress').value;
            state.company.themeColor = document.getElementById('cfgThemeColor').value;
            state.company.bankName = document.getElementById('cfgBankName').value;
            state.company.bankAcc = document.getElementById('cfgBankAcc').value;
            state.company.bankHolder = document.getElementById('cfgBankHolder').value;


            const logoPreview = document.getElementById('cfgLogoPreview');
            if (logoPreview.src) state.company.logo = logoPreview.src;


            updateUIHeader();
            showToast('Profil perusahaan & rekening berhasil diperbarui!');
        }


        function registerOwner(e) {
            e.preventDefault();
            const name = document.getElementById('regOwnerName').value;
            const username = document.getElementById('regOwnerUser').value;
            const password = document.getElementById('regOwnerPass').value;


            const carName = document.getElementById('regCarName').value;
            const dailyDeposit = parseFloat(document.getElementById('regCarDeposit').value);
            const adminFee = parseFloat(document.getElementById('regCarAdminFee').value);


            const front = document.getElementById('previewFront').src || 'https://placehold.co/300x200/1e293b/ffffff?text=Depan';
            const side = document.getElementById('previewSide').src || 'https://placehold.co/300x200/1e293b/ffffff?text=Samping';
            const rear = document.getElementById('previewRear').src || 'https://placehold.co/

at fetches real-time weather data from public APIs with beautiful UI and interactive features
