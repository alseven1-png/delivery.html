<!DOCTYPE html>
<html lang="es">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Delivery - Panadería y Cocina</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <link href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.0.0/css/all.min.css" rel="stylesheet">
    <style>
        .product-card { transition: all 0.2s; border-radius: 1.25rem; border: 1px solid #e5e7eb; }
        .product-card:active { transform: scale(0.97); }
        .btn-qty { width: 42px; height: 42px; display: flex; align-items: center; justify-content: center; border-radius: 50%; shadow: 0 2px 4px rgba(0,0,0,0.1); }
        .cart-footer { box-shadow: 0 -10px 30px rgba(0,0,0,0.15); border-radius: 2rem 2rem 0 0; }
        .modal { display: none; position: fixed; inset: 0; background: rgba(0,0,0,0.5); z-index: 100; align-items: center; justify-content: center; padding: 20px; }
        .modal.active { display: flex; }
    </style>
</head>
<body class="bg-slate-50 pb-40">

    <header class="bg-amber-900 text-white pt-10 pb-6 px-6 sticky top-0 z-50 shadow-xl border-b-4 border-amber-600">
        <div class="flex justify-between items-center max-w-md mx-auto">
            <div>
                <h1 class="text-3xl font-black italic tracking-tighter uppercase">Nuestra Cocina</h1>
                <p class="text-xs text-amber-300 font-bold tracking-[0.2em]">DELIVERY & TAKE AWAY</p>
            </div>
            <div class="bg-amber-800 p-4 rounded-3xl shadow-inner border border-amber-700">
                <i class="fas fa-utensils text-2xl text-amber-200"></i>
            </div>
        </div>
    </header>

    <div class="max-w-md mx-auto mt-4 px-4">
        <div class="bg-blue-50 border-l-4 border-blue-500 p-4 rounded-r-xl shadow-sm">
            <div class="flex items-center gap-3">
                <i class="fas fa-university text-blue-600 text-xl"></i>
                <div>
                    <p class="text-[10px] font-bold text-blue-400 uppercase tracking-widest leading-none">Aceptamos Transferencia</p>
                    <p class="text-sm font-black text-blue-800 uppercase tracking-tight">Alias: TU.ALIAS.AQUI</p>
                </div>
            </div>
        </div>
    </div>

    <main class="p-4 space-y-4 max-w-md mx-auto" id="lista-productos"></main>

    <div id="cart-bar" class="fixed bottom-0 left-0 right-0 bg-white p-6 cart-footer hidden z-50 border-t">
        <div class="max-w-md mx-auto flex justify-between items-center">
            <div>
                <p class="text-gray-400 text-[10px] font-black uppercase tracking-widest">Total Estimado</p>
                <p class="text-3xl font-black text-amber-900" id="total-view">$ 0</p>
            </div>
            <button onclick="abrirResumen()" class="bg-green-600 hover:bg-green-700 text-white px-10 py-5 rounded-2xl font-black shadow-lg flex items-center gap-3 transition-all active:scale-90">
                VER RESUMEN <i class="fas fa-chevron-right"></i>
            </button>
        </div>
    </div>

    <div id="modal-resumen" class="modal">
        <div class="bg-white w-full max-w-sm rounded-[2.5rem] overflow-hidden shadow-2xl animate-bounce-in">
            <div class="bg-amber-900 p-6 text-white text-center">
                <h2 class="text-xl font-black italic uppercase">Tu Pedido</h2>
            </div>
            <div class="p-6">
                <div id="detalle-lista" class="space-y-3 mb-6 max-h-60 overflow-y-auto font-medium text-gray-700">
                    </div>
                <hr class="mb-4">
                <div class="flex justify-between items-center mb-6">
                    <span class="font-bold text-gray-400 uppercase text-xs">A pagar:</span>
                    <span class="text-2xl font-black text-amber-900" id="total-modal">$ 0</span>
                </div>
                <div class="space-y-3">
                    <button onclick="enviarWhatsApp()" class="w-full bg-green-600 text-white py-5 rounded-2xl font-bold text-lg flex justify-center items-center gap-3">
                        CONFIRMAR POR WHATSAPP <i class="fab fa-whatsapp text-2xl"></i>
                    </button>
                    <button onclick="cerrarResumen()" class="w-full text-gray-400 font-bold text-sm uppercase tracking-widest py-2">
                        Seguir comprando
                    </button>
                </div>
            </div>
        </div>
    </div>

    <script>
        const WHATSAPP_NUMBER = "5492215087314";
        const PRODUCTOS = [
            { id: 1, nombre: "Empanada (Varios gustos)", precio: 2700 },
            { id: 2, nombre: "Calzón", precio: 3200 },
            { id: 3, nombre: "Sándwich de Mila Grande", precio: 18000 },
            { id: 4, nombre: "Sándwich de Miga Chico", precio: 9000 },
            { id: 5, nombre: "Tortilla de Papa", precio: 9100 },
            { id: 6, nombre: "Tortilla de Verdura", precio: 9100 },
            { id: 7, nombre: "Fatay", precio: 3200 },
            { id: 8, nombre: "Pizza Mozzarella (p/ hornear)", precio: 9300 },
            { id: 9, nombre: "Pizza Especial (p/ hornear)", precio: 10300 },
            { id: 10, nombre: "Triple Jamón y Queso", precio: 3100 },
            { id: 11, nombre: "Triple Surtido", precio: 3500 },
            { id: 12, nombre: "Matambre de Carne (Kg)", precio: 42000 },
            { id: 13, nombre: "Pastel de Papa", precio: 15000 },
            { id: 14, nombre: "Tarta Verdura (Individual)", precio: 10200 },
            { id: 15, nombre: "Tarta Pollo (Individual)", precio: 10200 },
            { id: 16, nombre: "Tarta JyQ (Individual)", precio: 10200 },
            { id: 17, nombre: "Milanesa con Fritas", precio: 18500 },
            { id: 18, nombre: "Milanesa Sola", precio: 16000 },
            { id: 19, nombre: "Milanesa con Ensalada", precio: 18500 }
        ];

        let carrito = {};

        function render() {
            const container = document.getElementById('lista-productos');
            container.innerHTML = PRODUCTOS.map(p => `
                <div class="product-card bg-white p-5 flex items-center justify-between shadow-sm">
                    <div class="flex-1 pr-4">
                        <h3 class="font-extrabold text-gray-800 text-lg leading-tight">${p.nombre}</h3>
                        <p class="text-amber-700 font-black text-xl mt-1">$ ${p.precio.toLocaleString('es-AR')}</p>
                    </div>
                    <div class="flex items-center gap-3 bg-gray-100 rounded-full p-1.5 border border-gray-200">
                        <button onclick="updateCart(${p.id}, -1)" class="btn-qty bg-white text-gray-400 shadow-sm border border-gray-100"><i class="fas fa-minus text-xs"></i></button>
                        <span id="qty-${p.id}" class="w-6 text-center font-black text-lg text-gray-800 uppercase">0</span>
                        <button onclick="updateCart(${p.id}, 1)" class="btn-qty bg-amber-600 text-white shadow-md"><i class="fas fa-plus text-xs"></i></button>
                    </div>
                </div>
            `).join('');
        }

        function updateCart(id, change) {
            carrito[id] = (carrito[id] || 0) + change;
            if (carrito[id] < 0) carrito[id] = 0;
            document.getElementById(`qty-${id}`).innerText = carrito[id];
            
            let total = 0;
            let items = 0;
            PRODUCTOS.forEach(p => {
                if(carrito[p.id]) {
                    total += (p.precio * carrito[p.id]);
                    items += carrito[p.id];
                }
            });

            const bar = document.getElementById('cart-bar');
            document.getElementById('total-view').innerText = `$ ${total.toLocaleString('es-AR')}`;
            if(items > 0) bar.classList.remove('hidden'); else bar.classList.add('hidden');
        }

        function abrirResumen() {
            const lista = document.getElementById('detalle-lista');
            let total = 0;
            let html = "";
            PRODUCTOS.forEach(p => {
                if(carrito[p.id] > 0) {
                    const subtotal = p.precio * carrito[p.id];
                    html += `<div class="flex justify-between border-b border-gray-100 pb-2">
                                <span><span class="font-black text-amber-700">${carrito[p.id]}x</span> ${p.nombre}</span>
                                <span class="font-bold">$ ${subtotal.toLocaleString('es-AR')}</span>
                             </div>`;
                    total += subtotal;
                }
            });
            lista.innerHTML = html;
            document.getElementById('total-modal').innerText = `$ ${total.toLocaleString('es-AR')}`;
            document.getElementById('modal-resumen').classList.add('active');
        }

        function cerrarResumen() {
            document.getElementById('modal-resumen').classList.remove('active');
        }

        function enviarWhatsApp() {
            let texto = "🛒 *NUEVO PEDIDO*\n-----------------------------\n";
            let total = 0;
            PRODUCTOS.forEach(p => {
                if(carrito[p.id] > 0) {
                    texto += `• ${carrito[p.id]} x ${p.nombre} ($${(p.precio * carrito[p.id]).toLocaleString('es-AR')})\n`;
                    total += (p.precio * carrito[p.id]);
                }
            });
            texto += `\n-----------------------------\n💰 *TOTAL: $ ${total.toLocaleString('es-AR')}*`;
            texto += `\n\n🏠 _Indique dirección de entrega:_`;
            texto += `\n💳 _¿Desea el link de pago/Alias?_`;
            
            const url = `https://wa.me/${WHATSAPP_NUMBER}?text=${encodeURIComponent(texto)}`;
            window.open(url, '_blank');
        }

        render();
    </script>
</body>
</html>
