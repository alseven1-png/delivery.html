<!DOCTYPE html>
<html lang="es">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Delivery - Panadería y Cocina</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <link href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.0.0/css/all.min.css" rel="stylesheet">
    <style>
        .product-card { transition: all 0.2s; border-radius: 1.25rem; border: 1px solid #f3f4f6; }
        .product-card:active { transform: scale(0.96); }
        .btn-qty { width: 40px; height: 40px; display: flex; align-items: center; justify-content: center; border-radius: 50%; }
        .cart-footer { box-shadow: 0 -10px 25px rgba(0,0,0,0.1); border-radius: 2rem 2rem 0 0; }
        body { font-family: sans-serif; }
    </style>
</head>
<body class="bg-gray-50 pb-40">

    <header class="bg-amber-800 text-white p-6 sticky top-0 z-50 shadow-md">
        <div class="flex justify-between items-center max-w-md mx-auto">
            <div>
                <h1 class="text-2xl font-black italic tracking-tight">DELIVERY ONLINE</h1>
                <p class="text-xs text-amber-200 font-bold uppercase tracking-widest">Panadería y Cocina</p>
            </div>
            <div class="bg-amber-700 p-3 rounded-2xl border border-amber-600">
                <i class="fas fa-moped text-2xl text-amber-100"></i>
            </div>
        </div>
    </header>

    <main class="p-4 space-y-4 max-w-md mx-auto" id="lista-productos"></main>

    <div id="cart-bar" class="fixed bottom-0 left-0 right-0 bg-white p-6 cart-footer hidden z-50 border-t">
        <div class="max-w-md mx-auto">
            <div class="flex justify-between items-center mb-4">
                <div>
                    <p class="text-gray-400 text-xs font-bold uppercase">Total del pedido</p>
                    <p class="text-3xl font-black text-amber-900" id="total-view">$ 0</p>
                </div>
                <button onclick="enviarWhatsApp()" class="bg-green-600 hover:bg-green-700 text-white px-8 py-4 rounded-2xl font-bold shadow-lg flex items-center gap-3 transition-all active:scale-95">
                    PEDIR <i class="fab fa-whatsapp text-2xl"></i>
                </button>
            </div>
            <p class="text-[10px] text-center text-gray-500 font-bold uppercase tracking-wider">Al tocar se enviará el WhatsApp</p>
        </div>
    </div>

    <script>
        const WHATSAPP_NUMBER = "5492215087314";
        
        const PRODUCTOS = [
            { id: 1, nombre: "Empanada (Carne/Pollo/Verd/Hum/JyQ)", precio: 2700 },
            { id: 2, nombre: "Calzón", precio: 3200 },
            { id: 3, nombre: "Sándwich de Milanesa Grande", precio: 18000 },
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
                <div class="product-card bg-white p-5 shadow-sm flex items-center justify-between">
                    <div class="flex-1 pr-4">
                        <h3 class="font-bold text-gray-800 text-lg leading-tight italic">${p.nombre}</h3>
                        <p class="text-amber-700 font-black text-xl mt-1">$ ${p.precio.toLocaleString('es-AR')}</p>
                    </div>
                    <div class="flex items-center gap-3 bg-gray-50 rounded-full p-1 border">
                        <button onclick="updateCart(${p.id}, -1)" class="btn-qty bg-white text-gray-400 shadow-sm border border-gray-100"><i class="fas fa-minus"></i></button>
                        <span id="qty-${p.id}" class="w-6 text-center font-bold text-lg text-gray-700">0</span>
                        <button onclick="updateCart(${p.id}, 1)" class="btn-qty bg-amber-600 text-white shadow-md"><i class="fas fa-plus"></i></button>
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

        function enviarWhatsApp() {
            let texto = "🛒 *NUEVO PEDIDO DE COCINA*\n-----------------------------\n";
            let total = 0;
            PRODUCTOS.forEach(p => {
                if(carrito[p.id] > 0) {
                    texto += `• ${carrito[p.id]} x ${p.nombre}\n`;
                    total += (p.precio * carrito[p.id]);
                }
            });
            texto += `\n-----------------------------\n💰 *TOTAL: $ ${total.toLocaleString('es-AR')}*`;
            texto += `\n\n🏠 _Indique dirección y horario de entrega:_`;
            
            const url = `https://wa.me/${WHATSAPP_NUMBER}?text=${encodeURIComponent(texto)}`;
            window.open(url, '_blank');
        }

        render();
    </script>
</body>
</html>
