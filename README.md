# marketgo-diyor-2026

<!DOCTYPE html>
<html lang="uz">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>MarketGo — Onlayn do‘kon</title>
<style>
*{box-sizing:border-box;margin:0;padding:0}
body{font-family:Arial,sans-serif;background:#f5f6fa;color:#222}
header{background:#6c3df4;color:white;padding:18px 6%;display:flex;align-items:center;justify-content:space-between;gap:15px;flex-wrap:wrap}
.logo{font-size:27px;font-weight:bold}
.search{flex:1;max-width:500px;padding:13px;border:0;border-radius:10px;font-size:15px}
button{cursor:pointer;border:0;border-radius:9px;padding:11px 16px;font-weight:bold}
header button{background:white;color:#6c3df4}
main{max-width:1200px;margin:auto;padding:25px 15px}
.hero{background:linear-gradient(120deg,#6c3df4,#a47bff);color:white;padding:35px;border-radius:18px;margin-bottom:25px}
.hero h1{font-size:32px;margin-bottom:12px}
.categories,.products{display:grid;grid-template-columns:repeat(auto-fit,minmax(160px,1fr));gap:15px;margin:18px 0 30px}
.category,.product{background:white;border-radius:13px;padding:18px;text-align:center;box-shadow:0 3px 12px #0000000b}
.category{cursor:pointer}
.category:hover,.product:hover{transform:translateY(-3px)}
.product .emoji{font-size:55px;margin:12px}
.product h3{font-size:16px;margin:10px 0}
.price{color:#6c3df4;font-size:19px;font-weight:bold;margin:12px}
.product button,.checkout{background:#6c3df4;color:white}
.section-title{margin:25px 0 12px}
.cart{background:white;padding:20px;border-radius:14px;margin-top:30px}
.cart-item{display:flex;justify-content:space-between;gap:10px;padding:12px 0;border-bottom:1px solid #eee;flex-wrap:wrap}
input,textarea,select{width:100%;padding:13px;margin:7px 0;border:1px solid #ddd;border-radius:8px}
footer{text-align:center;padding:30px;color:#777}
#notice{padding:12px;background:#e9e2ff;color:#5424c7;border-radius:9px;margin:12px 0;display:none}
</style>
</head>
<body>
<header>
<div class="logo">🛍 MarketGo</div>
<input class="search" id="search" placeholder="Mahsulot qidirish..." oninput="render()">
<button onclick="document.getElementById('cart').scrollIntoView()">🛒 Savatcha (<span id="count">0</span>)</button>
</header>

<main>
<section class="hero">
<h1>MarketGo bilan xarid qilish oson!</h1>
<p>Sevimli mahsulotlaringizni bir joyda toping.</p>
</section>

<h2 class="section-title">Kategoriyalar</h2>
<div class="categories">
<div class="category" onclick="setCategory('Elektronika')">📱<p>Elektronika</p></div>
<div class="category" onclick="setCategory('Kiyim')">👕<p>Kiyim</p></div>
<div class="category" onclick="setCategory('Uy uchun')">🏠<p>Uy uchun</p></div>
<div class="category" onclick="setCategory('Aksessuar')">⌚<p>Aksessuar</p></div>
<div class="category" onclick="setCategory('Barchasi')">🛍<p>Barchasi</p></div>
</div>

<h2 class="section-title">Mahsulotlar</h2>
<div id="products" class="products"></div>

<section class="cart" id="cart">
<h2>🛒 Savatcha</h2>
<div id="cartItems">Savatcha hozircha bo‘sh.</div>
<h3 id="total" class="price">Jami: 0 so‘m</h3>
<h2 class="section-title">Buyurtma berish</h2>
<form onsubmit="checkout(event)">
<input id="customer" placeholder="Ismingiz" required>
<input id="phone" type="tel" placeholder="Telefon raqamingiz" required>
<textarea id="address" placeholder="Yetkazib berish manzili" required></textarea>
<select id="payment">
<option value="Naqd pul">Naqd pul orqali</option>
<option value="Karta (demo)">Karta orqali (demo)</option>
</select>
<button class="checkout" type="submit">Buyurtmani tasdiqlash</button>
</form>
<div id="notice"></div>
</section>
</main>

<footer>© 2026 MarketGo. Barcha huquqlar himoyalangan.</footer>

<script>
const products=[
{id:1,name:"Smartfon",category:"Elektronika",price:2500000,emoji:"📱"},
{id:2,name:"Simsiz quloqchin",category:"Elektronika",price:180000,emoji:"🎧"},
{id:3,name:"Noutbuk",category:"Elektronika",price:6500000,emoji:"💻"},
{id:4,name:"Aqlli soat",category:"Aksessuar",price:350000,emoji:"⌚"},
{id:5,name:"Futbolka",category:"Kiyim",price:90000,emoji:"👕"},
{id:6,name:"Krossovka",category:"Kiyim",price:320000,emoji:"👟"},
{id:7,name:"Stol lampasi",category:"Uy uchun",price:150000,emoji:"💡"},
{id:8,name:"Ryukzak",category:"Aksessuar",price:210000,emoji:"🎒"},
{id:9,name:"Klaviatura",category:"Elektronika",price:120000,emoji:"⌨️"},
{id:10,name:"Kreslo",category:"Uy uchun",price:850000,emoji:"🪑"},
{id:11,name:"Ko‘zoynak",category:"Aksessuar",price:100000,emoji:"🕶️"},
{id:12,name:"Kurtka",category:"Kiyim",price:450000,emoji:"🧥"}
];
let cart=JSON.parse(localStorage.getItem("marketgo_cart")||"[]");
let category="Barchasi";

function money(n){return n.toLocaleString("uz-UZ")+" so‘m"}

function setCategory(c){category=c;render()}

function render(){
let q=document.getElementById("search").value.toLowerCase();
let list=products.filter(p=>
(category==="Barchasi"||p.category===category)&&p.name.toLowerCase().includes(q)
);
document.getElementById("products").innerHTML=list.map(p=>`
<div class="product">
<div class="emoji">${p.emoji}</div>
<h3>${p.name}</h3>
<p>${p.category}</p>
<div class="price">${money(p.price)}</div>
<button onclick="add(${p.id})">Savatchaga qo‘shish</button>
</div>`).join("")||"Mahsulot topilmadi.";
updateCart();
}

function add(id){
let item=cart.find(x=>x.id===id);
if(item)item.qty++;
else cart.push({id,qty:1});
save();
}

function removeItem(id){
cart=cart.filter(x=>x.id!==id);
save();
}

function changeQty(id,n){
let item=cart.find(x=>x.id===id);
if(!item)return;
item.qty+=n;
if(item.qty<=0)removeItem(id);
else save();
}

function save(){
localStorage.setItem("marketgo_cart",JSON.stringify(cart));
updateCart();
}

function updateCart(){
let count=cart.reduce((s,x)=>s+x.qty,0);
document.getElementById("count").textContent=count;
let total=0;
document.getElementById("cartItems").innerHTML=cart.length?cart.map(x=>{
let p=products.find(y=>y.id===x.id);
if(!p)return "";
total+=p.price*x.qty;
return `<div class="cart-item">
<span>${p.emoji} ${p.name} — ${money(p.price)}</span>
<span>
<button type="button" onclick="changeQty(${p.id},-1)">−</button>
${x.qty}
<button type="button" onclick="changeQty(${p.id},1)">+</button>
<button type="button" onclick="removeItem(${p.id})">🗑</button>
</span></div>`;
}).join(""):"Savatcha hozircha bo‘sh.";
document.getElementById("total").textContent="Jami: "+money(total);
}

function checkout(e){
e.preventDefault();
if(!cart.length){alert("Savatcha bo‘sh!");return;}
let order={
name:document.getElementById("customer").value,
phone:document.getElementById("phone").value,
address:document.getElementById("address").value,
payment:document.getElementById("payment").value,
items:cart,
date:new Date().toISOString()
};
let orders=JSON.parse(localStorage.getItem("marketgo_orders")||"[]");
orders.push(order);
localStorage.setItem("marketgo_orders",JSON.stringify(orders));
cart=[];
save();
document.getElementById("notice").style.display="block";
document.getElementById("notice").textContent=
"✅ Buyurtmangiz demo tarzida qabul qilindi! Bu haqiqiy to‘lov yoki serverga yuborilgan buyurtma emas.";
e.target.reset();
}

render();
</script>
</body>
</html>
