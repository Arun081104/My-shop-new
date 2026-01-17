<!DOCTYPE html>
<html>
<head>
<title>My Bike Shop</title>
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<style>
body{font-family:Arial;background:#f2f2f2;margin:0}
header{background:#1e88e5;color:white;padding:12px;text-align:center}
.topbar,.admin{display:flex;gap:8px;padding:10px;background:#fff}
.topbar input,.topbar select,.admin input,.admin select{flex:1;padding:8px;border-radius:6px;border:1px solid #ccc}
.products{display:grid;grid-template-columns:repeat(auto-fit,minmax(170px,1fr));gap:10px;padding:10px}
.card{background:white;border-radius:10px;padding:8px;text-align:center;box-shadow:0 2px 5px rgba(0,0,0,.2)}
.card img{width:100%;border-radius:8px;height:150px;object-fit:cover}
button{background:green;color:white;border:none;padding:9px;border-radius:5px;width:100%}
.cart{position:fixed;bottom:0;width:100%;background:white;padding:10px}
.badge{background:red;color:#fff;border-radius:50%;padding:2px 7px;font-size:12px}
.admin-panel{display:none;padding:10px;background:#fff}
</style>
</head>
<body>

<header>
<h2>🛍 My Bike Shop</h2>
</header>

<div class="topbar">
<input id="search" placeholder="Search product..." onkeyup="renderProducts()">
<select id="category" onchange="renderProducts()">
<option value="all">All</option>
<option value="cover">Bike Covers</option>
<option value="helmet">Helmets</option>
<option value="accessory">Accessories</option>
</select>
<button onclick="showAdmin()">Admin</button>
</div>

<!-- ADMIN PANEL -->
<div class="admin-panel" id="adminPanel">
<h3>🔐 Admin Login</h3>
<input id="adminPass" placeholder="Enter Password">
<button onclick="loginAdmin()">Login</button>

<div id="adminArea" style="display:none;">
<h3>➕ Add Product</h3>
<input id="pname" placeholder="Product Name">
<input id="pprice" placeholder="Price">
<input id="pimg" placeholder="Image URL">
<select id="pcat">
<option value="cover">Cover</option>
<option value="helmet">Helmet</option>
<option value="accessory">Accessory</option>
</select>
<button onclick="addProduct()">Add Product</button>
</div>
</div>

<div class="products" id="productList"></div>

<div class="cart">
<b>Total ₹<span id="total">0</span></b>
<span class="badge" id="count">0</span>
<button onclick="orderNow()">Order on WhatsApp</button>
</div>

<script>
const ADMIN_PASSWORD = "1234";   // 👉 apna password yaha change karo
const WHATSAPP_NUMBER = "917042173665";

let products = JSON.parse(localStorage.getItem("products")) || [
 {id:1,name:'Bike Cover',price:499,cat:'cover',img:'https://i.imgur.com/8Km9tLL.jpg'}
];

let total = 0;
let items = [];

function saveProducts(){
 localStorage.setItem("products", JSON.stringify(products));
}

function renderProducts(){
 const list = document.getElementById('productList');
 list.innerHTML = '';
 const search = document.getElementById('search').value.toLowerCase();
 const category = document.getElementById('category').value;

 products.filter(p =>
   (category==='all' || p.cat===category) &&
   p.name.toLowerCase().includes(search)
 ).forEach(p =>{
   list.innerHTML += `
   <div class="card">
     <img src="${p.img}">
     <h3>${p.name}</h3>
     <p>₹${p.price}</p>
     <button onclick="addToCart('${p.name}',${p.price})">Add</button>
   </div>`;
 });
}

renderProducts();

function addToCart(name, price){
 total += price;
 items.push(name);
 document.getElementById('total').innerText = total;
 document.getElementById('count').innerText = items.length;
}

function orderNow(){
 if(items.length==0){alert("Cart empty"); return;}
 let msg = `Order: ${items.join(', ')} Total ₹${total}`;
 window.open(`https://wa.me/${WHATSAPP_NUMBER}?text=${msg}`);
}

// ADMIN FUNCTIONS
function showAdmin(){
 document.getElementById("adminPanel").style.display="block";
}

function loginAdmin(){
 let pass = document.getElementById("adminPass").value;
 if(pass === ADMIN_PASSWORD){
   document.getElementById("adminArea").style.display="block";
   alert("Admin Login Success ✅");
 } else {
   alert("Wrong Password ❌");
 }
}

function addProduct(){
 let name = pname.value;
 let price = pprice.value;
 let img = pimg.value;
 let cat = pcat.value;

 if(!name || !price || !img){
   alert("Fill all fields");
   return;
 }

 products.push({
   id: Date.now(),
   name:name,
   price:Number(price),
   cat:cat,
   img:img
 });

 saveProducts();
 renderProducts();
 alert("Product Added ✅");

 pname.value="";
 pprice.value="";
 pimg.value="";
}
</script>

</body>
</html>
