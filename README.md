<!DOCTYPE html>
<html lang="es">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>FashionStyle Elegance</title>

<style>
:root{
    --primary:#111;
    --accent:#c59d5f;
}

*{
    box-sizing:border-box;
    margin:0;
    padding:0;
    font-family: 'Segoe UI', sans-serif;
}

body{
    background: linear-gradient(135deg,#1f1f1f,#2c2c2c);
    color:white;
}

/* HEADER */
header{
    display:flex;
    justify-content:space-between;
    align-items:center;
    padding:20px 40px;
    background: rgba(255,255,255,0.05);
    backdrop-filter: blur(10px);
    position:sticky;
    top:0;
}

.logo{
    font-size:22px;
    font-weight:bold;
    letter-spacing:1px;
}

.cart-btn{
    cursor:pointer;
    background:var(--accent);
    padding:8px 15px;
    border-radius:20px;
    border:none;
    color:white;
    font-weight:bold;
}

/* FILTROS */
.filters{
    text-align:center;
    margin:20px;
}

.filters button{
    margin:5px;
    padding:8px 15px;
    border:none;
    border-radius:20px;
    cursor:pointer;
    background:white;
    color:black;
    font-weight:bold;
}

/* PRODUCTOS */
.products{
    display:grid;
    grid-template-columns: repeat(auto-fit,minmax(250px,1fr));
    gap:20px;
    padding:20px 40px;
}

.card{
    background: rgba(255,255,255,0.07);
    border-radius:15px;
    padding:15px;
    text-align:center;
    transition:0.3s;
}

.card:hover{
    transform:translateY(-5px);
}

.card img{
    width:100%;
    height:350px;
    object-fit:cover;
    border-radius:10px;
    transition:0.3s;
}

.card:hover img{
    transform:scale(1.05);
}

.card button{
    margin-top:10px;
    padding:8px 15px;
    border:none;
    border-radius:20px;
    background:var(--accent);
    color:white;
    cursor:pointer;
    font-weight:bold;
}

/* MODAL CARRITO */
.modal{
    position:fixed;
    top:0;
    right:-400px;
    width:350px;
    height:100%;
    background:white;
    color:black;
    padding:20px;
    transition:0.4s;
    overflow-y:auto;
}

.modal.active{
    right:0;
}

.total{
    font-weight:bold;
    margin-top:10px;
}

/* CHATBOT */
#chat-container{
    position:fixed;
    bottom:20px;
    right:20px;
    width:340px;
    background:white;
    border-radius:15px;
    overflow:hidden;
    box-shadow:0 0 15px rgba(0,0,0,0.5);
}

#chat-header{
    background:var(--accent);
    padding:10px;
    color:white;
    text-align:center;
    font-weight:bold;
}

#chat-box{
    height:260px;
    overflow-y:auto;
    padding:10px;
    font-size:14px;
    color:black;
}

#chat-input{
    display:flex;
}

#chat-input input{
    flex:1;
    padding:10px;
    border:none;
    border-top:1px solid #ddd;
}

#chat-input button{
    background:var(--accent);
    border:none;
    padding:10px;
    color:white;
    font-weight:bold;
}
</style>
</head>
<body>

<header>
<div class="logo">✨ FashionStyle Elegance</div>
<button class="cart-btn" onclick="toggleCart()">🛒 Carrito (<span id="cart-count">0</span>)</button>
</header>

<div class="filters">
<button onclick="filterProducts('all')">Todos</button>
<button onclick="filterProducts('hombre')">Hombre</button>
<button onclick="filterProducts('mujer')">Mujer</button>
</div>

<section class="products" id="product-container"></section>

<!-- MODAL CARRITO -->
<div class="modal" id="cart-modal">
<h2>Tu Carrito</h2>
<div id="cart-items"></div>
<div class="total">Total: $<span id="cart-total">0</span></div>
<button onclick="toggleCart()">Cerrar</button>
</div>

<!-- CHATBOT -->
<div id="chat-container">
<div id="chat-header">Asistente Inteligente</div>
<div id="chat-box"></div>
<div id="chat-input">
<input type="text" id="user-input" placeholder="Escribe tu pregunta...">
<button onclick="sendMessage()">Enviar</button>
</div>
</div>

<script>

/* ================= PRODUCTOS ================= */

const products = [
{
id:1,
name:"Traje Ejecutivo Negro",
price:120,
category:"hombre",
img:"https://images.unsplash.com/photo-1593032465171-8d7e6b0b6f44?auto=format&fit=crop&w=800&q=80"
},
{
id:2,
name:"Blazer Elegante Azul",
price:85,
category:"hombre",
img:"https://images.unsplash.com/photo-1520975916090-3105956dac38?auto=format&fit=crop&w=800&q=80"
},
{
id:3,
name:"Vestido de Gala Rojo",
price:150,
category:"mujer",
img:"https://images.unsplash.com/photo-1524504388940-b1c1722653e1?auto=format&fit=crop&w=800&q=80"
},
{
id:4,
name:"Conjunto Formal Beige",
price:110,
category:"mujer",
img:"https://images.unsplash.com/photo-1542060748-10c28b62716f?auto=format&fit=crop&w=800&q=80"
}
];

const container = document.getElementById("product-container");
let cart = [];

function displayProducts(list){
container.innerHTML="";
list.forEach(p=>{
container.innerHTML += `
<div class="card">
<img src="${p.img}">
<h3>${p.name}</h3>
<p>$${p.price}</p>
<button onclick="addToCart(${p.id})">Agregar</button>
</div>`;
});
}

displayProducts(products);

function filterProducts(category){
if(category==="all") displayProducts(products);
else displayProducts(products.filter(p=>p.category===category));
}

/* ================= CARRITO ================= */

function addToCart(id){
const product = products.find(p=>p.id===id);
cart.push(product);
updateCart();
}

function updateCart(){
document.getElementById("cart-count").innerText = cart.length;
const cartItems = document.getElementById("cart-items");
cartItems.innerHTML="";
let total=0;

cart.forEach(item=>{
total+=item.price;
cartItems.innerHTML += `<p>${item.name} - $${item.price}</p>`;
});

document.getElementById("cart-total").innerText=total;
}

function toggleCart(){
document.getElementById("cart-modal").classList.toggle("active");
}

/* ================= CHATBOT INTELIGENTE ================= */

function sendMessage(){

let input = document.getElementById("user-input").value.trim();
if(input==="") return;

let lowerInput = input.toLowerCase();
let chatBox = document.getElementById("chat-box");

chatBox.innerHTML += `<p><strong>Tú:</strong> ${input}</p>`;

let response = "";

function calcularTotal(){
return cart.reduce((sum,item)=>sum+item.price,0);
}

function buscarProducto(){
return products.find(p => lowerInput.includes(p.name.toLowerCase()));
}

if(lowerInput.match(/tipo|qué venden|productos/)){
response="Ofrecemos ropa elegante para eventos formales y semi-formales.";
}
else if(lowerInput.match(/hombre|mujer|unisex/)){
response="Tenemos colecciones para hombre y mujer.";
}
else if(lowerInput.match(/talla|medidas/)){
response="Trabajamos desde XS hasta XXL.";
}
else if(lowerInput.match(/envío|entrega/)){
response="Enviamos a todo el país en 2 a 5 días hábiles.";
}
else if(lowerInput.includes("carrito")){
response=`Tienes ${cart.length} productos con un total de $${calcularTotal()}.`;
}
else if(buscarProducto()){
let producto = buscarProducto();
response=`${producto.name} cuesta $${producto.price}. Es ideal para eventos elegantes.`;
}
else{
response = generarRespuestaInteligente(lowerInput);
}

setTimeout(()=>{
chatBox.innerHTML += `<p><strong>Asistente:</strong> ${response}</p>`;
chatBox.scrollTop = chatBox.scrollHeight;
},400);

document.getElementById("user-input").value="";
}

function generarRespuestaInteligente(texto){

if(texto.includes("hola")) return "¡Hola! 👋 Estoy aquí para ayudarte.";
if(texto.includes("gracias")) return "Es un placer ayudarte 😊";
if(texto.includes("mejor")) return "Nuestro Vestido de Gala y Traje Ejecutivo son los más exclusivos.";
if(texto.includes("barato")){
let barato = products.reduce((min,p)=> p.price < min.price ? p : min);
return `La opción más accesible es ${barato.name} por $${barato.price}.`;
}

return "Puedo ayudarte con productos, tallas, envíos o recomendaciones de estilo.";
}

</script>

</body>
</html>
