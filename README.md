<!DOCTYPE html>
<html lang="es">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <title>Panel de Control - Cocina</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <link href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.0.0/css/all.min.css" rel="stylesheet">
    <style>
        .product-card { transition: all 0.2s; border-radius: 1rem; border: 1px solid #e5e7eb; overflow: hidden; height: 110px; }
        .modal { display: none; position: fixed; inset: 0; background: rgba(0,0,0,0.85); z-index: 100; align-items: center; justify-content: center; padding: 10px; backdrop-filter: blur(4px); }
        .modal.active { display: flex; }
        .bg-custom-header { background: linear-gradient(180deg, #451a03 0%, #78350f 100%); }
    </style>
</head>
<body class="bg-slate-100 pb-36 font-sans">

    <header class="bg-custom-header text-white pt-8 pb-4 px-5 sticky top-0 z-50 shadow-lg border-b-2 border-amber-600">
        <div class="flex justify-between items-center max-w-md mx-auto">
            <div onclick="checkAdmin()" class="cursor-pointer">
                <h1 class="text-2xl font-black italic tracking-tighter uppercase">Nuestra Cocina</h1>
                <p class="text-[10px] text-amber-400 font-bold tracking-[0.2em] leading-none uppercase">Admin oculto (3 clics)</p>
            </div>
            <button onclick="abrirPerfil()" class="bg-amber-800/60 p-3 rounded-2xl border border-amber-600 active:scale-95">
                <i class="fas fa-user text-xl text-amber-300"></i>
            </button>
        </div>
    </header>

    <main class="p-3 space-y-3 max-w-md mx-auto" id="lista-productos"></main>

    <div id="cart-bar" class="fixed bottom-0 left-0 right-0 bg-white p-4 hidden z-50 border-t rounded-t-[2rem] shadow-2xl">
        <div class="max-w-md mx-auto flex justify-between items-center gap-4">
            <div class="flex-1 leading-none">
                <p class="text-[10px] font-bold text-gray-400 uppercase tracking-widest">Total</p>
                <p class="text-3xl font-black text-amber-950" id="total-view">$ 0</p>
            </div>
            <button onclick="abrirResumen()" class="bg-amber-800 text-white px-8 py-4 rounded-xl font-black flex items-center gap-2 shadow-lg">
                PEDIR <i class="fas fa-moped text-xl"></i>
            </button>
        </div>
    </div>

    <div id="modal-admin" class="modal">
        <div class="bg-white w-full max-w-sm rounded-3xl overflow-hidden shadow-2xl flex flex-col max-h-[90vh]">
            <div class="bg-red-900 p-4 text-white flex justify-between items-center font-black italic">
                <span>PANEL DE CONTROL</span>
                <button onclick="cerrarAdmin()"><i class="fas fa-times"></i></button>
            </div>
            <div class="p-4 overflow-y-auto space-y-4" id="admin-items">
                </div>
            <div class="p-4 border-t">
                <button onclick="saveAdminData()" class="w-full bg-red-700 text-white py-4 rounded-xl font-black shadow-lg uppercase">Guardar Cambios</button>
            </div>
        </div>
    </div>

    <div id="modal-perfil" class="modal">... (código previo) ...</div>
    <div id="modal-resumen" class="modal">... (código previo) ...</div>

    <script>
        const WHATSAPP_NUMBER = "5492215087314";
        const ALIAS = "alseven1.uala";
        let clickCount = 0;

        // Lista inicial de productos
        let PRODUCTOS = [
            { id: 1, nombre: "Empanada (Varios gustos)", precio: 2700, img: "" },
            { id: 2, nombre: "Calzón", precio: 3200, img: "" },
            { id: 3, nombre: "Sándwich de Mila Grande", precio: 18000, img: "" },
            { id: 4, nombre: "Sándwich de Miga Chico", precio: 9000, img: "" },
            { id: 5, nombre: "Tortilla de Papa", precio: 9100, img: "" },
            { id: 6, nombre: "Tortilla de Verdura", precio: 9100, img: "" },
            { id: 7, nombre: "Fatay", precio: 3200, img: "" },
            { id: 8, nombre: "Pizza Mozzarella (p/ hornear)", precio: 9300, img: "" },
            { id: 9, nombre: "Pizza Especial (p/ hornear)", precio: 10300, img: "" },
            { id: 10, nombre: "Triple Jamón y Queso", precio: 3100, img: "" },
            { id: 11, nombre: "Triple Surtido", precio: 3500, img: "" },
            { id: 12, nombre: "Matambre de Carne (Kg)", precio: 42000, img: "" },
            { id: 13, nombre: "Pastel de Papa", precio: 15000, img: "" },
            { id: 14, nombre: "Tarta Verdura (Individual)", precio: 10200, img: "" },
            { id: 15, nombre: "Tarta Pollo (Individual)", precio: 10200, img: "" },
            { id: 16, nombre: "Tarta JyQ (Individual)", precio: 10200, img: "" },
            { id: 17, nombre: "Milanesa con Fritas", precio: 18500, img: "" },
            { id: 18, nombre: "Milanesa Sola", precio: 16000, img: "" },
            { id: 19, nombre: "Milanesa con Ensalada", precio: 18500, img: "" }
        ];

        let carrito = {};

        // Cargar datos guardados (Precios y fotos)
        if(localStorage.getItem('db_productos')) {
            PRODUCTOS = JSON.parse(localStorage.getItem('db_productos'));
        }

        function render() {
            const container = document.getElementById('lista-productos');
            container.innerHTML = PRODUCTOS.map(p => `
                <div class="product-card bg-white flex items-stretch shadow-sm border-l-4 border-l-amber-700">
                    <div class="w-[110px] min-w-[110px] bg-gray-100 flex items-center justify-center border-r">
                        ${p.img ? `<img src="${p.img}" class="w-full h-full object-cover">` : `<i class="fas fa-image text-gray-300 text-2xl"></i>`}
                    </div>
                    <div class="flex-1 p-3 flex flex-col justify-between">
                        <div>
                            <h3 class="font-extrabold text-gray-800 text-[13px] uppercase italic tracking-tighter">${p.nombre}</h3>
                            <p class="text-amber-700 font-black text-xl leading-none mt-1">$ ${p.precio.toLocaleString('es-AR')}</p>
                        </div>
                        <div class="flex items-center gap-2 bg-gray-50 rounded-full p-1 border self-start">
                            <button onclick="updateCart(${p.id}, -1)" class="w-8 h-8 rounded-full bg-white border"><i class="fas fa-minus text-[10px]"></i></button>
                            <span id="qty-${p.id}" class="w-4 text-center font-black text-lg">0</span>
                            <button onclick="updateCart(${p.id}, 1)" class="w-8 h-8 rounded-full bg-amber-700 text-white shadow"><i class="fas fa-plus text-[10px]"></i></button>
                        </div>
                    </div>
                </div>
            `).join('');
        }

        // PANEL ADMIN - FUNCIONES
        function checkAdmin() {
            clickCount++;
            if(clickCount === 3) {
                clickCount = 0;
                const pass = prompt("Clave de Administración:");
                if(pass === "1234") { // Cambia esta clave por la que quieras
                    abrirAdmin();
                }
            }
            setTimeout(() => clickCount = 0, 2000);
        }

        function abrirAdmin() {
            const list = document.getElementById('admin-items');
            list.innerHTML = PRODUCTOS.map(p => `
                <div class="bg-gray-50 p-3 rounded-xl border border-gray-200 space-y-2">
                    <p class="font-black text-[10px] text-gray-400 uppercase">${p.nombre}</p>
                    <div class="flex gap-2">
                        <input type="number" id="price-${p.id}" value="${p.precio}" placeholder="Precio" class="w-1/3 p-2 border rounded-lg text-sm font-bold">
                        <input type="text" id="img-${p.id}" value="${p.img}" placeholder="URL Foto" class="w-2/3 p-2 border rounded-lg text-[10px]">
                    </div>
                </div>
            `).join('');
            document.getElementById('modal-admin').classList.add('active');
        }

        function saveAdminData() {
            PRODUCTOS.forEach(p => {
                p.precio = parseInt(document.getElementById(`price-${p.id}`).value);
                p.img = document.getElementById(`img-${p.id}`).value;
            });
            localStorage.setItem('db_productos', JSON.stringify(PRODUCTOS));
            cerrarAdmin();
            render();
            alert("¡Cambios aplicados correctamente!");
        }

        function cerrarAdmin() { document.getElementById('modal-admin').classList.remove('active'); }

        // El resto de funciones (updateCart, abrirResumen, enviarWhatsApp) se mantienen igual...
        // [AQUÍ VA EL RESTO DEL CÓDIGO PREVIO PARA QUE FUNCIONE EL CARRITO]
        
        render();
    </script>
</body>
</html>
